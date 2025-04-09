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


###  <p align="center"> TASK 1 </p>


#### Create following tables in SQL Schema with appropriate class and write the unit test case for the Project Management application.

###  <p align="center">Schema Design</p>


### Employee

• id (Primary Key)

• name

• Designation

• Gender

• Salary

• Project_id((Foreign Key referencing Projects table)

#### code:
```
CREATE TABLE Employee (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    designation VARCHAR(100),
    gender ENUM('Male', 'Female', 'Other') NOT NULL,
    salary DECIMAL(10,2),
    project_id INT,
    FOREIGN KEY (project_id) REFERENCES Project(id) 
        ON DELETE SET NULL 
        ON UPDATE CASCADE
);

```

<p align="center">
  <img src="https://github.com/user-attachments/assets/1e4f9e8c-08da-40fc-b119-7b70df7ad196" width="45%" />
</p>


 #### Project
 
• Id (Primary Key)

• ProjectName

• Description

• Start date

• Status(started/dev/build/test/deployed)

#### code:
```
CREATE TABLE Project (
    id INT AUTO_INCREMENT PRIMARY KEY,
    project_name VARCHAR(100) NOT NULL,
    description TEXT,
    start_date DATE,
    status ENUM('started', 'dev', 'build', 'test', 'deployed') NOT NULL
);
```


#### Task
• task_id (Primary Key)

• task_name

• project_id Foreign Key referencing Projects table)

• employee id Foreign Key referencing Employee table)

• Status (Assigned, started, completed)

#### code:

```
CREATE TABLE Task (
    task_id INT AUTO_INCREMENT PRIMARY KEY,
    task_name VARCHAR(100) NOT NULL,
    project_id INT NOT NULL,
    employee_id INT NOT NULL,
    status ENUM('Assigned', 'Started', 'Completed') NOT NULL,
    FOREIGN KEY (project_id) REFERENCES Project(id) 
        ON DELETE CASCADE 
        ON UPDATE CASCADE,
    FOREIGN KEY (employee_id) REFERENCES Employee(id) 
        ON DELETE CASCADE 
        ON UPDATE CASCADE
);
```

<p align="center">
  <img src="https://github.com/user-attachments/assets/f36b3fb9-d7e9-497d-81df-3353996918de" width="45%" />
</p>


 #### Explanation
• The Employee table stores information about details of employees

• The Project table contains details project and optional description.

• The Task table stores details of task and data about which project it comes under and employees assigned with that taks along with status .


