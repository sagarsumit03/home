# Builder:

### The Builder pattern
Another common creational pattern is the Builder pattern. The popular use of Builder in the Spring is ResultAction. This class is part of the MockMcv - used for testing web applications.

### Intent
Each method return the object reference it`s called from.
There is build() method which returns a fully contstructed object (based on all the intermediate calls).
Problem Solution
Improves the readability of the code when object creation has many parameters.
Useful when some or all paramaters are optional.
Structure of the Builder Pattern
Firstly, you need to define a base class with all args constructor. Then, you have to create a Builder class with attributes and setters for each argument of the base class. Each setter should return the builder. Then, need to create a build() methid that will construct and return object of base class.


```
public class Employee{
    private String name;
    private String address;
    
    //getters
}

```
```
public class EmployeeBuilder{
    private String name;
    private String address;
    
    //setters which returns the Builder Only:
    public EmployeeBuilder(string name){
          this.name =name;
          return this;
    }
    //constructor which calls the Employee Constructor:
    public EmployeeBuilder(){
          new Employee(name, address);
    }
}

```
And this is how you return a new Employee 

```
public Employee getEmployee2() {  
        return new EmployeeBuilder()  
                .setEmployeeId("4321")  
                .setAddress("Jeff")    
                .buildEmployee();  
    }  

```
