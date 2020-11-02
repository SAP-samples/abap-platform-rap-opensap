# HANDS-ON EXERCISE FOR WEEK 3 UNIT 4: UNDERSTANDING ENTITY MANIPULATION LANGUAGE (EML)

## Previous exercise:
[Week 3 Unit 3: Creating the Business Object Behavior Projection](/week3/unit3.md)

## Introduction
In the present hands-on exercise, you will get an introduction into the entity manipulation language (short: EML) that you will need when you for instance add determinations, validations or actions to your business object behavior definition. EML allows you to programmatically access directly BO instances.    
You can watch [week 3 unit 4: Understanding Entity Manipulation Language (EML)]( https://open.sap.com/courses/cp13/items/1PQYUmWLxhSJ6jovoMOScA ) on the openSAP platform.
    
> **Hints and Tips**    
> Speed up the typing by making use of the Code Completion feature (shortcut *Ctrl+Space*) and the prepared code snippets provided. 
> You can easily open an object with the shortcut *Ctrl+Shift+A*, format your source code using the Pretty Printer feature *Shift+F1* and toggle the fullscreen of the editor using the shortcut *Ctrl+M*.   
>
> A great overview of ADT shortcuts can be found here: [Useful ADT Shortcuts](https://blogs.sap.com/2013/11/21/useful-keyboard-shortcuts-for-abap-in-eclipse/)
>
> Please note that the placeholder **`####`** used in object names in the exercise description must be replaced with the suffix of your choice during the exercises. The suffix can contain a maximum of 4 characters (numbers and letters).
> The screenshots in this document have been taken with the suffix `1234` and system `D20`. Your system id will be `TRL`.

> Please note that the ADT dialogs and views may change in the future due to software updates.

Follow the instructions below.

## Step 1. Create the Class
In this step you will create the class **`ZCL_RAP_EML_####`**, where **`####`** is your chosen suffix, to play around and learn the EML syntax.

1.	Right-click on your package **`ZRAP_TRAVEL_####`** and choose _**New > ABAP Class**_

 
![Create ABAP Class](images/w3u4_01_01.png)



2.	Maintain **`ZCL_RAP_EML_####`** as _Name_ and a meaningful _Description_ (.e.g. **`EML Playground`**) in the _New ABAP Class_ wizard.        
    
    Add the interface **`if_oo_adt_classrun`** to the _Interfaces_ section by choosing the _**Add**_, filtering the entries appropriately, choosing the correct entry from the list and choosing **OK**.    
    
    Choose **`Next >`** to continue.
 
    ![Create ABAP Class](images/w3u4_01_02.png)
    
3.	Assign a transport request and choose **`Finish`**.  

    ![Create ABAP Class](images/w3u4_01_03.png)
    
    The ABAP class skeleton is generated and displayed in the appropriate editor.  
     
    ![Create ABAP Class](images/w3u4_01_04.png)
    
4.	Use the ADT Quick Fix (**Ctrl+1**) to add the implementation for the **`main`** method to the class implementation section.  
     
    ![Create ABAP Class](images/w3u4_01_05.png)
    
5. Save ![save icon](images/adt_save.png) the class.
    
## Step 2. Perform different READ operations
The first EML example performs a READ operation.   

1.	For this insert the code snippet provided below into the **`main`** method.    
    Do not forget to replace the placeholder **`####`** with your chosen suffix.     
    <pre>    
        " step 1 - READ
        READ ENTITIES OF ZI_RAP_Travel_####
          ENTITY travel
            FROM VALUE #( ( TravelUUID = '&ltyour uuid&gt' ) )
          RESULT DATA(travels).

        out->write( travels ).
    </pre>

    Your source code should look as follows:  
    
    ![EML READ operation](images/w3u4_02_01.png)

    **Short explanation:**   
    -	The **`READ ENTITIES`** statement followed by our _travel_ business object is used for the purpose.  
    -	The entity for which we want to perform the operation is specified. In our case the travel entity.  
    -	All EML statements are mass-enabled by default, therefore, the list of keys for which the **`READ`** operation should be performed needs to be specified.  
    -	The result of the read is put into the table `travels` declared inline.  
    -	Finally, the console is used to show the content of the result table.  
          
            
2.	Before you can proceed, you need a travel UUID.   
    Open the _Data Preview_ of your Travel table **`ZRAP_ATRAV_####`** – where `####` is your chosen suffix – and pick a UUID that you want to use in this example.   
    Double-click the value of the **`TRAVEL_UUID`** column and copy it.  
         
    Remember the associated **`TRAVEL_ID`**, so that you find the record back, later on.  
     
    ![EML READ operation](images/w3u4_02_02.png)
            
3.	Go back to the EML Playground class and replace the placeholder **`<your uuid>`** with the copied travel UUID.  
         
    ![EML READ operation](images/w3u4_02_03.png)
         
        
4.	Save ![save icon](images/adt_save.png), activate ![activate icon](images/adt_activate.png), and run (**`F9`**) the class as a _console application_.  
          
    The **`READ`** operation is performed, and the result displayed in the console.    
    Only the key field is filled. The reason for that, is the missing specification of the fields that should be read. The keys are provided by default.  
         
    ![EML READ operation](images/w3u4_02_04.png)
    
5.	Specify that the value of the fields **`AgencyID`** and **`CustomerID`** should be read. For that, comment out the current implementation and insert the code snippet provided below.   
    Do not forget to replace the placeholders **`####`** and **`<your uuid>`** with your chosen suffix and the copied travel UUID respectively.  
    
    <pre>
        " step 2 - READ with Fields
        READ ENTITIES OF ZI_RAP_Travel_####
          ENTITY travel
            FIELDS ( AgencyID CustomerID )
          WITH VALUE #( ( TravelUUID = '<your uuid>' ) )
          RESULT DATA(travels).
        out->write( travels ).
    </pre>

    Your source code should look as follows:  
         
    ![EML READ operation](images/w3u4_02_05.png)
     
6.	Save ![save icon](images/adt_save.png), activate ![activate icon](images/adt_activate.png), and run (**`F9`**) the class as a _console application_.  

    The **`READ`** operation is performed, and the result displayed in the console.   
    The **`AgencyID`** and **`CustomerID`** are now also provided in the result.  
          
    ![EML READ operation](images/w3u4_02_06.png)
         
7.	It’s also possible to read all fields of the entity by using the **`ALL FIELDS WITH`** addition.    
    For that, comment out the current implementation and insert the code snippet provided below.      
    Do not forget to replace the placeholders **`####`** and **`<your uuid>`** with your chosen suffix and the copied travel UUID respectively.   
     
    <pre>
        " step 3 - READ with All Fields
        READ ENTITIES OF ZI_RAP_Travel_####
          ENTITY travel
            ALL FIELDS 
          WITH VALUE #( ( TravelUUID = '<your uuid>' ) )
          RESULT DATA(travels).

        out->write( travels ).
    </pre>

    Your source code should look as follows:      
         
    ![EML READ operation](images/w3u4_02_07.png)
         
8.	Save ![save icon](images/adt_save.png), activate ![activate icon](images/adt_activate.png), and run (**`F9`**) the class as a _console application_.  
         
    The **`READ`** operation is performed, and the result displayed in the console.     
    All fields of the chosen travel entity are now returned.   
          
    ![EML READ operation](images/w3u4_02_08.png)
          
    >**Note**:  You can clear the console by right-clicking in the editor and choosing _**clear**_ from the context menu.   
         
9.	Another option is to perform a _read-by-association_, by following the association defined in the behavior definition.  
    For that, comment out the current implementation and insert the code snippet provided below.   
    Do not forget to replace the placeholders **`####`** and **`<your uuid>`** with your chosen suffix and the copied travel UUID respectively.  
    
    <pre>
        " step 4 - READ By Association
        READ ENTITIES OF ZI_RAP_Travel_####
          ENTITY travel BY \_Booking
            ALL FIELDS WITH VALUE #( ( TravelUUID = '<your uuid>' ) )
          RESULT DATA(bookings).

        out->write( bookings ).
    </pre>

    Your source code should look as follows:
    
    ![EML READ operation](images/w3u4_02_09.png)
         
10.	Save ![save icon](images/adt_save.png), activate ![activate icon](images/adt_activate.png), and run (**`F9`**) the class as a _console application_.  
          
    The **`READ`** operation is performed, and the result displayed in the console.     
    As a result, all booking entities associated to this Travel are retrieved with all fields.   
          
    ![EML READ operation](images/w3u4_02_10.jpg)
    
## Step 3. Failure handling in READ operations
When performing EML read operations, you should not only consider the result table but also take the **`failed`** and the **`reported`** tables into account.  **`Failed`** is used to convey unsuccessful operations. **`Reported`** is optionally used to provide related `T100` messages.    
You will now try to read a travel UUID that does not exist.      
        
1. Comment out the current implementation and insert the code snippet provided below.    
    Do not forget to replace the placeholder **`####`** with your chosen suffix. Keep the specified **`TRAVELUUID`**.    
    
    <pre>
        " step 5 - Unsuccessful READ
        READ ENTITIES OF ZI_RAP_Travel_####
          ENTITY travel
            ALL FIELDS WITH VALUE #( ( TravelUUID = '11111111111111111111111111111111' ) )
          RESULT DATA(travels)
          FAILED DATA(failed)
          REPORTED DATA(reported).

        out->write( travels ).
        out->write( failed ).    " complex structures not supported by the console output
        out->write( reported ).  " complex structures not supported by the console output
    </pre>

    Your source code should look as follows:
         
    ![EML READ operation - Failure](images/w3u4_03_01.png)

2. Save ![save icon](images/adt_save.png), activate ![activate icon](images/adt_activate.png), and run (**`F9`**) the class as a _console application_.

    The **`READ`** operation is performed, and the result displayed in the console.     
    The result table is empty. The **`failed`** and **`reported`** tables are nested tables which are not supported by the console output.   
         
    ![EML READ operation - Failure](images/w3u4_03_02.png)
          
3. Therefore, set a breakpoint to the **`READ ENTITIES`** statement line, run (**`F9`**) the class as application console and check the content of the tables in the _ABAP Debugger_.   
    **`Failed`** contains an entry for the unsuccessful _read_ with the fail cause **`NOT_FOUND`**. The **`reported`** table is empty as the fail cause sufficiently explains the reason.  
          
    ABAP Debugger:  
          
    ![EML READ operation - Failure](images/w3u4_03_04.png)
    
        
## Step 4. Modify an existing Travel Instance 
Now you will implement and perform some modifying operations.   
    
1. Perform a **`MODIFY`** operation updating the description of your travel record. For that, comment out the current implementation and insert the code snippet provided below.   
    Do not forget to replace the placeholders **`####`** and **`<your uuid>`** with your chosen suffix and the copied travel UUID respectively.   
        
    <pre>
        " step 6 - MODIFY Update
        MODIFY ENTITIES OF ZI_RAP_Travel_####
          ENTITY travel
            UPDATE
              SET FIELDS WITH VALUE
                #( ( TravelUUID  = '<your uuid>'
                     Description = 'I like RAP@openSAP' ) )

         FAILED DATA(failed)
         REPORTED DATA(reported).

        out->write( 'Update done' ).
    </pre>
    
    Your source code should look as follows:  
         
    ![EML MODIFY operation](images/w3u4_04_01.png)
         
