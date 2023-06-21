# EMPLOYEE MANAGEMENT APP USING REACT, SPRINGBOOT AND SQL
## AIM
To create an Employee Management App using React,SpringBoot and SQL
## ALGORITHM
1. Configure a new project with the required dependencies and files.
2. Initialise the same in IntelliJ and include file employee.java which contains all the variables like employee ID,name,DOB,Email and their
   respective constructor,getters and setters.
3. Include Components and service files which configures and works for commands like GET,POST and DELETE.
4. Run the file by connecting to an database by creating the same in psql.
5. Start an react project named employee-management-frontend
6. Include necessary functions in App.js
7. Include the necessary .css files for all the three methods.
8. Define separate .js files for viewing the list of employees,adding an employee,and deleting an employee.
9. Connect with the backend server and view the output user interface in the browser.

# PROGRAM
### SpringBoot Program
### Employee.java
```
package com.saveetha.employee.emp;

import jakarta.persistence.*;

import java.time.LocalDate;
import java.time.Period;

@Entity
@Table
public class Employee {
    @Id
    @SequenceGenerator(
            name="employee_sequence",
            sequenceName = "employee_sequence",
            allocationSize = 1
    )
    @GeneratedValue(
            strategy = GenerationType.SEQUENCE,
            generator = "employee_sequence"
    )
   private Long employeeID;
    private String employeeName;

    @Transient
    private int employeeAge;
    private LocalDate employeeDOB;
    private String employeeEmail;
    public Employee(){

    }

    public Employee(Long employeeID, String employeeName,  LocalDate employeeDOB, String employeeEmail) {
        this.employeeID = employeeID;
        this.employeeName = employeeName;

        this.employeeDOB = employeeDOB;
        this.employeeEmail = employeeEmail;
    }

    public Long getEmployeeID() {
        return employeeID;
    }

    public String getEmployeeName() {
        return employeeName;
    }

    public int getEmployeeAge() {

        return Period.between(employeeDOB, LocalDate.now()).getYears();
    }

    public LocalDate getEmployeeDOB() {
        return employeeDOB;
    }

    public String getEmployeeEmail() {
        return employeeEmail;
    }

    public void setEmployeeID(Long employeeID) {
        this.employeeID = employeeID;
    }

    public void setEmployeeName(String employeeName) {
        this.employeeName = employeeName;
    }

    public void setEmployeeAge(int employeeAge) {
        this.employeeAge = employeeAge;
    }

    public void setEmployeeDOB(LocalDate employeeDOB) {
        this.employeeDOB = employeeDOB;
    }

    public void setEmployeeEmail(String employeeEmail) {
        this.employeeEmail = employeeEmail;
    }
@Override
    public String toString() {
        return "Employee{" +
                "employeeID=" + employeeID +
                ", employeeName='" + employeeName + '\'' +
                ", employeeAge=" + employeeAge +
                ", employeeDOB=" + employeeDOB +
                ", employeeEmail='" + employeeEmail + '\'' +
                '}';
    }
}
```

