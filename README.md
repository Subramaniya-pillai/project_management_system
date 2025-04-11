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

### ER Diagram

<p align="center">
  <img src="https://github.com/user-attachments/assets/ea104d75-c27b-49c6-accf-f07dc7f9b96d" width="55%" />
</p>

###  <p align="center"> Directory structure </p>


<p align="center">
  <img src="https://github.com/user-attachments/assets/e8c55119-e1e5-4898-8feb-5ab3a88510c8" width="55%" />
</p>


## Entity:


### employee.py

```
class Employee:
    def __init__(self, name, designation, gender, salary, project_id=None):
        self.name = name
        self.designation = designation
        self.gender = gender
        self.salary = salary
        self.project_id = project_id

    def get_name(self):
        return self.name

    def get_designation(self):
        return self.designation

    def get_gender(self):
        return self.gender

    def get_salary(self):
        return self.salary

    def get_project_id(self):
        return self.project_id

```

### project.py

```
class Project:
    def __init__(self, name=None, description=None, start_date=None, status=None):
        self.__name = name
        self.__description = description
        self.__start_date = start_date
        self.__status = status

    def get_name(self):
        return self.__name

    def get_description(self):
        return self.__description

    def get_start_date(self):
        return self.__start_date

    def get_status(self):
        return self.__status

    def set_project_id(self, project_id): self.__project_id = project_id
    def set_name(self, name): self.__name = name
    def set_description(self, description): self.__description = description
    def set_start_date(self, start_date): self.__start_date = start_date
    def set_status(self, status): self.__status = status

```

### Task.py

```

class Task:
    def __init__(self, task_id, task_name, project_id, employee_id, status):
        self.__task_id = task_id
        self.__task_name = task_name
        self.__project_id = project_id
        self.__employee_id = employee_id
        self.__status = status


    # Getters
    def get_task_id(self):
        return self.__task_id

    def get_task_name(self):
        return self.__task_name

    def get_project_id(self):
        return self.__project_id

    def get_employee_id(self):
        return self.__employee_id

    def get_status(self):
        return self.__status

    # Setters
    def set_task_id(self, task_id):
        self.__task_id = task_id

    def set_task_name(self, task_name):
        self.__task_name = task_name

    def set_project_id(self, project_id):
        self.__project_id = project_id

    def set_employee_id(self, employee_id):
        self.__employee_id = employee_id

    def set_status(self, status):
        self.__status = status

```

## Dao

### IProjectRepository

```

import mysql.connector
from dao.i_project_repository import IProjectRepository
from util.db_conn_util import DBConnUtil
from myexceptions.employee_not_found_exception import EmployeeNotFoundException
from myexceptions.project_not_found_exception import ProjectNotFoundException
from entity.task import Task  # adjust path based on your project structure

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

        print(" DEBUG: Project Values:", values)  # <-- Add this line

        self.cursor.execute(query, values)
        self.conn.commit()
        return True

    def createTask(self, task):
        query = "INSERT INTO Task (task_name, project_id, employee_id, status) VALUES (%s, %s, %s, %s)"
        values = (
            task.get_task_name(),
            task.get_project_id(),
            task.get_employee_id(),
            task.get_status()
        )

        print(" DEBUG: Task Values:", values)  # Optional debug line
        self.cursor.execute(query, values)
        self.conn.commit()
        return True

    def assignProjectToEmployee(self, projectId, employeeId):
        if not self.projectExists(projectId):
            raise ProjectNotFoundException(projectId)
        if not self.employeeExists(employeeId):
            raise EmployeeNotFoundException(employeeId)
        query = "UPDATE Employee SET project_id = %s WHERE id = %s"
        self.cursor.execute(query, (projectId, employeeId))
        self.conn.commit()
        return True

    def assignTaskInProjectToEmployee(self, taskId, projectId, employeeId):
        if not self.projectExists(projectId):
            raise ProjectNotFoundException(projectId)
        if not self.employeeExists(employeeId):
            raise EmployeeNotFoundException(employeeId)
        query = "UPDATE Task SET project_id = %s, employee_id = %s WHERE task_id = %s"
        self.cursor.execute(query, (projectId, employeeId, taskId))
        self.conn.commit()
        return True

    def deleteEmployee(self, empId):
        if not self.employeeExists(empId):
            raise EmployeeNotFoundException(empId)
        query = "DELETE FROM Employee WHERE id = %s"
        self.cursor.execute(query, (empId,))
        self.conn.commit()
        return True

    def deleteProject(self, projectId):
        if not self.projectExists(projectId):
            raise ProjectNotFoundException(projectId)
        query = "DELETE FROM Project WHERE id = %s"
        self.cursor.execute(query, (projectId,))
        self.conn.commit()
        return True

    def getAllTasks(self, employee_id, project_id):
        query = "SELECT task_id, task_name, project_id, employee_id, status FROM Task WHERE employee_id = %s AND project_id = %s"
        self.cursor.execute(query, (employee_id, project_id))
        results = self.cursor.fetchall()

        tasks = []
        for row in results:
            task = Task(None, None, None, None)  # Create empty object
            task.set_task_id(row[0])
            task.set_name(row[1])
            task.set_project_id(row[2])
            task.set_employee_id(row[3])
            task.set_status(row[4])
            tasks.append(task)

        return tasks

    def employeeExists(self, empId):
        self.cursor.execute("SELECT 1 FROM Employee WHERE id = %s", (empId,))
        return self.cursor.fetchone() is not None

    def projectExists(self, projectId):
        self.cursor.execute("SELECT 1 FROM Project WHERE id = %s", (projectId,))
        return self.cursor.fetchone() is not None

```

