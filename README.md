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

  ####   <p align="center"> Employee </p>


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


 ####   <p align="center"> Project </p>

 
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


 #### <p align="center"> Task </p>

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

### Create the model/entity classes corresponding to the schema within package entity with variables declared private, constructors(default and parametrized) and getters,setters )

####  Service Provider Interface/Abstract class:

_Keep the interfaces and implementation classes in package dao_

created a directory named as dao inside the project file 

<p align="center">
  <img src="https://github.com/user-attachments/assets/75e04e94-2bef-460c-9510-04ce6f3bf1c8" width="45%" />
</p>


####  Define IProjectRepository interface/abstract class with methods for adding/removing  products to/from the cart and placing orders. The following methods will interact with database.


#### 1. createEmployee()

parameter: Employee emp

return type: boolean

#### 2. createProject()

parameter: Project pj

return type: Boolean

#### 3. createTask()
Parameter: Project pj

Return type: Boolean

#### 5. assignProjectToEmployee()

Parameter: int projectId, int employeeId

Return type: Boolean

#### 5.AssigntaskInProjecttoEmployee()

Parameter: int taskid, int projectid, int employeeId

Return type: Boolean

#### 6. deleteEmployee()

parameter: userId

return type: boolean
   
#### 1. deleteProject()
         
parameter: projectId
         
return type: boolean
        
#### 2. getAllTasks() list all Taks in a project assigned to an employeee
        
parameter: empId,projectId
         
return type: list of expenes


python code:
```
import mysql.connector
from dao.i_project_repository import IProjectRepository
from util.db_conn_util import DBConnUtil

class ProjectRepositoryImpl(IProjectRepository):

    def __init__(self, config_path):
        self.conn = DBConnUtil.get_connection(config_path)
        self.cursor = self.conn.cursor()

    def createEmployee(self, emp):
        values = (
            emp.get_name(),
            emp.get_designation(),
            emp.get_gender(),  # This should be already 'Male' / 'Female' / 'Other'
            emp.get_salary(),
            emp.get_project_id()
        )

        print(" DEBUG - Values to Insert:", values)

        query = "INSERT INTO Employee (name, designation, gender, salary, project_id) VALUES (%s, %s, %s, %s, %s)"

        try:
            self.cursor.execute(query, values)
            self.conn.commit()
            return True
        except Exception as e:
            print(" ERROR:", e)
            return False

    def createProject(self, pj):
        query = "INSERT INTO Project (project_name, description, start_date, status) VALUES (%s, %s, %s, %s)"
        values = (pj.get_name(), pj.get_description(), pj.get_start_date(), pj.get_status())
        self.cursor.execute(query, values)
        self.conn.commit()
        return True

    def createTask(self, task):
        query = "INSERT INTO Task (task_name, project_id, employee_id, status) VALUES (%s, %s, %s, %s)"
        values = (task.get_name(), task.get_project_id(), task.get_employee_id(), task.get_status())
        self.cursor.execute(query, values)
        self.conn.commit()
        return True

    def assignProjectToEmployee(self, projectId, employeeId):
        query = "UPDATE Employee SET project_id = %s WHERE id = %s"
        self.cursor.execute(query, (projectId, employeeId))
        self.conn.commit()
        return True

    def assignTaskInProjectToEmployee(self, taskId, projectId, employeeId):
        query = "UPDATE Task SET project_id = %s, employee_id = %s WHERE task_id = %s"
        self.cursor.execute(query, (projectId, employeeId, taskId))
        self.conn.commit()
        return True

    def deleteEmployee(self, empId):
        query = "DELETE FROM Employee WHERE id = %s"
        self.cursor.execute(query, (empId,))
        self.conn.commit()
        return True

    def deleteProject(self, projectId):
        query = "DELETE FROM Project WHERE id = %s"
        self.cursor.execute(query, (projectId,))
        self.conn.commit()
        return True

    def getAllTasks(self, empId, projectId):
        query = "SELECT * FROM Task WHERE employee_id = %s AND project_id = %s"
        self.cursor.execute(query, (empId, projectId))
        return self.cursor.fetchall()

```
#### <p align = "center"> explanation </p>
#### i_project_repository.py – Interface Layer

This module defines the abstract contract for the data access layer (DAO). It contains the method signatures (blueprints) that are implemented in the corresponding repository implementation class.

#### 🔹 Purpose:
To separate the interface from the implementation and promote loose coupling and modularity in the application.

#### 🔹 Key Responsibilities:

Declare all necessary methods for managing employees, projects, and tasks.

Ensure consistency across all DAO implementations.

Facilitate clean architecture by abstracting business logic from database logic.

#### 🔹 Declared Methods:

1. createEmployee(emp)

2. createProject(pj)

3. createTask(task)