2. Save ![save icon](images/adt_save.png), activate ![activate icon](images/adt_activate.png), and run (**`F9`**) the class as a _console application_.   
         
    The **`MODIFY`** operation has been performed.     
    Check the updated _Description_ either in the _Data Preview_ of your _travel_ table **`ZRAP_ATRAV_####`** (**`F8`**) or in your _Travel_ app. As you can see – **`the description has not changed`**. It’s still the old value. The reason for this is the **`missing commit`** entities statement that we will add now.  
    
    _Data Preview_:      
         
    ![EML MODIFY operation](images/w3u4_04_02.png)

3. Add the **`COMMIT ENTITIES`** statement.   
    For that, copy and insert the code snippet provided below after the **`MODIFY`** as shown on the screenshot below.  
    Do not forget to replace the placeholder *`####`*** with your chosen suffix.  
    
    <pre>
        " step 6b - Commit Entities
        COMMIT ENTITIES
          RESPONSE OF ZI_RAP_Travel_####
          FAILED     DATA(failed_commit)
          REPORTED   DATA(reported_commit).
    </pre>
    
    Your source code should look as follows:   
           
    ![EML MODIFY operation](images/w3u4_04_03.png)
    
5.	Save ![save icon](images/adt_save.png), activate ![activate icon](images/adt_activate.png), and run (**`F9`**) the class as a _console application_.  
    