### ProjectRepositoryImpl

```
import mysql.connector
from dao.i_project_repository import IProjectRepository
from util.db_conn_util import DBConnUtil
from myexceptions.employee_not_found_exception import EmployeeNotFoundException
from myexceptions.project_not_found_exception import ProjectNotFoundException
from entity.task import Task  # adjust path based on your project structure

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

        print("🛠 DEBUG: Project Values:", values)  # <-- Add this line

        self.cursor.execute(query, values)
        self.conn.commit()
        return True

    def createTask(self, task):
        query = "INSERT INTO Task (task_name, project_id, employee_id, status) VALUES (%s, %s, %s, %s)"
        values = (
            task.get_task_name(),
            task.get_project_id(),
            task.get_employee_id(),
            task.get_status()
        )

        print(" DEBUG: Task Values:", values)  # Optional debug line
        self.cursor.execute(query, values)
        self.conn.commit()
        return True

    def assignProjectToEmployee(self, projectId, employeeId):
        if not self.projectExists(projectId):
            raise ProjectNotFoundException(projectId)
        if not self.employeeExists(employeeId):
            raise EmployeeNotFoundException(employeeId)
        query = "UPDATE Employee SET project_id = %s WHERE id = %s"
        self.cursor.execute(query, (projectId, employeeId))
        self.conn.commit()
        return True

    def assignTaskInProjectToEmployee(self, taskId, projectId, employeeId):
        if not self.projectExists(projectId):
            raise ProjectNotFoundException(projectId)
        if not self.employeeExists(employeeId):
            raise EmployeeNotFoundException(employeeId)
        query = "UPDATE Task SET project_id = %s, employee_id = %s WHERE task_id = %s"
        self.cursor.execute(query, (projectId, employeeId, taskId))
        self.conn.commit()
        return True

    def deleteEmployee(self, empId):
        if not self.employeeExists(empId):
            raise EmployeeNotFoundException(empId)
        query = "DELETE FROM Employee WHERE id = %s"
        self.cursor.execute(query, (empId,))
        self.conn.commit()
        return True

    def deleteProject(self, projectId):
        if not self.projectExists(projectId):
            raise ProjectNotFoundException(projectId)
        query = "DELETE FROM Project WHERE id = %s"
        self.cursor.execute(query, (projectId,))
        self.conn.commit()
        return True

    def getAllTasks(self, employee_id, project_id):
        query = "SELECT task_id, task_name, project_id, employee_id, status FROM Task WHERE employee_id = %s AND project_id = %s"
        self.cursor.execute(query, (employee_id, project_id))
        results = self.cursor.fetchall()

        tasks = []
        for row in results:
            task = Task(None, None, None, None)  # Create empty object
            task.set_task_id(row[0])
            task.set_name(row[1])
            task.set_project_id(row[2])
            task.set_employee_id(row[3])
            task.set_status(row[4])
            tasks.append(task)

        return tasks

    def employeeExists(self, empId):
        self.cursor.execute("SELECT 1 FROM Employee WHERE id = %s", (empId,))
        return self.cursor.fetchone() is not None

    def projectExists(self, projectId):
        self.cursor.execute("SELECT 1 FROM Project WHERE id = %s", (projectId,))
        return self.cursor.fetchone() is not None

```

## exceptions

### EmployeeNotFoundException

```
class EmployeeNotFoundException(Exception):
    def __init__(self, employee_id):
        super().__init__(f"Employee with ID {employee_id} not found in the database.")

```


### ProjectNotFoundException

```
class ProjectNotFoundException(Exception):
    def __init__(self, project_id):
        super().__init__(f"Project with ID {project_id} not found in the database.")

```


## Main

