CLASS lhc_Booking DEFINITION INHERITING FROM cl_abap_behavior_handler.
  PRIVATE SECTION.

    METHODS delete FOR MODIFY
      IMPORTING keys FOR DELETE Booking.

    METHODS update FOR MODIFY
      IMPORTING entities FOR UPDATE Booking.

    METHODS read FOR READ
      IMPORTING keys FOR READ Booking RESULT result.

    METHODS rba_Travel FOR READ
      IMPORTING
        keys_rba FOR READ Booking\_Travel FULL result_requested RESULT result LINK association_links.

ENDCLASS.

CLASS lhc_Booking IMPLEMENTATION.

  METHOD delete.

    DATA messages TYPE /dmo/t_message.

    LOOP AT keys ASSIGNING FIELD-SYMBOL(<key>).

      CALL FUNCTION '/DMO/FLIGHT_TRAVEL_UPDATE'
        EXPORTING
          is_travel   = VALUE /dmo/s_travel_in( travel_id = <key>-travelid )
          is_travelx  = VALUE /dmo/s_travel_inx( travel_id = <key>-travelid )
          it_booking  = VALUE /dmo/t_booking_in( ( booking_id = <key>-bookingid ) )
          it_bookingx = VALUE /dmo/t_booking_inx( ( booking_id  = <key>-bookingid
                                                    action_code = /dmo/if_flight_legacy=>action_code-delete ) )
        IMPORTING
          et_messages = messages.

      IF messages IS INITIAL.

        APPEND VALUE #( travelid = <key>-travelid
                       bookingid = <key>-bookingid ) TO mapped-booking.

      ELSE.

        "fill failed return structure for the framework
        APPEND VALUE #( travelid = <key>-travelid
                        bookingid = <key>-bookingid ) TO failed-booking.

        LOOP AT messages INTO DATA(message).
          "fill reported structure to be displayed on the UI
          APPEND VALUE #( travelid = <key>-travelid
                          bookingid = <key>-bookingid
                  %msg = new_message( id = message-msgid
                                                number = message-msgno
                                                v1 = message-msgv1
                                                v2 = message-msgv2
                                                v3 = message-msgv3
                                                v4 = message-msgv4
                                                severity = CONV #( message-msgty ) )
         ) TO reported-booking.
        ENDLOOP.



      ENDIF.

    ENDLOOP.

  ENDMETHOD.

  METHOD update.

    DATA messages TYPE /dmo/t_message.
    DATA legacy_entity_in  TYPE /dmo/booking.
    DATA legacy_entity_x TYPE /dmo/s_booking_inx.


    LOOP AT entities ASSIGNING FIELD-SYMBOL(<entity>).

      legacy_entity_in = CORRESPONDING #( <entity> MAPPING FROM ENTITY ).

      legacy_entity_x-booking_id = <entity>-BookingID.
      legacy_entity_x-_intx      = CORRESPONDING zsrap_booking_x_####( <entity> MAPPING FROM ENTITY ).
      legacy_entity_x-action_code = /dmo/if_flight_legacy=>action_code-update.

      CALL FUNCTION '/DMO/FLIGHT_TRAVEL_UPDATE'
        EXPORTING
          is_travel   = VALUE /dmo/s_travel_in( travel_id = <entity>-travelid )
          is_travelx  = VALUE /dmo/s_travel_inx( travel_id = <entity>-travelid )
          it_booking  = VALUE /dmo/t_booking_in( ( CORRESPONDING #( legacy_entity_in ) ) )
          it_bookingx = VALUE /dmo/t_booking_inx( ( legacy_entity_x ) )
        IMPORTING
          et_messages = messages.



      IF messages IS INITIAL.

        APPEND VALUE #( travelid = <entity>-travelid
                       bookingid = legacy_entity_in-booking_id ) TO mapped-booking.

      ELSE.

        "fill failed return structure for the framework
        APPEND VALUE #( travelid = <entity>-travelid
                        bookingid = legacy_entity_in-booking_id ) TO failed-booking.
        "fill reported structure to be displayed on the UI

        LOOP AT messages INTO DATA(message).
          "fill reported structure to be displayed on the UI
          APPEND VALUE #( travelid = <entity>-travelid
                          bookingid = legacy_entity_in-booking_id
                  %msg = new_message( id = message-msgid
                                                number = message-msgno
                                                v1 = message-msgv1
                                                v2 = message-msgv2
                                                v3 = message-msgv3
                                                v4 = message-msgv4
                                                severity = CONV #( message-msgty ) )
         ) TO reported-booking.
        ENDLOOP.

      ENDIF.

    ENDLOOP.


  ENDMETHOD.

  METHOD read.

    DATA: legacy_parent_entity_out TYPE /dmo/travel,
          legacy_entities_out      TYPE /dmo/t_booking,
          messages                 TYPE /dmo/t_message.

    "Only one function call for each requested travelid
    LOOP AT keys ASSIGNING FIELD-SYMBOL(<key_parent>)
                            GROUP BY <key_parent>-travelid .

      CALL FUNCTION '/DMO/FLIGHT_TRAVEL_READ'
        EXPORTING
          iv_travel_id = <key_parent>-travelid
        IMPORTING
          es_travel    = legacy_parent_entity_out
          et_booking   = legacy_entities_out
          et_messages  = messages.

      IF messages IS INITIAL.
        "For each travelID find the requested bookings
        LOOP AT GROUP <key_parent> ASSIGNING FIELD-SYMBOL(<key>)
                                       GROUP BY <key>-%key.

          READ TABLE legacy_entities_out INTO DATA(legacy_entity_out) WITH KEY travel_id  = <key>-%key-TravelID
                                                                   booking_id = <key>-%key-BookingID .
          "if read was successfull
          "fill result parameter with flagged fields
          IF sy-subrc = 0.

            INSERT CORRESPONDING #( legacy_entity_out MAPPING TO ENTITY ) INTO TABLE result.

          ELSE.
            "BookingID not found
            INSERT
              VALUE #( travelid    = <key>-TravelID
                       bookingid   = <key>-BookingID
                       %fail-cause = if_abap_behv=>cause-not_found )
              INTO TABLE failed-booking.
          ENDIF.
        ENDLOOP.
      ELSE.
        "TravelID not found or other fail cause
        LOOP AT GROUP <key_parent> ASSIGNING <key>.
          failed-booking = VALUE #(  BASE failed-booking
                                     FOR msg IN messages ( %key-TravelID    = <key>-TravelID
                                                             %key-BookingID   = <key>-BookingID
                                                             %fail-cause      = COND #( WHEN msg-msgty = 'E' AND ( msg-msgno = '016' OR msg-msgno = '009' )
                                                                                        THEN if_abap_behv=>cause-not_found
                                                                                        ELSE if_abap_behv=>cause-unspecific ) ) ).
        ENDLOOP.

      ENDIF.

    ENDLOOP.

  ENDMETHOD.

  METHOD rba_Travel.

    DATA: ls_travel_out  TYPE /dmo/travel,
          lt_booking_out TYPE /dmo/t_booking,
          ls_travel      LIKE LINE OF result,
          lt_message     TYPE /dmo/t_message.

    "result  type table for read result /dmo/i_travel_u\\booking\_travel

    "Only one function call for each requested travelid
    LOOP AT keys_rba ASSIGNING FIELD-SYMBOL(<fs_travel>)
                                 GROUP BY <fs_travel>-TravelID.

      CALL FUNCTION '/DMO/FLIGHT_TRAVEL_READ'
        EXPORTING
          iv_travel_id = <fs_travel>-%key-TravelID
        IMPORTING
          es_travel    = ls_travel_out
          et_messages  = lt_message.

      IF lt_message IS INITIAL.

        LOOP AT GROUP <fs_travel> ASSIGNING FIELD-SYMBOL(<fs_booking>).
          "fill link table with key fields
          INSERT VALUE #( source-%key = <fs_booking>-%key
                          target-%key = ls_travel_out-travel_id )
           INTO TABLE association_links .

          IF  result_requested  = abap_true.
            "fill result parameter with flagged fields
            ls_travel = CORRESPONDING #(  ls_travel_out MAPPING TO ENTITY ).
            INSERT ls_travel INTO TABLE result.
          ENDIF.
        ENDLOOP.

      ELSE. "fill failed table in case of error
        failed-booking = VALUE #(  BASE failed-booking
                              FOR msg IN lt_message ( %key-TravelID    = <fs_travel>-%key-TravelID
                                                      %key-BookingID   = <fs_travel>-%key-BookingID
                                                      %fail-cause      = COND #( WHEN msg-msgty = 'E' AND ( msg-msgno = '016' OR msg-msgno = '009' )
                                                                                 THEN if_abap_behv=>cause-not_found
                                                                                ELSE if_abap_behv=>cause-unspecific ) ) ).
      ENDIF.

    ENDLOOP.

  ENDMETHOD.

ENDCLASS.