### EmployeeController.java
```
package com.saveetha.employee.emp;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.time.LocalDate;
import java.time.Month;
import java.util.List;

@RestController
@RequestMapping(path="/api/v1/employee")
public class EmployeeController {
    private final EmployeeService employeeService;

    @Autowired
    public EmployeeController(EmployeeService employeeService) {
        this.employeeService = employeeService;
    }

    @GetMapping("/")
    public List<Employee> getEmployeeDetails() {

       return employeeService.displayEmployeeDetails();
    }
    @PostMapping("/")
    public void postEmployee(@RequestBody Employee employee){
        employeeService.registerNewEmployee(employee);
    }
    @DeleteMapping(path = {"/{id}"})
    public void deleteEmployee(@PathVariable("id") Long employeeId){
        employeeService.removeEmployee(employeeId);
    }
}
```
### React Program
### EmployeeDirectoryComponent.js
```
import React, { useEffect, useState } from 'react'
import './EmployeeDirectoryComponent.css'

function EmployeeDirectoryComponent() {
    const [employees,setEmployees] = useState([])

    useEffect(() => {
        fetchEmployees()
    }, [])

    const fetchEmployees = async() => {
        try{
        const response = await fetch('http://localhost:8080/api/v1/employee/')
        const data = await response.json()
        setEmployees(data)
        }
        catch(error){
            console.log("Error:",error);
        }
    }
  return (
    <div className='employee-list'>
        {employees.map((employee) =>(
            <div className='employee-card' key={employee.employeeID}>
                <p>Employee ID: {employee.employeeID}</p>
                <p> Name: {employee.employeeName}</p>
                <p> Age: {employee.employeeAge}</p>
                <p> DOB: {employee.employeeDOB}</p>
                <p> Email: {employee.employeeEmail}</p>
            </div>
        )
        )}
    </div>
  )
}

export default EmployeeDirectoryComponent
```
### EmployeeRegistrationComponent.js
```
import React,{useState} from 'react'
import './EmployeeRegistrationComponent.css'
function EmployeeRegistrationComponent() {

    const [employee,setEmployees] = useState({
        employeeName: '',
        employeeDOB: '',
        employeeEmail: ''
    })

    const handleChange = (event) => {
        const {name,value} = event.target
        setEmployees({...employee,[name]:value})
    }

    const handleFormSubmit = async(event) => {
        event.preventDefault();
        await fetch('http://localhost:8080/api/v1/employee/',{
            method:'POST',
            headers:{
                'Content-Type':'application/json'
            },
            body:JSON.stringify(employee)
        })
        .then((response) => {
            if (response.status == 500){
                alert('Error!')
               
            }
            else{
                alert(`Data of ${employee.employeeName} is added successfully`)
                window.location.href = '/'

            }
        })
    }

  return (
    <form onSubmit={handleFormSubmit}>
        <label>
            Employee Name:
            <input
            type='text'
            name='employeeName'
            value={employee.employeeName}
            onChange={handleChange}
            required
            />
        </label>
        <label>
            Employee DOB:
            <input
            type='date'
            name='employeeDOB'
            value={employee.employeeDOB}
            onChange={handleChange}
            required
            />
        </label>
        <label>
            Employee Email:
            <input
            type='email'
            name='employeeEmail'
            value={employee.employeeEmail}
            onChange={handleChange}
            required
            />
        </label>

        <button type='submit'> submit</button>
    </form>
  )
}

export default EmployeeRegistrationComponent
```
### EmployeeDeletionComponent.js
```
import React, { useState } from 'react';
import './EmployeeDeletionComponent.css'

function EmployeeDeletionComponent() {
  const [employeeID, setEmployeeID] = useState('');

  const handleChange = (event) => {
    setEmployeeID(event.target.value);
  };

  const handleSubmit = async(event) => {
    event.preventDefault();
    await fetch(`http://localhost:8080/api/v1/employee/${employeeID}`,{
      method: 'DELETE'
    })
    .then((response) => {
            if (response.status == 500)
            {
                alert(`Error!`)
            }
            else{
                alert(`Data deleted successfully`)
                window.location.href = '/'
            }
        })
  };

  return (
    <form onSubmit={handleSubmit}>
      <label>
        Employee ID:
        <input
          type="number"
          name="employeeID"
          value={employeeID}
          onChange={handleChange}
          required
        />
      </label>
      <button type="submit">Submit</button>
    </form>
  );
};

export default EmployeeDeletionComponent;

```
## OUTPUT
### List of Employees
<img width="960" alt="Screenshot 2023-06-21 205853" src="https://github.com/Shavedha/Employee-Management-App/assets/93427376/cb2383c9-9114-4e5b-9c95-0970b566028c">

### Add an Employee
<img width="960" alt="Screenshot 2023-06-21 205944" src="https://github.com/Shavedha/Employee-Management-App/assets/93427376/4085c8a5-ada3-4d5b-acb3-934f6a02e161">

### Delete an Employee
<img width="960" alt="Screenshot 2023-06-21 205934" src="https://github.com/Shavedha/Employee-Management-App/assets/93427376/ba34641c-4227-4376-86e1-ca0d314d1c17">

## RESULT
Thus an Employee Management App using React,SpringBoot and SQL is successfully created.