```
from dao.project_repository_impl import ProjectRepositoryImpl
from entity.employee import Employee
from entity.project import Project
from entity.task import Task
from myexceptions.employee_not_found_exception import EmployeeNotFoundException
from myexceptions.project_not_found_exception import ProjectNotFoundException
import os

def main():
    # Path to your properties file
    current_dir = os.path.dirname(os.path.abspath(__file__))
    config_path = os.path.join(current_dir, "../config/db.properties")

    # Repository instance
    repo = ProjectRepositoryImpl(config_path)

    while True:
        print("\n==== Project Management System ====")
        print("1. Add Employee")
        print("2. Add Project")
        print("3. Add Task")
        print("4. Assign Project to Employee")
        print("5. Assign Task to Employee in a Project")
        print("6. Delete Employee")
        print("7. Delete Project")
        print("8. Get All Tasks for an Employee in a Project")
        print("9. Exit")

        choice = input("Enter your choice: ")

        try:
            if choice == '1':
                name = input("Enter employee name: ")
                designation = input("Enter designation: ")

                gender = input("Enter gender (Male/Female/Other): ").strip().capitalize()
                valid_genders = ['Male', 'Female', 'Other']
                while gender not in valid_genders:
                    print(" Invalid gender. Please enter Male, Female, or Other.")
                    gender = input("Enter gender (Male/Female/Other): ").strip().capitalize()

                salary = float(input("Enter salary: "))
                project_id_input = input("Enter project ID (or leave blank): ")
                project_id = int(project_id_input) if project_id_input else None

                print(
                    f" Submitting Employee: Name={name}, Designation={designation}, Gender={gender}, Salary={salary}, ProjectID={project_id}")

                emp = Employee(name, designation, gender, salary, project_id)
                print(" Employee added!" if repo.createEmployee(emp) else " Failed to add employee.")


            elif choice == '2':

                name = input("Enter project name: ").strip()

                # Keep asking until a non-empty description is entered

                while True:

                    description = input("Enter project description: ").strip()

                    if description:
                        break

                    print(" Description cannot be empty.")

                while True:

                    start_date = input("En4ter start date (YYYY-MM-DD): ").strip()

                    if start_date:
                        break

                    print(" Start date cannot be empty.")

                while True:

                    status = input("Enter status (started/dev/build/test/deployed): ").strip().lower()

                    valid_status = ['started', 'dev', 'build', 'test', 'deployed']

                    if status in valid_status:
                        break

                    print(" Invalid status. Choose from:", ", ".join(valid_status))

                proj = Project(name, description, start_date, status)

                print(" Project added!" if repo.createProject(proj) else " Failed to add project.")



            elif choice == '3':

                task_name = input("Enter task name: ").strip()

                while True:

                    try:

                        project_id = int(input("Enter project ID: ").strip())

                        break

                    except ValueError:

                        print(" Please enter a valid numeric Project ID.")

                while True:

                    try:

                        employee_id = int(input("Enter employee ID: ").strip())

                        break

                    except ValueError:

                        print(" Please enter a valid numeric Employee ID.")

                valid_status = ['Assigned', 'Started', 'Completed']

                while True:

                    status = input("Enter task status (Assigned/Started/Completed): ").strip().capitalize()

                    if status in valid_status:
                        break

                    print(" Invalid status. Choose from Assigned, Started, Completed.")

                task = Task(task_name, project_id, employee_id, status)

                print(" Task added!" if repo.createTask(task) else " Failed to add task.")


            elif choice == '4':
                project_id = int(input("Enter project ID to assign: "))
                employee_id = int(input("Enter employee ID: "))
                print(" Project assigned!" if repo.assignProjectToEmployee(project_id, employee_id) else " Failed to assign project.")

            elif choice == '5':
                task_id = int(input("Enter task ID: "))
                project_id = int(input("Enter project ID: "))
                employee_id = int(input("Enter employee ID: "))
                print(" Task assigned!" if repo.assignTaskInProjectToEmployee(task_id, project_id, employee_id) else " Failed to assign task.")

            elif choice == '6':
                employee_id = int(input("Enter employee ID to delete: "))
                print("Employee deleted!" if repo.deleteEmployee(employee_id) else " Failed to delete employee.")

            elif choice == '7':
                project_id = int(input("Enter project ID to delete: "))
                print(" Project deleted!" if repo.deleteProject(project_id) else " Failed to delete project.")

            elif choice == '8':
                employee_id = int(input("Enter employee ID: "))
                project_id = int(input("Enter project ID: "))
                tasks = repo.getAllTasks(employee_id, project_id)
                if tasks:
                    print(f" Tasks for Employee ID {employee_id} in Project ID {project_id}:")
                    for t in tasks:
                        print(f"• {t.task_id} - {t.task_name} [{t.status}]")
                else:
                    print(" No tasks found.")

            elif choice == '9':
                print(" Exiting Project Management System. Goodbye!")
                break2

            else:
                print(" Invalid choice. Try again.")

        except EmployeeNotFoundException as e:
            print(f" Error: {e}")

        except ProjectNotFoundException as e:
            print(f" Error: {e}")

        except Exception as e:
            print(f" Unexpected Error: {e}")

if __name__ == "__main__":
    main()

```

###  <p align="center"> Database Connection Setup </p>

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

        print("Reading from:", full_path)  
        config.read(full_path)

        if 'database' not in config:
            print("Sections found:", config.sections())  
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