6.	Check the result either in the _Data Preview_ of your _travel_ table **`ZRAP_ATRAV_####`** (**`F8`**) or in your _Travel_ app.   
    You can filter the entries by the _travel_ uuid (**`TRAVEL_UUID`** alias **`TRAVELUUID`**) or the corresponding _travel_ ID (**`TRAVEL_ID`**).   
    
    The _Description_ should have been modified now.   
    
    _Data Preview_:      
         
    ![EML MODIFY operation](images/w3u4_04_04.png)
    
## Step 5. Create a new Travel instance 
The creation of new entities is performed using the **`MODIFY`** statement with the **`CREATE`** clause. For operations that create instances, a **`mapped`** table is returned which maps the created instance to the provided content ID.    
    
1.	You will now create a new _travel_ instance. For that, comment out the current implementation and insert the code snippet provided below.   
    Do not forget to replace the placeholder **`####`** with your chosen suffix.  
         
    <pre>
        " step 7 - MODIFY Create
        MODIFY ENTITIES OF ZI_RAP_Travel_####
          ENTITY travel
            CREATE
              SET FIELDS WITH VALUE
                #( ( %cid        = 'MyContentID_1'
                     AgencyID    = '70012'
                     CustomerID  = '14'
                     BeginDate   = cl_abap_context_info=>get_system_date( )
                     EndDate     = cl_abap_context_info=>get_system_date( ) + 10
                     Description = 'I like RAP@openSAP' ) )

         MAPPED DATA(mapped)
         FAILED DATA(failed)
         REPORTED DATA(reported).

        out->write( mapped-travel ).

        COMMIT ENTITIES
          RESPONSE OF ZI_RAP_Travel_####
          FAILED     DATA(failed_commit)
          REPORTED   DATA(reported_commit).

        out->write( 'Create done' ).
    </pre>
    
    Your source code should look as follows:  
         
    ![EML MODIFY CREATE operation](images/w3u4_05_01.png)
         