4. assignProjectToEmployee(projectId, employeeId)

5. assignTaskInProjectToEmployee(taskId, projectId, employeeId)

6. deleteEmployee(employeeId)

7. deleteProject(projectId)

8. getAllTasks(empId, projectId)



####  Implement the above interface in a class called ProjectRepositoryImpl in package dao

   #### created a class called ProjectRepositoryImpl in package dao 

<p align="center">
  <img src="https://github.com/user-attachments/assets/90d72294-9272-4497-850a-517dae6ed8c8" width="45%" />
</p>


#### code:
```
import mysql.connector
from dao.i_project_repository import IProjectRepository
from util.db_conn_util import DBConnUtil

class ProjectRepositoryImpl(IProjectRepository):

    def __init__(self, config_path):
        self.conn = DBConnUtil.get_connection(config_path)
        self.cursor = self.conn.cursor()

    def createEmployee(self, emp):
        values = (
            emp.get_name(),
            emp.get_designation(),
            emp.get_gender(),  # This should be already 'Male' / 'Female' / 'Other'
            emp.get_salary(),
            emp.get_project_id()
        )

        print(" DEBUG - Values to Insert:", values)

        query = "INSERT INTO Employee (name, designation, gender, salary, project_id) VALUES (%s, %s, %s, %s, %s)"

        try:
            self.cursor.execute(query, values)
            self.conn.commit()
            return True
        except Exception as e:
            print(" ERROR:", e)
            return False

    def createProject(self, pj):
        query = "INSERT INTO Project (project_name, description, start_date, status) VALUES (%s, %s, %s, %s)"
        values = (pj.get_name(), pj.get_description(), pj.get_start_date(), pj.get_status())
        self.cursor.execute(query, values)
        self.conn.commit()
        return True

    def createTask(self, task):
        query = "INSERT INTO Task (task_name, project_id, employee_id, status) VALUES (%s, %s, %s, %s)"
        values = (task.get_name(), task.get_project_id(), task.get_employee_id(), task.get_status())
        self.cursor.execute(query, values)
        self.conn.commit()
        return True

    def assignProjectToEmployee(self, projectId, employeeId):
        query = "UPDATE Employee SET project_id = %s WHERE id = %s"
        self.cursor.execute(query, (projectId, employeeId))
        self.conn.commit()
        return True

    def assignTaskInProjectToEmployee(self, taskId, projectId, employeeId):
        query = "UPDATE Task SET project_id = %s, employee_id = %s WHERE task_id = %s"
        self.cursor.execute(query, (projectId, employeeId, taskId))
        self.conn.commit()
        return True

    def deleteEmployee(self, empId):
        query = "DELETE FROM Employee WHERE id = %s"
        self.cursor.execute(query, (empId,))
        self.conn.commit()
        return True

    def deleteProject(self, projectId):
        query = "DELETE FROM Project WHERE id = %s"
        self.cursor.execute(query, (projectId,))
        self.conn.commit()
        return True

    def getAllTasks(self, empId, projectId):
        query = "SELECT * FROM Task WHERE employee_id = %s AND project_id = %s"
        self.cursor.execute(query, (empId, projectId))
        return self.cursor.fetchall()

```
#### project_repository_impl.py – DAO Implementation Layer

This module provides the concrete implementation of all methods declared in i_project_repository.py. It is responsible for actual interaction with the MySQL database using SQL queries.

#### 🔹 Purpose:
To execute CRUD operations and data manipulation by communicating directly with the database.

#### 🔹 Key Responsibilities:

Insert new employees, projects, and tasks into the database.

Assign tasks/projects to employees.

Delete employees or projects.

Fetch task data filtered by employee and project.

Handle exceptions during database transactions.

Maintain connection and cursor management.

#### 🔹 Technologies Used:

mysql-connector-python for MySQL database interaction.

Custom exception handling for better feedback and debugging.

Object-oriented approach with method-driven design.

#### 🔹 Benefits:

Keeps the database logic modular and manageable.

Can easily be extended or swapped with another DB implementation.

Enhances testability and maintainability of the system.

###  <p align="center"> TASK 8 </p>


### 🔌 Database Connection Setup

The application connects to a MySQL database using utility classes inside the `util/` package.

#### 🔹 DBConnection (in `util/db_connection.py`)

- Establishes a connection to the MySQL database.
 
- Uses `mysql-connector-python` for connectivity.
  
- Provides a static method `get_connection()` to reuse a single connection throughout the app.

```
from util.db_property_util import DBPropertyUtil
import mysql.connector

class DBConnUtil:
    @staticmethod
    def get_connection(config_path):
        try:
            db_config = DBPropertyUtil.get_db_config(config_path)
            connection = mysql.connector.connect(
                host=db_config['host'],
                user=db_config['user'],
                password=db_config['password'],
                database=db_config['database']
            )
            return connection
        except Exception as e:
            print(" Error in DBConnUtil:", str(e))
            return None

```

