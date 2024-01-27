
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