2.	Save ![save icon](images/adt_save.png), activate ![activate icon](images/adt_activate.png), and run (**`F9`**) the class as a _console application_.  
          
    ![EML MODIFY CREATE operation](images/w3u4_05_02.png)
    
3.	Check the result either in the _Data Preview_ of your _travel_ table **`ZRAP_ATRAV_####`** (**`F8`**) or in your _Travel_ app.  
    You can filter the entries by the _travel_ uuid (**`TRAVEL_UUID`** alias **`TRAVELUUID`**). You can use the _travel_ UUID written in the _console_ for that.  
         
    _Data Preview_:     
         
    ![EML MODIFY CREATE operation](images/w3u4_05_03.png)
    
## Step 6. Delete an existing Travel instance
Deletions are performed using the **`MODIFY ENTITIES`** statement with the **`DELETE`** clause.   
    
1.	You will now delete an existing _travel_ instance. For that, comment out the current implementation and insert the code snippet provided below.   
    Do not forget to replace the placeholders **`####`** and **`<your uuid>`** respectively with your chosen suffix and a copied travel UUID – e.g. the uuid of the travel instance you just created or from any other.   
         
    <pre>
        " step 8 - MODIFY Delete
        MODIFY ENTITIES OF ZI_RAP_Travel_####
          ENTITY travel
            DELETE FROM
              VALUE
                #( ( TravelUUID  = '<your uuid>' ) )

         FAILED DATA(failed)
         REPORTED DATA(reported).

        COMMIT ENTITIES
          RESPONSE OF ZI_RAP_Travel_####
          FAILED     DATA(failed_commit)
          REPORTED   DATA(reported_commit).

        out->write( 'Delete done' ).
    </pre>
    
    Your source code should look as follows:
         
    ![EML MODIFY DELETE operation](images/w3u4_06_01.png)

2.	Save ![save icon](images/adt_save.png), activate ![activate icon](images/adt_activate.png), and run (**`F9`**) the class as a _console application_.  
    
    ![EML MODIFY DELETE operation](images/w3u4_06_02.png)
         
3.	Check the result either in the _Data Preview_ of your _travel_ table **`ZRAP_ATRAV_###`** or in your _Travel_ app.    
    You can filter the entries by the _travel_ uuid (**`TRAVEL_UUID`** alias **`TRAVELUUID`**). Use the UUID written in the _console_.
         
    _Data Preview_:  
         
    ![EML MODIFY DELETE operation](images/w3u4_06_03.png)
         
## Summary
You have completed the exercise!   
In this unit, you have learned the basic syntax of the entity manipulation language (EML) and how it allows you to create, read, update and delete transactional data.  
    
## Solution
Find the source code of the created ABAP Class **[sources](/week3/sources)** folder:  
-	[W3U4_CLAS_ZCL_RAP_EML_####](/week3/sources/W3U4_CLAS_ZCL_RAP_EML.txt)     
    
Do not forget to replace all the occurrences of `####` with your chosen suffix in the copied source code.  

## Next exercise
[Week 3 Unit 5: Enhancing the Business Object Behavior with App-Specific Logic](unit5.md)