#### 🔹 PropertyUtil (in `util/property_util.py`)

- Reads database connection details (hostname, port, db name, username, password) from a `.properties` file.
  
- Returns a formatted connection string.

```
import configparser
import os


class DBPropertyUtil:
    @staticmethod
    def get_db_config(path: str):
        config = configparser.ConfigParser()
        full_path = os.path.abspath(path)

        print("Reading from:", full_path)  # 🔍 Debug print
        config.read(full_path)

        if 'database' not in config:
            print("Sections found:", config.sections())  # 🔍 Debug print
            raise Exception("Missing [database] section in properties file.")

        return {
            'host': config['database']['host'],
            'user': config['database']['user'],
            'password': config['database']['password'],
            'database': config['database']['database']
        }

```
📁 **config.properties**

```
[database]
host = localhost
user = root
password = Vineeth1246@
database = project_management

```

### <p align="center" > Menu description and Output </p>

### <p align="center"> Description </p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/de68b6c4-0b95-4969-a9b0-1c9cda054ed9" width="45%" />
</p>

### Adding employee

<table>
  <tr>
    <th align="center">User Input</th>
    <th></th>
    <th align="center">Database View</th>
  </tr>
  <tr>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/cbd785a7-3b64-4854-922f-ece8c51bdd71" width="300px" />
    </td>
    <td align="center" style="border-left: 2px solid gray; width: 1px;"></td>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/40027ba8-ed83-42d5-8f5e-968395c5c951" width="300px" />
    </td>
  </tr>
</table>


### Adding Project

<table>
  <tr>
    <th align="center">User Input</th>
    <th></th>
    <th align="center">Database View</th>
  </tr>
  <tr>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/137ebbdb-552b-4788-92a2-1135655370f9" width="300px" />
    </td>
    <td align="center" style="border-left: 2px solid gray; width: 1px;"></td>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/1e664ac0-9a29-41de-939b-6adb88f50681" width="300px" />
    </td>
  </tr>
</table>

### Adding Task
<table>
  <tr>
    <th align="center">User Input</th>
    <th></th>
    <th align="center">Database View</th>
  </tr>
  <tr>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/9bcb5c60-cf19-46d7-8153-321aedffb9b0" width="300px" />
    </td>
    <td align="center" style="border-left: 2px solid gray; width: 1px;"></td>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/f8307ae1-a352-4ed7-94ae-28bddc719b9c" width="300px" />
    </td>
  </tr>
</table>

### Assign project to employee
<table>
  <tr>
    <th align="center">User Input</th>
    <th></th>
    <th align="center">Database View</th>
  </tr>
  <tr>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/96a04b89-c24f-46a7-b846-c32de5acb61a" width="300px" />
    </td>
    <td align="center" style="border-left: 2px solid gray; width: 1px;"></td>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/2f7633aa-eb73-4982-b7e1-7fc87354ffd1" width="300px" />
    </td>
  </tr>
</table>

### Assign task within a project to employee

<table>
  <tr>
    <th align="center">User Input</th>
    <th></th>
    <th align="center">Database View</th>
  </tr>
  <tr>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/bebef7bb-0975-4335-b322-a5db405614fd" width="300px" />
    </td>
    <td align="center" style="border-left: 2px solid gray; width: 1px;"></td>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/4c417f8a-4116-4c35-a0a9-77aad19d35b5" width="300px" />
    </td>
  </tr>
</table>


### Delete Employee


<p align="center">
  <img src="https://github.com/user-attachments/assets/4ac65040-b0bc-47e0-a61a-8dcadcd7690e" width="45%" />
</p>




<table>
  <tr>
    <th align="center">Before</th>
    <th></th>
    <th align="center">After</th>
  </tr>
  <tr>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/e205284c-653b-4a49-9f33-01d7eb33c9df" width="300px" />
    </td>
    <td align="center" style="border-left: 2px solid gray; width: 1px;"></td>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/0f3dfe00-afff-45e6-b585-d4c2e530c698" width="300px" />
    </td>
  </tr>
</table>

### Delete task
![image](https://github.com/user-attachments/assets/df492b3a-d6a2-4782-a286-627f9bc21daf)

<table>
  <tr>
    <th align="center">Before</th>
    <th></th>
    <th align="center">After</th>
  </tr>
  <tr>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/da55a6ee-3b9a-4221-a3a3-4eb3a11d30f1" width="300px" />
    </td>
    <td align="center" style="border-left: 2px solid gray; width: 1px;"></td>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/900510a5-bdd5-4796-ad13-5dd673b18aba" width="300px" />
    </td>
  </tr>
</table>
