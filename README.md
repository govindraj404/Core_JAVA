
## OOPS Concept

### Table of Contents

| No. | Questions |
|---- | ---------
|1 | [List out the Java OOPs Concept?](#List-out-the-Java-OOPs-Concept)|
|2 | [What is the difference between AngularJS and Angular?](#what-is-the-difference-between-angularjs-and-angular)|

1. ### List out the Java OOPs Concept

        1.Polymorphisum
        2.Encapsulation
        3.Abstraction
        4.Iniheritance
  **[⬆ Back to Top](#table-of-contents)**

2. ### Encapsulation

   **Detailed Explanation:**

    Encapsulation is the bundling of data (attributes or fields) and methods that operate on the data into a single unit, typically a class. It involves the concept of access modifiers to control the access to the internal state of an object.

   **Key Aspects:**

   Access Modifiers: Fields are often declared as private, limiting direct access from outside the class. Public methods, often referred to as getters and setters, provide controlled access to the internal state.
Data Hiding: Encapsulation hides the internal details of how an object's state is implemented, allowing the object to maintain its integrity.
Detailed Scenario:
Consider a Person class encapsulating personal information:

   ```java
         public class Person {
            private String name;
            private int age;
        
            public Person(String name, int age) {
                this.name = name;
                this.age = age;
            }
        
            // Getter and setter methods
            public String getName() {
                return name;
            }
        
            public int getAge() {
                return age;
            }
        
            public void setAge(int age) {
                // Additional logic for validation can be added
                this.age = age;
            }
        }
        
   ```

**[⬆ Back to Top](#table-of-contents)**

3. ### Inheritance in Java

   Inheritance is a fundamental concept in Object-Oriented Programming (OOP) that allows a class to inherit properties and behaviors from another class. In 
   Java, it promotes code reuse and establishes relationships between classes.

   ## Detailed Explanation:

   ### Superclass and Subclass:
   Inheritance involves the creation of a superclass (base class) and a subclass (derived class). The superclass provides a general template, and the 
   subclass extends or specializes it by adding or modifying features.

   ### Code Reusability:
    One of the primary advantages of inheritance is code reusability. The fields and methods defined in the superclass are inherited by the subclass, allowing 
   the subclass to reuse the functionality provided by the superclass.
   ## Detailed Scenario:

   Consider a `Vehicle` superclass and specific vehicle types (`Car` and `Motorcycle`) as subclasses:

   ```java
        public class Vehicle {
            protected String brand;

            public Vehicle(String brand) {
                this.brand = brand;
            }

            public void start() {
                System.out.println("The vehicle is starting.");
            }
        }

        public class Car extends Vehicle {
            private int numberOfDoors;

            public Car(String brand, int numberOfDoors) {
                super(brand);
                this.numberOfDoors = numberOfDoors;
            }

            // Additional methods specific to Car
        }

        public class Motorcycle extends Vehicle {
            private boolean hasSidecar;
        
            public Motorcycle(String brand, boolean hasSidecar) {
                super(brand);
            this.hasSidecar = hasSidecar;
            }
        
            // Additional methods specific to Motorcycle
        }
      ```
      In this scenario, Car and Motorcycle inherit common properties from the Vehicle superclass. The start method is reused in both subclasses, demonstrating         the principle of code reusability through inheritance.
   
   **[⬆ Back to Top](#table-of-contents)**

4. ### what is the difference betweent the abstractclass and interface?
   ```typescritp
   ```
   **[⬆ Back to Top](#table-of-contents)**

5. ### Q5?
   ```typescritp
   ```
   **[⬆ Back to Top](#table-of-contents)**
6. ### Q6?
   ```typescritp
   ```
   **[⬆ Back to Top](#table-of-contents)**
   


