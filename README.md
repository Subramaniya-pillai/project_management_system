#  <p align="center"> Project Management System </p>
 
####  The following Directory structure is  followed in the application.
#### entity/model
Create entity classes in this package. All entity class should not have any business logic.
####  dao
• Create Service Provider interface to showcase functionalities.

• Create the implementation class for the above interface with db interaction.
#### exception
• Create user defined exceptions in this package and handle exceptions whenever needed.
####  util
• Create a DBPropertyUtil class with a static function which takes property filename as parameter and returns connection string.

• Create a DBConnUtil class which holds static method which takes connection string as parameter file and returns connection object(Use method defined in DBPropertyUtil class to get the connection String ).
####  main

• Create a class MainModule and demonstrate the functionalities in a menu
driven application.

#### Key Functionalities:

1. Project Management: manager can add, view, update, and delete projects
2. Project allocations: employees can be alloted to various projects
3. Taks addition: Tasks can be added to each project with allocation date and deadline date
4. Reports Generation: Users can generate reports for their expenses over specific time periods.
5. Database Connectivity: Data will be stored in a relational database to ensure persistence.
