# Java 8 Lambda Expressions & Functional Interfaces
## Complete Study Notes with Examples

---

## Table of Contents

1. [Introduction to Lambda Expressions](#introduction)
2. [Functional Interfaces](#functional-interfaces)
3. [Lambda Expression Syntax](#lambda-syntax)
4. [Built-in Functional Interfaces](#builtin-interfaces)
5. [Stream API with Lambda Expressions](#stream-api)
6. [Comparator with Lambda](#comparator)
7. [Optional Class](#optional)
8. [Real-World Examples](#real-world)
9. [Best Practices](#best-practices)

---

## 1. Introduction to Lambda Expressions {#introduction}

### What is a Lambda Expression?
A **lambda expression** is a concise way to represent an anonymous function that can be used to implement functional interfaces. It provides a clear and compact way to represent one method interface using an expression.

### Benefits:
- Reduces boilerplate code
- Makes code more readable
- Enables functional programming in Java
- Works seamlessly with Stream API

### Before Java 8 vs After Java 8

```java
// Before Java 8 - Using Anonymous Inner Class
Runnable r1 = new Runnable() {
    @Override
    public void run() {                    // Method declaration
        System.out.println("Hello World"); // Method body
    }
};

// After Java 8 - Using Lambda Expression
Runnable r2 = () -> System.out.println("Hello World");
//                ^^ Lambda arrow operator
//             ^^ Empty parameter list (no parameters)
//                   ^^ Method body as expression
```

**Line-by-line Comments:**
- Line 1: Traditional approach requires explicit interface implementation
- Line 2: @Override annotation for interface method
- Line 3: Method signature with return type and parameters
- Line 4: Actual business logic
- Line 8: Lambda expression - compact representation
- `()` - Empty parameter list since run() takes no parameters
- `->` - Lambda arrow operator separating parameters from body
- Right side - Method body as a single expression

---

## 2. Functional Interfaces {#functional-interfaces}

### Definition
A **functional interface** is an interface that contains exactly **one abstract method**. It may have multiple default or static methods, but only one abstract method.

### @FunctionalInterface Annotation

```java
@FunctionalInterface  // Optional but recommended annotation
public interface Calculator {
    // Only one abstract method allowed
    int calculate(int a, int b);
    
    // Default methods are allowed (multiple)
    default void printResult(int result) {
        System.out.println("Result: " + result);
    }
    
    // Static methods are allowed (multiple)
    static void info() {
        System.out.println("Calculator Interface");
    }
}
```

**Line-by-line Comments:**
- Line 1: `@FunctionalInterface` ensures compile-time checking
- Line 2: Interface declaration - must be public
- Line 4: Single abstract method - this is what lambda will implement
- Line 7: Default method with implementation - doesn't count as abstract
- Line 12: Static method with implementation - doesn't count as abstract

### Implementation Examples

```java
public class LambdaExample {
    public static void main(String[] args) {
        
        // Traditional approach - Anonymous Inner Class
        Calculator add = new Calculator() {
            @Override
            public int calculate(int a, int b) {
                return a + b;  // Addition logic
            }
        };
        
        // Lambda Expression - Concise approach
        Calculator multiply = (a, b) -> a * b;
        //                    ^^^^^ Parameters (type inferred)
        //                           ^^ Lambda operator
        //                              ^^^^^ Expression body
        
        // Lambda with block body (multiple statements)
        Calculator divide = (int x, int y) -> {
            //                ^^^ ^^^ Explicit type declaration (optional)
            if (y == 0) {
                throw new ArithmeticException("Division by zero");
            }
            return x / y;  // Multiple statements require return
        };
        
        // Testing the implementations
        System.out.println(add.calculate(10, 5));      // Output: 15
        System.out.println(multiply.calculate(10, 5)); // Output: 50
        System.out.println(divide.calculate(10, 5));   // Output: 2
    }
}
```

**Line-by-line Comments:**
- Lines 4-9: Traditional anonymous inner class implementation
- Line 12: Lambda with inferred parameter types and expression body
- Line 17: Lambda with explicit parameter types and block body
- Line 18-19: Block body allows multiple statements and validation
- Line 21: Explicit return statement required in block body
- Lines 25-27: Method calls work exactly the same regardless of implementation

---

## 3. Lambda Expression Syntax {#lambda-syntax}

### Syntax Variations

```java
// 1. No parameters
Runnable r = () -> System.out.println("No parameters");
//           ^^ Empty parentheses for no parameters

// 2. Single parameter (parentheses optional)
Consumer<String> c1 = (s) -> System.out.println(s);
Consumer<String> c2 = s -> System.out.println(s);  // Parentheses optional
//                    ^ Single parameter without parentheses

// 3. Multiple parameters
BiFunction<Integer, Integer, Integer> add = (a, b) -> a + b;
//                                          ^^^^^^ Multiple parameters in parentheses

// 4. Explicit type declaration
BiFunction<Integer, Integer, Integer> subtract = (Integer x, Integer y) -> x - y;
//                                               ^^^^^^^^^ ^^^^^^^^^ Explicit types

// 5. Block body with multiple statements
Function<String, String> formatter = (input) -> {
    String trimmed = input.trim();           // Statement 1
    String uppercase = trimmed.toUpperCase(); // Statement 2
    return "Formatted: " + uppercase;        // Return statement required
};

// 6. Method reference (alternative to lambda)
Function<String, Integer> length1 = s -> s.length();        // Lambda
Function<String, Integer> length2 = String::length;         // Method reference
//                                   ^^^^^^^^^^^^^^ Class::method syntax
```

**Line-by-line Comments:**
- Line 2: Empty `()` required when no parameters
- Line 5: Parentheses optional for single parameter
- Line 6: Cleaner syntax without parentheses
- Line 9: Multiple parameters must be enclosed in parentheses
- Line 12: Explicit type declaration (usually unnecessary due to type inference)
- Line 15-19: Block body enclosed in `{}` with explicit return
- Line 22: Traditional lambda expression
- Line 23: Method reference as shorthand for lambda

---

## 4. Built-in Functional Interfaces {#builtin-interfaces}

Java 8 provides several pre-defined functional interfaces in the `java.util.function` package.

### 4.1 Consumer\<T\>

**Purpose:** Accepts input but returns nothing (void). Used for operations that perform actions on input.

```java
import java.util.function.Consumer;
import java.util.Arrays;
import java.util.List;

public class ConsumerExample {
    public static void main(String[] args) {
        
        // Consumer that prints employee details
        Consumer<String> printer = (name) -> {
            System.out.println("Employee: " + name);  // Action performed
        };
        //                              ^^^^^^^^^^^^ No return value
        
        // Simplified version
        Consumer<String> simplePrinter = System.out::println;
        //                               ^^^^^^^^^^^^^^^^^^^^ Method reference
        
        // Using Consumer with a list
        List<String> employees = Arrays.asList("John", "Jane", "Bob", "Alice");
        
        // forEach accepts a Consumer
        employees.forEach(printer);        // Passes each element to Consumer
        //        ^^^^^^^ Built-in method that takes Consumer
        
        System.out.println("---");
        
        // Inline lambda Consumer
        employees.forEach(emp -> System.out.println("Name: " + emp.toUpperCase()));
        //                ^^^ Parameter name
        //                       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Action
        
        // Consumer chaining with andThen()
        Consumer<String> upperCase = s -> System.out.println(s.toUpperCase());
        Consumer<String> lowerCase = s -> System.out.println(s.toLowerCase());
        
        Consumer<String> combined = upperCase.andThen(lowerCase);
        //                                    ^^^^^^^ Chains two consumers
        combined.accept("Hello World");
        //       ^^^^^^ Executes both consumers in sequence
    }
}
```

**Output:**
```
Employee: John
Employee: Jane  
Employee: Bob
Employee: Alice
---
Name: JOHN
Name: JANE
Name: BOB
Name: ALICE
HELLO WORLD
hello world
```

### 4.2 Predicate\<T\>

**Purpose:** Represents a boolean-valued function. Used for conditional testing and filtering.

```java
import java.util.function.Predicate;
import java.util.List;
import java.util.Arrays;
import java.util.stream.Collectors;

public class PredicateExample {
    public static void main(String[] args) {
        
        // Predicate to check if salary is above threshold
        Predicate<Integer> highSalary = (salary) -> salary > 50000;
        //                              ^^^^^^^ Input parameter
        //                                       ^^ Boolean condition
        
        // Testing the predicate
        System.out.println(highSalary.test(60000)); // true
        System.out.println(highSalary.test(30000)); // false
        //                           ^^^^ Method to test condition
        
        // Predicate for string validation
        Predicate<String> nonEmpty = s -> s != null && !s.isEmpty();
        //                            ^ Single parameter
        //                                 ^^^^^^^^^^^^^^^^^^^^^^^ Boolean expression
        
        // Employee salary list
        List<Integer> salaries = Arrays.asList(30000, 60000, 45000, 80000, 25000);
        
        // Filter using predicate
        List<Integer> highSalaries = salaries.stream()
                                            .filter(highSalary)  // Uses predicate
                                            //      ^^^^^^^^^^ Predicate instance
                                            .collect(Collectors.toList());
        
        System.out.println("High Salaries: " + highSalaries); // [60000, 80000]
        
        // Inline predicate
        List<Integer> mediumSalaries = salaries.stream()
                                               .filter(s -> s >= 30000 && s <= 60000)
                                               //      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Inline predicate
                                               .collect(Collectors.toList());
        
        System.out.println("Medium Salaries: " + mediumSalaries); // [30000, 60000, 45000]
        
        // Predicate composition
        Predicate<Integer> lowSalary = salary -> salary < 40000;
        Predicate<Integer> notLowSalary = lowSalary.negate();  // Negation
        //                                          ^^^^^^ Built-in method
        
        Predicate<Integer> midRange = salary -> salary >= 30000;
        Predicate<Integer> highRange = salary -> salary <= 70000;
        Predicate<Integer> midToHigh = midRange.and(highRange);  // AND operation
        //                                     ^^^ Combines predicates
        
        // Testing combined predicate
        List<Integer> midToHighSalaries = salaries.stream()
                                                  .filter(midToHigh)
                                                  .collect(Collectors.toList());
        
        System.out.println("Mid to High: " + midToHighSalaries); // [30000, 60000, 45000]
    }
}
```

### 4.3 Supplier\<T\>

**Purpose:** Provides results without input parameters. Useful for lazy value generation.

```java
import java.util.function.Supplier;
import java.util.Random;
import java.time.LocalDateTime;

public class SupplierExample {
    public static void main(String[] args) {
        
        // Supplier that provides current timestamp
        Supplier<LocalDateTime> timeSupplier = () -> LocalDateTime.now();
        //                                     ^^ No parameters
        //                                        ^^ Returns current time
        
        System.out.println("Time 1: " + timeSupplier.get());
        //                                         ^^^ Method to get supplied value
        
        // Wait a bit and get time again
        try { Thread.sleep(1000); } catch (InterruptedException e) {}
        
        System.out.println("Time 2: " + timeSupplier.get());
        
        // Supplier for random numbers
        Supplier<Integer> randomSupplier = () -> {
            Random random = new Random();              // Setup code
            return random.nextInt(100);                // Return random value
        };
        //                           ^^^^^^^^^^^^ Block body with return
        
        // Generate multiple random numbers
        for (int i = 0; i < 5; i++) {
            System.out.println("Random " + (i+1) + ": " + randomSupplier.get());
            //                                                           ^^^ Each call generates new value
        }
        
        // Supplier for default configuration
        Supplier<String> configSupplier = () -> "default-config.properties";
        //                                       ^^^^^^^^^^^^^^^^^^^^^^^^^ Constant value
        
        // Lazy initialization example
        Supplier<ExpensiveObject> lazyInit = () -> new ExpensiveObject();
        //                                          ^^^^^^^^^^^^^^^^^^^ Created only when needed
        
        // Object created only when get() is called
        System.out.println("Before get() call");
        ExpensiveObject obj = lazyInit.get();  // Object created here
        System.out.println("After get() call");
        
        // Method reference as Supplier
        Supplier<Double> mathRandom = Math::random;
        //                            ^^^^^^^^^^^^ Method reference
        System.out.println("Math random: " + mathRandom.get());
    }
    
    // Helper class for demonstration
    static class ExpensiveObject {
        public ExpensiveObject() {
            System.out.println("ExpensiveObject created!");  // Constructor called
        }
    }
}
```

### 4.4 Function\<T, R\>

**Purpose:** Represents a function that accepts one argument and produces a result.

```java
import java.util.function.Function;
import java.util.List;
import java.util.Arrays;
import java.util.stream.Collectors;

public class FunctionExample {
    public static void main(String[] args) {
        
        // Function to convert string to its length
        Function<String, Integer> stringLength = s -> s.length();
        //       ^^^^^^  ^^^^^^^ Input type, Return type
        //                                        ^ Input parameter
        //                                             ^^^^^^^^ Return expression
        
        System.out.println("Length of 'Hello': " + stringLength.apply("Hello"));
        //                                                      ^^^^^ Method to apply function
        
        // Function to square a number
        Function<Integer, Integer> square = x -> x * x;
        System.out.println("Square of 5: " + square.apply(5)); // 25
        
        // Function with more complex logic
        Function<String, String> formatter = input -> {
            if (input == null) {                        // Validation
                return "NULL";
            }
            String trimmed = input.trim();              // Step 1: Trim
            String upperCased = trimmed.toUpperCase();  // Step 2: Upper case
            return "[" + upperCased + "]";             // Step 3: Format
        };
        
        System.out.println(formatter.apply("  hello world  "));  // [HELLO WORLD]
        
        // Using Function with Stream API
        List<String> names = Arrays.asList("john", "jane", "bob", "alice");
        
        // Map names to their lengths
        List<Integer> nameLengths = names.stream()
                                         .map(stringLength)  // Apply function to each element
                                         //   ^^^^^^^^^^^^ Function instance
                                         .collect(Collectors.toList());
        
        System.out.println("Name lengths: " + nameLengths); // [4, 4, 3, 5]
        
        // Inline function in map
        List<String> upperNames = names.stream()
                                       .map(name -> name.toUpperCase())
                                       //   ^^^^ ^^^^^^^^^^^^^^^^^^^ Inline function
                                       .collect(Collectors.toList());
        
        System.out.println("Upper names: " + upperNames); // [JOHN, JANE, BOB, ALICE]
        
        // Function composition
        Function<String, String> addPrefix = s -> "Mr. " + s;
        Function<String, String> addSuffix = s -> s + " Jr.";
        
        // Compose functions: first apply addPrefix, then addSuffix
        Function<String, String> fullFormatter = addPrefix.andThen(addSuffix);
        //                                                  ^^^^^^^ Apply second function to result of first
        
        System.out.println(fullFormatter.apply("John"));  // Mr. John Jr.
        
        // Compose in reverse order
        Function<String, String> reverseFormatter = addPrefix.compose(addSuffix);
        //                                                    ^^^^^^^ Apply first function to input, then second
        
        System.out.println(reverseFormatter.apply("John")); // Mr. John Jr.
        
        // Method reference as Function
        Function<String, String> trimFunction = String::trim;
        //                                      ^^^^^^^^^^^ Method reference
        System.out.println("'" + trimFunction.apply("  spaced  ") + "'"); // 'spaced'
    }
}
```

---

## 5. Stream API with Lambda Expressions {#stream-api}

The Stream API is one of the most powerful features of Java 8, especially when combined with lambda expressions.

### 5.1 Basic Stream Operations

```java
import java.util.*;
import java.util.stream.Collectors;

// Employee class for examples
class Employee {
    private String name;
    private int salary;
    private String department;
    
    public Employee(String name, int salary, String department) {
        this.name = name;
        this.salary = salary;
        this.department = department;
    }
    
    // Getters
    public String getName() { return name; }
    public int getSalary() { return salary; }
    public String getDepartment() { return department; }
    
    @Override
    public String toString() {
        return String.format("Employee{name='%s', salary=%d, dept='%s'}", name, salary, department);
    }
}

public class StreamExample {
    public static void main(String[] args) {
        
        // Sample data
        List<Employee> employees = Arrays.asList(
            new Employee("John", 60000, "IT"),
            new Employee("Jane", 75000, "HR"),
            new Employee("Bob", 45000, "IT"),
            new Employee("Alice", 80000, "Finance"),
            new Employee("Charlie", 55000, "HR")
        );
        
        System.out.println("=== FILTER OPERATION ===");
        
        // Filter employees with salary > 60000
        List<Employee> highEarners = employees.stream()  // Create stream from list
                                            .filter(emp -> emp.getSalary() > 60000)  // Intermediate operation
                                            //      ^^^ Each employee passed to lambda
                                            //           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Boolean condition
                                            .collect(Collectors.toList());  // Terminal operation
                                            //       ^^^^^^^^^^ Converts stream back to list
        
        highEarners.forEach(System.out::println);
        // Output: Jane (75000), Alice (80000)
        
        System.out.println("\n=== MAP OPERATION ===");
        
        // Map employees to their names (transformation)
        List<String> employeeNames = employees.stream()
                                             .map(emp -> emp.getName())  // Transform Employee to String
                                             //   ^^^ Input: Employee object
                                             //        ^^^^^^^^^^^^^^^^^^ Output: String (name)
                                             .collect(Collectors.toList());
        
        System.out.println("Employee Names: " + employeeNames);
        // Output: [John, Jane, Bob, Alice, Charlie]
        
        // Map to salary with method reference
        List<Integer> salaries = employees.stream()
                                         .map(Employee::getSalary)  // Method reference
                                         //   ^^^^^^^^^^^^^^^^^^^ Equivalent to emp -> emp.getSalary()
                                         .collect(Collectors.toList());
        
        System.out.println("Salaries: " + salaries);
        
        System.out.println("\n=== SORTED OPERATION ===");
        
        // Sort by salary (ascending)
        List<Employee> sortedBySalary = employees.stream()
                                                .sorted((e1, e2) -> Integer.compare(e1.getSalary(), e2.getSalary()))
                                                //      ^^^ ^^^ Two parameters for comparison
                                                //               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Comparison logic
                                                .collect(Collectors.toList());
        
        sortedBySalary.forEach(System.out::println);
        
        // Sort by salary using Comparator (cleaner)
        List<Employee> sortedBySalary2 = employees.stream()
                                                 .sorted(Comparator.comparing(Employee::getSalary))
                                                 //      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Pre-built comparator
                                                 .collect(Collectors.toList());
        
        System.out.println("\n=== REDUCE OPERATION ===");
        
        // Calculate total salary using reduce
        int totalSalary = employees.stream()
                                  .mapToInt(Employee::getSalary)  // Convert to IntStream
                                  //        ^^^^^^^^^^^^^^^^^^^ Extract salary as int
                                  .reduce(0, (sum, salary) -> sum + salary);  // Accumulate
                                  //      ^ Initial value
                                  //         ^^^ ^^^^^^ Accumulator parameters
                                  //              ^^^^^^^^^^^^^ Accumulation logic
        
        System.out.println("Total Salary: " + totalSalary);
        
        // Simpler way using sum()
        int totalSalary2 = employees.stream()
                                   .mapToInt(Employee::getSalary)
                                   .sum();  // Built-in sum method
        
        System.out.println("Total Salary (sum): " + totalSalary2);
        
        System.out.println("\n=== COMPLEX OPERATIONS ===");
        
        // Find highest paid employee in IT department
        Optional<Employee> highestPaidIT = employees.stream()
                                                   .filter(emp -> "IT".equals(emp.getDepartment()))  // Filter IT employees
                                                   //      ^^^ ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Department check
                                                   .max(Comparator.comparing(Employee::getSalary));  // Find maximum by salary
                                                   //   ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Max operation
        
        if (highestPaidIT.isPresent()) {  // Check if value exists
            System.out.println("Highest paid IT employee: " + highestPaidIT.get());
        }
        
        // Group employees by department
        Map<String, List<Employee>> byDepartment = employees.stream()
                                                           .collect(Collectors.groupingBy(Employee::getDepartment));
                                                           //       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Group by department
        
        byDepartment.forEach((dept, empList) -> {
            //                  ^^^^ ^^^^^^^ Key-Value pairs from map
            System.out.println(dept + ": " + empList.size() + " employees");
        });
    }
}
```

### 5.2 FlatMap Operation

**Purpose:** Flattens nested structures into a single stream.

```java
import java.util.*;
import java.util.stream.Collectors;

// Customer class with phone numbers
class Customer {
    private String name;
    private List<String> phoneNumbers;
    
    public Customer(String name, List<String> phoneNumbers) {
        this.name = name;
        this.phoneNumbers = phoneNumbers;
    }
    
    public String getName() { return name; }
    public List<String> getPhoneNumbers() { return phoneNumbers; }
    
    @Override
    public String toString() {
        return String.format("Customer{name='%s', phones=%s}", name, phoneNumbers);
    }
}

public class FlatMapExample {
    public static void main(String[] args) {
        
        // Sample data with nested phone numbers
        List<Customer> customers = Arrays.asList(
            new Customer("John", Arrays.asList("123-456-7890", "098-765-4321")),
            new Customer("Jane", Arrays.asList("555-123-4567")),
            new Customer("Bob", Arrays.asList("777-888-9999", "111-222-3333", "444-555-6666"))
        );
        
        System.out.println("=== MAP vs FLATMAP ===");
        
        // Using map() - Results in Stream<List<String>>
        List<List<String>> phoneListsWithMap = customers.stream()
                                                       .map(Customer::getPhoneNumbers)  // Each customer -> List<String>
                                                       //   ^^^^^^^^^^^^^^^^^^^^^^^^^ Returns List for each customer
                                                       .collect(Collectors.toList());
        
        System.out.println("With map() - nested lists:");
        phoneListsWithMap.forEach(System.out::println);
        // Output: 
        // [123-456-7890, 098-765-4321]
        // [555-123-4567]
        // [777-888-9999, 111-222-3333, 444-555-6666]
        
        // Using flatMap() - Results in Stream<String>
        List<String> allPhones = customers.stream()
                                         .flatMap(customer -> customer.getPhoneNumbers().stream())
                                         //       ^^^^^^^^ Input: Customer object
                                         //                 ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Convert List<String> to Stream<String>
                                         .collect(Collectors.toList());
        
        System.out.println("\nWith flatMap() - flattened:");
        allPhones.forEach(System.out::println);
        // Output: All phone numbers in a single list
        // 123-456-7890
        // 098-765-4321
        // 555-123-4567
        // 777-888-9999
        // 111-222-3333
        // 444-555-6666
        
        System.out.println("\n=== COMPLEX FLATMAP EXAMPLE ===");
        
        // Find all phone numbers that start with "555"
        List<String> phonesWith555 = customers.stream()
                                             .flatMap(c -> c.getPhoneNumbers().stream())  // Flatten to individual phones
                                             //       ^ Each customer
                                             //            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Stream of phone numbers
                                             .filter(phone -> phone.startsWith("555"))   // Filter specific pattern
                                             //      ^^^^^ Each individual phone number
                                             //             ^^^^^^^^^^^^^^^^^^^^^^^^^^^ Boolean condition
                                             .collect(Collectors.toList());
        
        System.out.println("Phones starting with 555: " + phonesWith555);
        
        // Count total phone numbers across all customers
        long totalPhoneCount = customers.stream()
                                       .flatMap(c -> c.getPhoneNumbers().stream())  // Flatten
                                       .count();  // Count elements in flattened stream
                                       //    ^^^^^ Terminal operation
        
        System.out.println("Total phone numbers: " + totalPhoneCount);
        
        // Example with nested arrays
        System.out.println("\n=== NESTED ARRAYS EXAMPLE ===");
        
        List<List<Integer>> nestedNumbers = Arrays.asList(
            Arrays.asList(1, 2, 3),
            Arrays.asList(4, 5),
            Arrays.asList(6, 7, 8, 9)
        );
        
        // Flatten and find sum
        int sum = nestedNumbers.stream()
                              .flatMap(List::stream)  // Convert each List<Integer> to Stream<Integer>
                              //       ^^^^^^^^^^^ Method reference to stream() method
                              .mapToInt(Integer::intValue)  // Convert to primitive int stream
                              .sum();  // Sum all numbers
        
        System.out.println("Sum of all nested numbers: " + sum);  // 45
        
        // Flatten and filter even numbers
        List<Integer> evenNumbers = nestedNumbers.stream()
                                                .flatMap(List::stream)        // Flatten
                                                .filter(n -> n % 2 == 0)      // Keep even numbers
                                                //      ^ Each individual number
                                                //           ^^^^^^^^^^^^^ Even check
                                                .collect(Collectors.toList());
        
        System.out.println("Even numbers: " + evenNumbers);  // [2, 4, 6, 8]
    }
}
```

---

## 6. Comparator with Lambda {#comparator}

### Custom Comparators with Lambda Expressions

```java
import java.util.*;
import java.util.stream.Collectors;

// Person class for comparison examples
class Person {
    private String name;
    private int age;
    private double salary;
    
    public Person(String name, int age, double salary) {
        this.name = name;
        this.age = age;
        this.salary = salary;
    }
    
    // Getters
    public String getName() { return name; }
    public int getAge() { return age; }
    public double getSalary() { return salary; }
    
    @Override
    public String toString() {
        return String.format("Person{name='%s', age=%d, salary=%.1f}", name, age, salary);
    }
}

public class ComparatorExample {
    public static void main(String[] args) {
        
        List<Person> people = Arrays.asList(
            new Person("John", 30, 60000.0),
            new Person("Jane", 25, 75000.0),
            new Person("Bob", 35, 45000.0),
            new Person("Alice", 28, 80000.0),
            new Person("Charlie", 32, 55000.0)
        );
        
        System.out.println("=== ORIGINAL LIST ===");
        people.forEach(System.out::println);
        
        System.out.println("\n=== SORT BY AGE (ASCENDING) ===");
        
        // Traditional way with lambda
        List<Person> sortedByAge = people.stream()
                                        .sorted((p1, p2) -> Integer.compare(p1.getAge(), p2.getAge()))
                                        //      ^^^ ^^^ Two objects to compare
                                        //               ^^^^^^^^^^^^^^^^^^^^ Comparison method for integers
                                        //                       ^^^^^^^^^^^ ^^^^^^^^^^^ Extract age from each person
                                        .collect(Collectors.toList());
        
        sortedByAge.forEach(System.out::println);
        
        System.out.println("\n=== SORT BY AGE USING COMPARATOR.COMPARING ===");
        
        // Cleaner way using Comparator.comparing
        List<Person> sortedByAge2 = people.stream()
                                         .sorted(Comparator.comparing(Person::getAge))
                                         //      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Method reference for extraction
                                         .collect(Collectors.toList());
        
        sortedByAge2.forEach(System.out::println);
        
        System.out.println("\n=== SORT BY SALARY (DESCENDING) ===");
        
        // Sort by salary in descending order
        List<Person> sortedBySalaryDesc = people.stream()
                                               .sorted(Comparator.comparing(Person::getSalary).reversed())
                                               //      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Extract salary
                                               //                                           ^^^^^^^^^^ Reverse natural order
                                               .collect(Collectors.toList());
        
        sortedBySalaryDesc.forEach(System.out::println);
        
        System.out.println("\n=== SORT BY NAME (CASE-INSENSITIVE) ===");
        
        // Sort by name ignoring case
        List<Person> sortedByName = people.stream()
                                         .sorted(Comparator.comparing(Person::getName, String.CASE_INSENSITIVE_ORDER))
                                         //      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Key extractor
                                         //                                        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Comparator for the key
                                         .collect(Collectors.toList());
        
        sortedByName.forEach(System.out::println);
        
        System.out.println("\n=== MULTIPLE CRITERIA SORTING ===");
        
        // Sort by age first, then by salary if age is same
        List<Person> multiSort = people.stream()
                                      .sorted(Comparator.comparing(Person::getAge)          // Primary sort
                                              //        ^^^^^^^^^^^^^^^^^^^^^^^^^ First criteria
                                              .thenComparing(Person::getSalary))             // Secondary sort
                                              // ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Second criteria (if first is equal)
                                      .collect(Collectors.toList());
        
        multiSort.forEach(System.out::println);
        
        System.out.println("\n=== COMPLEX COMPARATOR WITH LAMBDA ===");
        
        // Custom comparison logic with lambda
        Comparator<Person> customComparator = (p1, p2) -> {
            // First compare by age group (adult vs senior)
            boolean p1Adult = p1.getAge() < 30;              // Check if person 1 is adult
            boolean p2Adult = p2.getAge() < 30;              // Check if person 2 is adult
            
            if (p1Adult != p2Adult) {                        // If different age groups
                return p1Adult ? -1 : 1;                     // Adults come first
                //     ^^^^^^^^^ If p1 is adult, return -1 (p1 comes first)
                //                ^^^ Otherwise return 1 (p2 comes first)
            }
            
            // If same age group, compare by salary (descending)
            return Double.compare(p2.getSalary(), p1.getSalary());  // Higher salary first
            //     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Reverse order comparison
        };
        
        List<Person> customSorted = people.stream()
                                         .sorted(customComparator)
                                         .collect(Collectors.toList());
        
        customSorted.forEach(System.out::println);
        
        System.out.println("\n=== FINDING MIN/MAX WITH COMPARATOR ===");
        
        // Find oldest person
        Optional<Person> oldest = people.stream()
                                       .max(Comparator.comparing(Person::getAge));
                                       //   ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Find maximum by age
        
        oldest.ifPresent(p -> System.out.println("Oldest: " + p));
        //     ^^^^^^^^^ Execute if Optional contains value
        
        // Find highest paid person
        Optional<Person> highestPaid = people.stream()
                                            .max(Comparator.comparing(Person::getSalary));
        
        highestPaid.ifPresent(p -> System.out.println("Highest Paid: " + p));
        
        // Find person with shortest name
        Optional<Person> shortestName = people.stream()
                                             .min(Comparator.comparing(p -> p.getName().length()));
                                             //   ^^^^^^^^^^^^^^^^^^^^^^^^ ^^^^^^^^^^^^^^^^^^^^^^^^^^^ Extract name length
                                             //                           ^ Lambda to get derived property
        
        shortestName.ifPresent(p -> System.out.println("Shortest Name: " + p));
        
        System.out.println("\n=== NULL-SAFE COMPARATOR ===");
        
        // Create list with potential null values
        List<Person> peopleWithNulls = new ArrayList<>(people);
        peopleWithNulls.add(null);  // Add null element
        
        // Null-safe sorting (nulls last)
        List<Person> nullSafeSorted = peopleWithNulls.stream()
                                                     .sorted(Comparator.nullsLast(Comparator.comparing(Person::getName)))
                                                     //      ^^^^^^^^^^^^^^^^^ Handle null values
                                                     //                        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Normal comparator for non-null
                                                     .collect(Collectors.toList());
        
        System.out.println("Null-safe sorted:");
        nullSafeSorted.forEach(p -> System.out.println(p == null ? "NULL" : p.toString()));
    }
}
```

---

## 7. Optional Class {#optional}

The `Optional` class helps handle null values safely and avoid `NullPointerException`.

### 7.1 Basic Optional Usage

```java
import java.util.Optional;
import java.util.List;
import java.util.Arrays;

// Repository class to demonstrate Optional usage
class UserRepository {
    private List<User> users = Arrays.asList(
        new User("john@email.com", "John Doe", 30),
        new User("jane@email.com", "Jane Smith", 25),
        new User(null, "Bob Wilson", 35)  // User with null email
    );
    
    // Method that might not find a user - returns Optional
    public Optional<User> findByEmail(String email) {
        return users.stream()
                   .filter(user -> email != null && email.equals(user.getEmail()))
                   //      ^^^^ Each user in the stream
                   //             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Safe null check
                   .findFirst();  // Returns Optional<User>
                   //   ^^^^^^^^^ Terminal operation that returns Optional
    }
    
    // Method that might return null email - wrapped in Optional
    public Optional<String> getEmailById(String name) {
        return users.stream()
                   .filter(user -> name.equals(user.getName()))
                   .map(User::getEmail)          // Extract email (might be null)
                   //   ^^^^^^^^^^^^^^ Transform User to String (email)
                   .findFirst();                 // Optional<String>
    }
}

class User {
    private String email;
    private String name;
    private int age;
    
    public User(String email, String name, int age) {
        this.email = email;
        this.name = name;
        this.age = age;
    }
    
    public String getEmail() { return email; }
    public String getName() { return name; }
    public int getAge() { return age; }
    
    @Override
    public String toString() {
        return String.format("User{email='%s', name='%s', age=%d}", email, name, age);
    }
}

public class OptionalExample {
    public static void main(String[] args) {
        UserRepository repository = new UserRepository();
        
        System.out.println("=== BASIC OPTIONAL OPERATIONS ===");
        
        // Creating Optional values
        Optional<String> presentValue = Optional.of("Hello World");
        //                              ^^^^^^^^^^^ Creates Optional with non-null value
        
        Optional<String> emptyValue = Optional.empty();
        //                            ^^^^^^^^^^^^^^^^ Creates empty Optional
        
        Optional<String> nullableValue = Optional.ofNullable(null);
        //                               ^^^^^^^^^^^^^^^^^^^^^ Creates Optional that might be null
        
        System.out.println("Present: " + presentValue.isPresent());    // true
        System.out.println("Empty: " + emptyValue.isPresent());        // false
        System.out.println("Nullable: " + nullableValue.isPresent());  // false
        
        System.out.println("\n=== CHECKING AND RETRIEVING VALUES ===");
        
        // Safe way to check and get value
        if (presentValue.isPresent()) {     // Check if value exists
            String value = presentValue.get();  // Safe to call get()
            //             ^^^^^^^^^^^^^^^^^^ Get the actual value
            System.out.println("Value: " + value);
        }
        
        // Using ifPresent() with lambda
        presentValue.ifPresent(value -> System.out.println("Using ifPresent: " + value));
        //           ^^^^^^^^^ Execute lambda only if value is present
        //                     ^^^^^ The actual value passed to lambda
        
        emptyValue.ifPresent(value -> System.out.println("This won't print"));
        
        System.out.println("\n=== PROVIDING DEFAULTS ===");
        
        // Using orElse() to provide default value
        String defaultValue = emptyValue.orElse("Default String");
        //                               ^^^^^^^ Returns this if Optional is empty
        System.out.println("Default value: " + defaultValue);
        
        // Using orElseGet() with Supplier (lazy evaluation)
        String lazyDefault = emptyValue.orElseGet(() -> {
            System.out.println("Computing default value...");  // Only executed if empty
            return "Computed Default";
        });
        //                          ^^^^^^^^^^^^^^^^^^^^^^^^^ Supplier lambda
        System.out.println("Lazy default: " + lazyDefault);
        
        // orElseThrow() for exceptions
        try {
            String value = emptyValue.orElseThrow(() -> new RuntimeException("No value found"));
            //                        ^^^^^^^^^^^ Throw exception if empty
            //                                    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Exception supplier
        } catch (RuntimeException e) {
            System.out.println("Exception caught: " + e.getMessage());
        }
        
        System.out.println("\n=== REPOSITORY EXAMPLES ===");
        
        // Finding existing user
        Optional<User> foundUser = repository.findByEmail("john@email.com");
        foundUser.ifPresent(user -> System.out.println("Found: " + user));
        //        ^^^^^^^^^^^^ Execute if user found
        //                     ^^^^ The actual User object
        
        // Finding non-existing user
        Optional<User> notFound = repository.findByEmail("unknown@email.com");
        System.out.println("User found: " + notFound.isPresent());  // false
        
        // Providing default user
        User defaultUser = notFound.orElse(new User("default@email.com", "Default User", 0));
        //                          ^^^^^^^ Return default if not found
        System.out.println("Default user: " + defaultUser);
        
        System.out.println("\n=== CHAINING OPTIONAL OPERATIONS ===");
        
        // Chain operations on Optional
        Optional<String> emailResult = repository.findByEmail("john@email.com")
                                                .map(User::getName)           // Transform User to String (name)
                                                //   ^^^^^^^^^^^^^ Extract name if user exists
                                                .filter(name -> name.startsWith("J"))  // Keep only if starts with J
                                                //      ^^^^ The extracted name
                                                //             ^^^^^^^^^^^^^^^^^^^^^^^ Boolean condition
                                                .map(String::toUpperCase);    // Transform to uppercase
                                                //   ^^^^^^^^^^^^^^^^^^^^ Transform string
        
        emailResult.ifPresent(name -> System.out.println("Processed name: " + name));
        
        // Complex chaining with flatMap
        Optional<Integer> nameLength = repository.findByEmail("john@email.com")
                                                .map(User::getName)          // User -> String
                                                .filter(name -> !name.isEmpty())  // Keep non-empty names
                                                .map(String::length);        // String -> Integer
        
        nameLength.ifPresent(length -> System.out.println("Name length: " + length));
        
        System.out.println("\n=== HANDLING NULL VALUES SAFELY ===");
        
        // Getting email that might be null
        Optional<String> possibleEmail = repository.getEmailById("Bob Wilson");
        
        // Safe handling of potentially null email
        String emailOrDefault = possibleEmail.orElse("no-email@example.com");
        //                                    ^^^^^^^ Default for null email
        System.out.println("Email or default: " + emailOrDefault);
        
        // Transform and provide default in one chain
        String processedEmail = repository.findByEmail("john@email.com")
                                         .map(User::getEmail)              // Extract email
                                         .map(email -> email.toUpperCase()) // Transform to uppercase
                                         //   ^^^^^ Email string
                                         //          ^^^^^^^^^^^^^^^^^^^^^ Transform operation
                                         .orElse("NO EMAIL FOUND");        // Default if any step fails
        
        System.out.println("Processed email: " + processedEmail);
        
        System.out.println("\n=== OPTIONAL IN STREAMS ===");
        
        // Filter stream based on Optional values
        List<String> validEmails = Arrays.asList("john@email.com", "unknown@email.com", "jane@email.com")
                                         .stream()
                                         .map(repository::findByEmail)        // Stream<Optional<User>>
                                         //   ^^^^^^^^^^^^^^^^^^^^^^^ Method reference to findByEmail
                                         .filter(Optional::isPresent)         // Keep only present Optionals
                                         //      ^^^^^^^^^^^^^^^^^^^^ Filter out empty Optionals
                                         .map(Optional::get)                  // Extract User from Optional
                                         //   ^^^^^^^^^^^^^^ Get the actual User objects
                                         .map(User::getEmail)                 // Extract email from User
                                         .collect(Collectors.toList());
        
        System.out.println("Valid emails: " + validEmails);
    }
}
```

### 7.2 Advanced Optional Patterns

```java
import java.util.Optional;
import java.util.function.Function;

public class AdvancedOptionalExample {
    
    // Service class demonstrating Optional patterns
    static class OrderService {
        
        // Method that might fail - returns Optional
        public Optional<Order> findOrder(String orderId) {
            if ("ORDER-123".equals(orderId)) {
                return Optional.of(new Order(orderId, "Laptop", 999.99));
            }
            return Optional.empty();  // Order not found
        }
        
        // Method with multiple failure points
        public Optional<String> getOrderStatus(String orderId) {
            return findOrder(orderId)
                    .filter(order -> order.getAmount() > 0)     // Valid order amount
                    //      ^^^^^ Order object if present
                    //             ^^^^^^^^^^^^^^^^^^^^^^^^^^ Boolean condition
                    .map(order -> {                             // Transform to status
                        if (order.getAmount() > 500) {
                            return "PRIORITY";                  // High-value order
                        } else {
                            return "STANDARD";                  // Regular order
                        }
                    });
        }
        
        // Combining multiple Optional results
        public Optional<String> getCombinedInfo(String orderId) {
            Optional<Order> orderOpt = findOrder(orderId);
            Optional<String> statusOpt = getOrderStatus(orderId);
            
            // Both must be present to combine
            if (orderOpt.isPresent() && statusOpt.isPresent()) {
                Order order = orderOpt.get();
                String status = statusOpt.get();
                return Optional.of(order.getProduct() + " - " + status);
            }
            
            return Optional.empty();  // Either is missing
        }
        
        // Using flatMap for nested Optional operations
        public Optional<Double> getDiscountedPrice(String orderId) {
            return findOrder(orderId)
                    .flatMap(this::calculateDiscount);  // Returns Optional<Double>
                    //       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Method returns Optional, flatMap flattens
        }
        
        private Optional<Double> calculateDiscount(Order order) {
            if (order.getAmount() > 500) {
                return Optional.of(order.getAmount() * 0.9);  // 10% discount
                //     ^^^^^^^^^ Wrap in Optional
            }
            return Optional.empty();  // No discount available
        }
    }
    
    static class Order {
        private String id;
        private String product;
        private double amount;
        
        public Order(String id, String product, double amount) {
            this.id = id;
            this.product = product;
            this.amount = amount;
        }
        
        public String getId() { return id; }
        public String getProduct() { return product; }
        public double getAmount() { return amount; }
        
        @Override
        public String toString() {
            return String.format("Order{id='%s', product='%s', amount=%.2f}", id, product, amount);
        }
    }
    
    public static void main(String[] args) {
        OrderService orderService = new OrderService();
        
        System.out.println("=== OPTIONAL CHAINING ===");
        
        // Successful chain
        Optional<String> status = orderService.getOrderStatus("ORDER-123");
        status.ifPresent(s -> System.out.println("Order status: " + s));
        
        // Failed chain (order not found)
        Optional<String> noStatus = orderService.getOrderStatus("ORDER-999");
        System.out.println("Status present: " + noStatus.isPresent());  // false
        
        System.out.println("\n=== FLATMAP WITH OPTIONAL ===");
        
        // Using flatMap for nested Optional operations
        Optional<Double> discountedPrice = orderService.getDiscountedPrice("ORDER-123");
        discountedPrice.ifPresent(price -> 
            System.out.println("Discounted price: $" + String.format("%.2f", price))
        );
        //              ^^^^^ The calculated price value
        
        // No discount available
        Optional<Double> noDiscount = orderService.getDiscountedPrice("ORDER-999");
        System.out.println("Discount available: " + noDiscount.isPresent());
        
        System.out.println("\n=== OPTIONAL OR ALTERNATIVE ===");
        
        // Using or() to try alternative sources (Java 9+)
        // For Java 8 compatibility, we'll use a custom method
        Optional<String> result = getOrderInfo("ORDER-999")
                                    .or(() -> getAlternativeInfo("ORDER-999"));
        //                           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Alternative supplier
        
        result.ifPresentOrElse(
            info -> System.out.println("Found info: " + info),         // If present
            () -> System.out.println("No info found from any source")  // If empty
        );
        
        System.out.println("\n=== EXCEPTION HANDLING WITH OPTIONAL ===");
        
        // Safe exception handling
        Optional<Integer> parseResult = safeParseInt("123");
        parseResult.ifPresent(num -> System.out.println("Parsed number: " + num));
        
        // Failed parsing
        Optional<Integer> failedParse = safeParseInt("abc");
        int defaultValue = failedParse.orElse(-1);  // Provide default for invalid input
        System.out.println("Failed parse, using default: " + defaultValue);
        
        System.out.println("\n=== OPTIONAL WITH VALIDATION ===");
        
        // Validation chain with Optional
        Optional<String> validatedEmail = validateEmail("user@example.com");
        validatedEmail.ifPresentOrElse(
            email -> System.out.println("Valid email: " + email),
            () -> System.out.println("Invalid email format")
        );
        
        // Invalid email
        Optional<String> invalidEmail = validateEmail("invalid-email");
        String emailResult = invalidEmail.orElse("default@example.com");
        System.out.println("Email result: " + emailResult);
        
        System.out.println("\n=== PERFORMANCE CONSIDERATIONS ===");
        
        // orElse() vs orElseGet() - performance difference
        System.out.println("Testing orElse vs orElseGet:");
        
        Optional<String> presentOpt = Optional.of("Present");
        
        // orElse() always evaluates the default (even when not needed)
        String result1 = presentOpt.orElse(expensiveOperation());
        //                              ^^^^^^^^^^^^^^^^^^^^^^^^^^ Always called
        System.out.println("orElse result: " + result1);
        
        // orElseGet() only evaluates when needed (lazy)
        String result2 = presentOpt.orElseGet(() -> expensiveOperation());
        //                                     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Only called if empty
        System.out.println("orElseGet result: " + result2);
    }
    
    // Helper methods for examples
    
    private static Optional<String> getOrderInfo(String orderId) {
        // Simulate order lookup that fails
        return Optional.empty();
    }
    
    private static Optional<String> getAlternativeInfo(String orderId) {
        // Simulate alternative source
        return Optional.of("Alternative info for " + orderId);
    }
    
    private static Optional<Integer> safeParseInt(String str) {
        try {
            return Optional.of(Integer.parseInt(str));  // Successful parse
            //     ^^^^^^^^^ Wrap successful result
        } catch (NumberFormatException e) {
            return Optional.empty();  // Parse failed
        }
    }
    
    private static Optional<String> validateEmail(String email) {
        if (email != null && email.contains("@") && email.contains(".")) {
            return Optional.of(email);  // Valid email
        }
        return Optional.empty();  // Invalid format
    }
    
    private static String expensiveOperation() {
        System.out.println("Expensive operation called!");
        // Simulate expensive computation
        try { Thread.sleep(100); } catch (InterruptedException e) {}
        return "Expensive Result";
    }
}
```

---

## 8. Real-World Examples {#real-world}

### 8.1 Employee Management System

```java
import java.util.*;
import java.util.stream.Collectors;
import java.time.LocalDate;

// Employee class with comprehensive data
class Employee {
    private String id;
    private String name;
    private String department;
    private double salary;
    private LocalDate hireDate;
    private List<String> skills;
    
    public Employee(String id, String name, String department, double salary, LocalDate hireDate, List<String> skills) {
        this.id = id;
        this.name = name;
        this.department = department;
        this.salary = salary;
        this.hireDate = hireDate;
        this.skills = skills;
    }
    
    // Getters
    public String getId() { return id; }
    public String getName() { return name; }
    public String getDepartment() { return department; }
    public double getSalary() { return salary; }
    public LocalDate getHireDate() { return hireDate; }
    public List<String> getSkills() { return skills; }
    
    @Override
    public String toString() {
        return String.format("Employee{id='%s', name='%s', dept='%s', salary=%.0f, hireDate=%s}", 
                           id, name, department, salary, hireDate);
    }
}

// Department statistics class
class DepartmentStats {
    private String department;
    private long employeeCount;
    private double averageSalary;
    private double totalSalary;
    
    public DepartmentStats(String department, long employeeCount, double averageSalary, double totalSalary) {
        this.department = department;
        this.employeeCount = employeeCount;
        this.averageSalary = averageSalary;
        this.totalSalary = totalSalary;
    }
    
    // Getters and toString
    public String getDepartment() { return department; }
    public long getEmployeeCount() { return employeeCount; }
    public double getAverageSalary() { return averageSalary; }
    public double getTotalSalary() { return totalSalary; }
    
    @Override
    public String toString() {
        return String.format("DepartmentStats{dept='%s', count=%d, avgSalary=%.0f, totalSalary=%.0f}", 
                           department, employeeCount, averageSalary, totalSalary);
    }
}

public class EmployeeManagementSystem {
    
    // Sample data
    private static List<Employee> createSampleEmployees() {
        return Arrays.asList(
            new Employee("E001", "John Doe", "IT", 75000, LocalDate.of(2020, 1, 15), 
                        Arrays.asList("Java", "Spring", "AWS")),
            new Employee("E002", "Jane Smith", "HR", 65000, LocalDate.of(2019, 3, 20), 
                        Arrays.asList("Recruiting", "Training", "Communication")),
            new Employee("E003", "Bob Johnson", "IT", 80000, LocalDate.of(2018, 7, 10), 
                        Arrays.asList("Python", "Docker", "Kubernetes")),
            new Employee("E004", "Alice Williams", "Finance", 70000, LocalDate.of(2021, 5, 8), 
                        Arrays.asList("Accounting", "Excel", "SAP")),
            new Employee("E005", "Charlie Brown", "IT", 85000, LocalDate.of(2017, 11, 22), 
                        Arrays.asList("JavaScript", "React", "Node.js")),
            new Employee("E006", "Diana Davis", "HR", 68000, LocalDate.of(2020, 9, 14), 
                        Arrays.asList("Policy", "Benefits", "Compliance")),
            new Employee("E007", "Eva Garcia", "Finance", 72000, LocalDate.of(2019, 12, 3), 
                        Arrays.asList("Financial Analysis", "Reporting", "Budgeting"))
        );
    }
    
    public static void main(String[] args) {
        List<Employee> employees = createSampleEmployees();
        
        System.out.println("=== EMPLOYEE ANALYTICS WITH LAMBDA EXPRESSIONS ===");
        
        System.out.println("\n1. DEPARTMENT-WISE EMPLOYEE COUNT");
        // Group employees by department and count
        Map<String, Long> departmentCount = employees.stream()
                                                    .collect(Collectors.groupingBy(Employee::getDepartment, 
                                                    //       ^^^^^^^^^^^^^^^^^^^^^ Group by department
                                                            Collectors.counting()));
                                                    //       ^^^^^^^^^^^^^^^^^^ Count elements in each group
        
        departmentCount.forEach((dept, count) -> 
            System.out.println(dept + ": " + count + " employees"));
        //                  ^^^^ ^^^^^ Key-value pairs from the map
        
        System.out.println("\n2. TOP 3 HIGHEST PAID EMPLOYEES");
        // Find top 3 highest paid employees
        List<Employee> topEarners = employees.stream()
                                           .sorted(Comparator.comparing(Employee::getSalary).reversed())
                                           //      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Sort by salary descending
                                           .limit(3)  // Take first 3
                                           //    ^^^ Limit result size
                                           .collect(Collectors.toList());
        
        topEarners.forEach(System.out::println);
        
        System.out.println("\n3. AVERAGE SALARY BY DEPARTMENT");
        // Calculate average salary per department
        Map<String, Double> avgSalaryByDept = employees.stream()
                                                      .collect(Collectors.groupingBy(Employee::getDepartment,
                                                      //       ^^^^^^^^^^^^^^^^^^^^^ Group by department
                                                              Collectors.averagingDouble(Employee::getSalary)));
                                                      //       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Calculate average of salary
        
        avgSalaryByDept.forEach((dept, avgSalary) -> 
            System.out.printf("%s: $%.2f%n", dept, avgSalary));
        //                  ^^^^ ^^^^^^^^^ Department and average salary
        
        System.out.println("\n4. EMPLOYEES HIRED IN LAST 3 YEARS");
        LocalDate threeYearsAgo = LocalDate.now().minusYears(3);  // Calculate cutoff date
        
        List<Employee> recentHires = employees.stream()
                                            .filter(emp -> emp.getHireDate().isAfter(threeYearsAgo))
                                            //      ^^^ Each employee
                                            //           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Date comparison
                                            .sorted(Comparator.comparing(Employee::getHireDate).reversed())
                                            //      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Sort by hire date (newest first)
                                            .collect(Collectors.toList());
        
        System.out.println("Recent hires (last 3 years):");
        recentHires.forEach(System.out::println);
        
        System.out.println("\n5. SKILLS DISTRIBUTION");
        // Flatten all skills and count occurrences
        Map<String, Long> skillsCount = employees.stream()
                                                .flatMap(emp -> emp.getSkills().stream())
                                                //       ^^^ Each employee
                                                //                ^^^^^^^^^^^^^^^^^^^^^^^ Stream of skills for each employee
                                                .collect(Collectors.groupingBy(skill -> skill,
                                                //       ^^^^^^^^^^^^^^^^^^^ Group by skill name
                                                //                            ^^^^ ^^^^ Each skill
                                                        Collectors.counting()));
                                                //       ^^^^^^^^^^^^^^^^^^ Count occurrences
        
        // Sort skills by count (descending)
        skillsCount.entrySet().stream()
                  //          ^^^^^^ Stream of Map.Entry<String, Long>
                  .sorted(Map.Entry.<String, Long>comparingByValue().reversed())
                  //      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Sort by value (count) descending
                  .forEach(entry -> 
                      System.out.println(entry.getKey() + ": " + entry.getValue() + " employees"));
                  //                   ^^^^^^^^^^^^^^^ ^^^^^^^^^^^^^^^^^^ Key (skill) and value (count)
        
        System.out.println("\n6. DEPARTMENT STATISTICS");
        // Create comprehensive department statistics
        Map<String, DepartmentStats> deptStats = employees.stream()
                                                          .collect(Collectors.groupingBy(Employee::getDepartment))
                                                          //       ^^^^^^^^^^^^^^^^^^^^^ Group by department first
                                                          .entrySet().stream()
                                                          //          ^^^^^^ Convert to entry stream for processing
                                                          .collect(Collectors.toMap(
                                                              Map.Entry::getKey,  // Department name as key
                                                              //        ^^^^^^ Extract department name
                                                              entry -> {          // Calculate stats for each department
                                                                  List<Employee> deptEmployees = entry.getValue();
                                                                  //                              ^^^^^^^^^^^^^^^^^ List of employees in department
                                                                  long count = deptEmployees.size();
                                                                  double total = deptEmployees.stream()
                                                                                              .mapToDouble(Employee::getSalary)
                                                                                              //           ^^^^^^^^^^^^^^^^^^^ Extract salary
                                                                                              .sum();
                                                                  double average = total / count;
                                                                  return new DepartmentStats(entry.getKey(), count, average, total);
                                                                  //     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Create stats object
                                                              }
                                                          ));
        
        deptStats.values().forEach(System.out::println);
        //        ^^^^^^ Get all DepartmentStats objects
        
        System.out.println("\n7. EMPLOYEES WITH SPECIFIC SKILLS");
        String targetSkill = "Java";
        
        List<Employee> javaExperts = employees.stream()
                                             .filter(emp -> emp.getSkills().contains(targetSkill))
                                             //      ^^^ Each employee
                                             //              ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Check if skills list contains target
                                             .collect(Collectors.toList());
        
        System.out.println("Employees with " + targetSkill + " skills:");
        javaExperts.forEach(System.out::println);
        
        System.out.println("\n8. SALARY RANGE ANALYSIS");
        // Categorize employees by salary ranges
        Map<String, List<Employee>> salaryRanges = employees.stream()
                                                           .collect(Collectors.groupingBy(emp -> {
                                                               double salary = emp.getSalary();
                                                               //     ^^^^^^^^^^^^^^^^^^^^^^^^^ Extract salary
                                                               if (salary < 60000) return "Junior (< 60K)";
                                                               else if (salary < 75000) return "Mid (60K-75K)";
                                                               else return "Senior (75K+)";
                                                               //   ^^^^^^^^^^^^^^^^^^^^ Return category based on salary
                                                           }));
        
        salaryRanges.forEach((range, empList) -> {
            System.out.println(range + ": " + empList.size() + " employees");
            empList.forEach(emp -> System.out.println("  - " + emp.getName() + " ($" + (int)emp.getSalary() + ")"));
            //      ^^^^^^ List of employees in this range
            //              ^^^ Each employee in the range
        });
        
        System.out.println("\n9. MOST EXPERIENCED EMPLOYEE BY DEPARTMENT");
        // Find most senior employee in each department (by hire date)
        Map<String, Optional<Employee>> seniorByDept = employees.stream()
                                                               .collect(Collectors.groupingBy(Employee::getDepartment,
                                                               //       ^^^^^^^^^^^^^^^^^^^^^ Group by department
                                                                       Collectors.minBy(Comparator.comparing(Employee::getHireDate))));
                                                               //       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Find earliest hire date (most experienced)
        
        seniorByDept.forEach((dept, seniorEmp) -> {
            seniorEmp.ifPresent(emp -> {
                //        ^^^^^^^^^ Execute if employee found
                int experience = LocalDate.now().getYear() - emp.getHireDate().getYear();
                System.out.println(dept + " - Most experienced: " + emp.getName() + 
                                 " (" + experience + " years)");
            });
        });
        
        System.out.println("\n10. COMPLEX FILTERING AND TRANSFORMATION");
        // Find IT employees hired after 2018 with cloud skills, sorted by salary
        List<String> resultList = employees.stream()
                                          .filter(emp -> "IT".equals(emp.getDepartment()))              // IT department only
                                          //      ^^^ Each employee
                                          //              ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Department check
                                          .filter(emp -> emp.getHireDate().getYear() > 2018)           // Hired after 2018
                                          //              ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Date check
                                          .filter(emp -> emp.getSkills().stream()                      // Has cloud-related skills
                                          //              ^^^^^^^^^^^^^^^^^^ Stream of skills for employee
                                                           .anyMatch(skill -> skill.toLowerCase().contains("aws") ||
                                                           //        ^^^^^ Each skill
                                                           //                ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Case-insensitive cloud skill check
                                                                             skill.toLowerCase().contains("docker") ||
                                                                             skill.toLowerCase().contains("kubernetes")))
                                          .sorted(Comparator.comparing(Employee::getSalary).reversed()) // Sort by salary descending
                                          .map(emp -> String.format("%s - $%.0f (%s)", 
                                          //   ^^^ Transform Employee to formatted String
                                                                     emp.getName(), emp.getSalary(),
                                                                     String.join(", ", emp.getSkills())))
                                          //                       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Join skills with comma
                                          .collect(Collectors.toList());
        
        System.out.println("IT employees (hired after 2018) with cloud skills:");
        resultList.forEach(System.out::println);
        
        System.out.println("\n11. PERFORMANCE METRICS");
        // Calculate various performance metrics using lambda expressions
        
        // Total payroll
        double totalPayroll = employees.stream()
                                      .mapToDouble(Employee::getSalary)  // Convert to double stream
                                      .sum();                            // Sum all values
        
        System.out.printf("Total Company Payroll: $%.2f%n", totalPayroll);
        
        // Average years of experience
        double avgExperience = employees.stream()
                                       .mapToInt(emp -> LocalDate.now().getYear() - emp.getHireDate().getYear())
                                       //        ^^^ Transform to int stream
                                       //             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Calculate experience
                                       .average()      // Calculate average
                                       .orElse(0.0);   // Default if no elements
        
        System.out.printf("Average Experience: %.1f years%n", avgExperience);
        
        // Department with highest total payroll
        Optional<Map.Entry<String, Double>> highestPayrollDept = avgSalaryByDept.entrySet().stream()
                                                                               .max(Map.Entry.comparingByValue());
                                                                               //   ^^^^^^^^^^^^^^^^^^^^^^^^^^ Compare by value (salary)
        
        highestPayrollDept.ifPresent(entry -> 
            System.out.printf("Highest Average Salary Department: %s ($%.2f)%n", 
                            entry.getKey(), entry.getValue()));
        //                  ^^^^^^^^^^^^^^ ^^^^^^^^^^^^^^^^ Key (department) and value (average salary)
    }
}
```

### 8.2 E-commerce Order Processing System

```java
import java.util.*;
import java.util.stream.Collectors;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

// Order classes for e-commerce system
class Order {
    private String orderId;
    private String customerId;
    private LocalDateTime orderDate;
    private List<OrderItem> items;
    private OrderStatus status;
    private double shippingCost;
    
    public Order(String orderId, String customerId, LocalDateTime orderDate, 
                List<OrderItem> items, OrderStatus status, double shippingCost) {
        this.orderId = orderId;
        this.customerId = customerId;
        this.orderDate = orderDate;
        this.items = items;
        this.status = status;
        this.shippingCost = shippingCost;
    }
    
    // Calculate total order value
    public double getTotalValue() {
        return items.stream()
                   .mapToDouble(item -> item.getPrice() * item.getQuantity())  // Price * quantity for each item
                   //           ^^^^ Each OrderItem
                   //                 ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Calculate line total
                   .sum() + shippingCost;  // Add shipping cost
    }
    
    // Getters
    public String getOrderId() { return orderId; }
    public String getCustomerId() { return customerId; }
    public LocalDateTime getOrderDate() { return orderDate; }
    public List<OrderItem> getItems() { return items; }
    public OrderStatus getStatus() { return status; }
    public double getShippingCost() { return shippingCost; }
    
    @Override
    public String toString() {
        return String.format("Order{id='%s', customer='%s', date=%s, total=$%.2f, status=%s}",
                           orderId, customerId, orderDate.format(DateTimeFormatter.ofPattern("MM/dd/yyyy")),
                           getTotalValue(), status);
    }
}

class OrderItem {
    private String productId;
    private String productName;
    private int quantity;
    private double price;
    private String category;
    
    public OrderItem(String productId, String productName, int quantity, double price, String category) {
        this.productId = productId;
        this.productName = productName;
        this.quantity = quantity;
        this.price = price;
        this.category = category;
    }
    
    public double getLineTotal() {
        return price * quantity;
    }
    
    // Getters
    public String getProductId() { return productId; }
    public String getProductName() { return productName; }
    public int getQuantity() { return quantity; }
    public double getPrice() { return price; }
    public String getCategory() { return category; }
    
    @Override
    public String toString() {
        return String.format("OrderItem{product='%s', qty=%d, price=$%.2f, total=$%.2f}",
                           productName, quantity, price, getLineTotal());
    }
}

enum OrderStatus {
    PENDING, PROCESSING, SHIPPED, DELIVERED, CANCELLED
}

// Customer analytics class
class CustomerAnalytics {
    private String customerId;
    private int totalOrders;
    private double totalSpent;
    private double averageOrderValue;
    private LocalDateTime lastOrderDate;
    
    public CustomerAnalytics(String customerId, int totalOrders, double totalSpent, 
                           double averageOrderValue, LocalDateTime lastOrderDate) {
        this.customerId = customerId;
        this.totalOrders = totalOrders;
        this.totalSpent = totalSpent;
        this.averageOrderValue = averageOrderValue;
        this.lastOrderDate = lastOrderDate;
    }
    
    // Getters
    public String getCustomerId() { return customerId; }
    public int getTotalOrders() { return totalOrders; }
    public double getTotalSpent() { return totalSpent; }
    public double getAverageOrderValue() { return averageOrderValue; }
    public LocalDateTime getLastOrderDate() { return lastOrderDate; }
    
    @Override
    public String toString() {
        return String.format("Customer{id='%s', orders=%d, spent=$%.2f, avg=$%.2f, lastOrder=%s}",
                           customerId, totalOrders, totalSpent, averageOrderValue,
                           lastOrderDate.format(DateTimeFormatter.ofPattern("MM/dd/yyyy")));
    }
}

public class ECommerceAnalytics {
    
    // Create sample orders for demonstration
    private static List<Order> createSampleOrders() {
        LocalDateTime baseDate = LocalDateTime.now().minusDays(30);
        
        return Arrays.asList(
            new Order("ORD001", "CUST001", baseDate.plusDays(1),
                     Arrays.asList(
                         new OrderItem("P001", "Laptop", 1, 999.99, "Electronics"),
                         new OrderItem("P002", "Mouse", 2, 25.99, "Electronics")
                     ), OrderStatus.DELIVERED, 15.99),
            
            new Order("ORD002", "CUST002", baseDate.plusDays(3),
                     Arrays.asList(
                         new OrderItem("P003", "T-Shirt", 3, 19.99, "Clothing"),
                         new OrderItem("P004", "Jeans", 2, 49.99, "Clothing")
                     ), OrderStatus.SHIPPED, 8.99),
            
            new Order("ORD003", "CUST001", baseDate.plusDays(5),
                     Arrays.asList(
                         new OrderItem("P005", "Book", 5, 12.99, "Books"),
                         new OrderItem("P006", "Kindle", 1, 129.99, "Electronics")
                     ), OrderStatus.DELIVERED, 12.99),
            
            new Order("ORD004", "CUST003", baseDate.plusDays(7),
                     Arrays.asList(
                         new OrderItem("P007", "Coffee Maker", 1, 89.99, "Kitchen"),
                         new OrderItem("P008", "Coffee Beans", 4, 15.99, "Food")
                     ), OrderStatus.PROCESSING, 9.99),
            
            new Order("ORD005", "CUST002", baseDate.plusDays(10),
                     Arrays.asList(
                         new OrderItem("P009", "Headphones", 1, 199.99, "Electronics")
                     ), OrderStatus.CANCELLED, 0.0),
            
            new Order("ORD006", "CUST004", baseDate.plusDays(12),
                     Arrays.asList(
                         new OrderItem("P010", "Yoga Mat", 1, 39.99, "Sports"),
                         new OrderItem("P011", "Water Bottle", 2, 12.99, "Sports")
                     ), OrderStatus.DELIVERED, 7.99)
        );
    }
    
    public static void main(String[] args) {
        List<Order> orders = createSampleOrders();
        
        System.out.println("=== E-COMMERCE ANALYTICS WITH LAMBDA EXPRESSIONS ===");
        
        System.out.println("\n1. ORDER STATUS DISTRIBUTION");
        // Count orders by status
        Map<OrderStatus, Long> statusDistribution = orders.stream()
                                                          .collect(Collectors.groupingBy(Order::getStatus,
                                                          //       ^^^^^^^^^^^^^^^^^^^^^ Group by order status
                                                                  Collectors.counting()));
                                                          //       ^^^^^^^^^^^^^^^^^^ Count orders in each status
        
        statusDistribution.forEach((status, count) ->
            System.out.println(status + ": " + count + " orders"));
        
        System.out.println("\n2. TOP SELLING CATEGORIES");
        // Analyze sales by category (quantity sold)
        Map<String, Integer> categorySales = orders.stream()
                                                   .filter(order -> order.getStatus() != OrderStatus.CANCELLED)
                                                   //      ^^^^^ Filter out cancelled orders
                                                   //               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Status check
                                                   .flatMap(order -> order.getItems().stream())
                                                   //       ^^^^^ Each order
                                                   //                 ^^^^^^^^^^^^^^^^^^^^^^^^^^ Stream of items from each order
                                                   .collect(Collectors.groupingBy(OrderItem::getCategory,
                                                   //       ^^^^^^^^^^^^^^^^^^^^^ Group by product category
                                                           Collectors.summingInt(OrderItem::getQuantity)));
                                                   //       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Sum quantities for each category
        
        // Sort categories by sales volume
        categorySales.entrySet().stream()
                    .sorted(Map.Entry.<String, Integer>comparingByValue().reversed())
                    //      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Sort by quantity descending
                    .forEach(entry ->
                        System.out.println(entry.getKey() + ": " + entry.getValue() + " units sold"));
        
        System.out.println("\n3. REVENUE BY CATEGORY");
        // Calculate revenue by category
        Map<String, Double> categoryRevenue = orders.stream()
                                                   .filter(order -> order.getStatus() != OrderStatus.CANCELLED)
                                                   .flatMap(order -> order.getItems().stream())
                                                   .collect(Collectors.groupingBy(OrderItem::getCategory,
                                                           Collectors.summingDouble(OrderItem::getLineTotal)));
                                                   //       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Sum line totals (price * quantity)
        
        categoryRevenue.entrySet().stream()
                      .sorted(Map.Entry.<String, Double>comparingByValue().reversed())
                      .forEach(entry ->
                          System.out.printf("%s: $%.2f revenue%n", entry.getKey(), entry.getValue()));
        
        System.out.println("\n4. CUSTOMER ANALYTICS");
        // Generate comprehensive customer analytics
        Map<String, CustomerAnalytics> customerStats = orders.stream()
                                                            .filter(order -> order.getStatus() != OrderStatus.CANCELLED)
                                                            .collect(Collectors.groupingBy(Order::getCustomerId))
                                                            //       ^^^^^^^^^^^^^^^^^^^^^ Group orders by customer ID
                                                            .entrySet().stream()
                                                            .collect(Collectors.toMap(
                                                                Map.Entry::getKey,  // Customer ID
                                                                entry -> {          // Calculate customer analytics
                                                                    String customerId = entry.getKey();
                                                                    List<Order> customerOrders = entry.getValue();
                                                                    //                            ^^^^^^^^^^^^^^^^^ Orders for this customer
                                                                    
                                                                    int totalOrders = customerOrders.size();
                                                                    double totalSpent = customerOrders.stream()
                                                                                                      .mapToDouble(Order::getTotalValue)
                                                                                                      //           ^^^^^^^^^^^^^^^^^^^^ Extract total value
                                                                                                      .sum();
                                                                    double avgOrderValue = totalSpent / totalOrders;
                                                                    LocalDateTime lastOrder = customerOrders.stream()
                                                                                                           .map(Order::getOrderDate)
                                                                                                           //   ^^^^^^^^^^^^^^^^^ Extract order date
                                                                                                           .max(LocalDateTime::compareTo)
                                                                                                           //   ^^^^^^^^^^^^^^^^^^^^^^^ Find latest date
                                                                                                           .orElse(LocalDateTime.MIN);
                                                                    
                                                                    return new CustomerAnalytics(customerId, totalOrders,
                                                                                                totalSpent, avgOrderValue, lastOrder);
                                                                }
                                                            ));
        
        // Sort customers by total spent (descending)
        customerStats.values().stream()
                    .sorted(Comparator.comparing(CustomerAnalytics::getTotalSpent).reversed())
                    //      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Sort by total spent
                    .forEach(System.out::println);
        
        System.out.println("\n5. HIGH-VALUE ORDERS ANALYSIS");
        double highValueThreshold = 200.0;
        
        List<Order> highValueOrders = orders.stream()
                                           .filter(order -> order.getTotalValue() > highValueThreshold)
                                           //      ^^^^^ Each order
                                           //               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Value check
                                           .filter(order -> order.getStatus() != OrderStatus.CANCELLED)
                                           .sorted(Comparator.comparing(Order::getTotalValue).reversed())
                                           .collect(Collectors.toList());
        
        System.out.println("High-value orders (> $" + highValueThreshold + "):");
        highValueOrders.forEach(order -> {
            System.out.println(order);
            order.getItems().forEach(item -> System.out.println("  - " + item));
            //    ^^^^^^^^ List of items in the order
            //              ^^^^ Each item
        });
        
        System.out.println("\n6. MONTHLY SALES TREND");
        // Group orders by month and calculate totals
        Map<String, Double> monthlySales = orders.stream()
                                                 .filter(order -> order.getStatus() != OrderStatus.CANCELLED)
                                                 .collect(Collectors.groupingBy(
                                                     order -> order.getOrderDate().format(DateTimeFormatter.ofPattern("yyyy-MM")),
                                                     //       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Extract year-month
                                                     Collectors.summingDouble(Order::getTotalValue)));
                                                 //  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Sum total values
        
        monthlySales.forEach((month, total) ->
            System.out.printf("%s: $%.2f%n", month, total));
        
        System.out.println("\n7. PRODUCT PERFORMANCE");
        // Analyze individual product performance
        Map<String, Map<String, Object>> productMetrics = orders.stream()
                                                                .filter(order -> order.getStatus() != OrderStatus.CANCELLED)
                                                                .flatMap(order -> order.getItems().stream())
                                                                .collect(Collectors.groupingBy(OrderItem::getProductName,
                                                                //       ^^^^^^^^^^^^^^^^^^^^^ Group by product name
                                                                        Collectors.collectingAndThen(
                                                                            Collectors.toList(),  // Collect all items
                                                                            items -> {            // Transform collected items
                                                                                int totalQuantity = items.stream()
                                                                                                        .mapToInt(OrderItem::getQuantity)
                                                                                                        .sum();
                                                                                double totalRevenue = items.stream()
                                                                                                          .mapToDouble(OrderItem::getLineTotal)
                                                                                                          .sum();
                                                                                int orderCount = items.size();
                                                                                
                                                                                Map<String, Object> metrics = new HashMap<>();
                                                                                metrics.put("quantity", totalQuantity);
                                                                                metrics.put("revenue", totalRevenue);
                                                                                metrics.put("orders", orderCount);
                                                                                return metrics;
                                                                            }
                                                                        )));
        
        // Display top products by revenue
        productMetrics.entrySet().stream()
                     .sorted((e1, e2) -> Double.compare(
                         (Double) e2.getValue().get("revenue"),
                         (Double) e1.getValue().get("revenue")))
                     //  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Sort by revenue descending
                     .limit(5)  // Top 5 products
                     .forEach(entry -> {
                         String product = entry.getKey();
                         Map<String, Object> metrics = entry.getValue();
                         System.out.printf("%s - Qty: %d, Revenue: $%.2f, Orders: %d%n",
                                         product,
                                         metrics.get("quantity"),
                                         metrics.get("revenue"),
                                         metrics.get("orders"));
                     });
        
        System.out.println("\n8. ORDER PROCESSING EFFICIENCY");
        // Calculate average processing time by status
        long deliveredOrders = orders.stream()
                                    .filter(order -> order.getStatus() == OrderStatus.DELIVERED)
                                    .count();
        
        long totalOrders = orders.stream()
                                .filter(order -> order.getStatus() != OrderStatus.CANCELLED)
                                .count();
        
        double deliveryRate = (double) deliveredOrders / totalOrders * 100;
        
        System.out.printf("Delivery Success Rate: %.1f%% (%d/%d orders)%n",
                        deliveryRate, deliveredOrders, totalOrders);
        
        // Average order value
        double avgOrderValue = orders.stream()
                                    .filter(order -> order.getStatus() != OrderStatus.CANCELLED)
                                    .mapToDouble(Order::getTotalValue)
                                    .average()
                                    .orElse(0.0);
        
        System.out.printf("Average Order Value: $%.2f%n", avgOrderValue);
        
        // Total revenue
        double totalRevenue = orders.stream()
                                   .filter(order -> order.getStatus() != OrderStatus.CANCELLED)
                                   .mapToDouble(Order::getTotalValue)
                                   .sum();
        
        System.out.printf("Total Revenue: $%.2f%n", totalRevenue);
    }
}
```

---

## 9. Best Practices {#best-practices}

### 9.1 Lambda Expression Best Practices

```java
import java.util.*;
import java.util.function.*;
import java.util.stream.Collectors;

public class LambdaBestPractices {
    
    public static void main(String[] args) {
        List<String> words = Arrays.asList("apple", "banana", "cherry", "date", "elderberry");
        
        System.out.println("=== LAMBDA EXPRESSION BEST PRACTICES ===");
        
        System.out.println("\n1. PREFER METHOD REFERENCES WHEN POSSIBLE");
        
        // ❌ AVOID: Verbose lambda when method reference is available
        words.stream()
             .map(word -> word.toUpperCase())        // Unnecessary lambda
             //   ^^^^ ^^^^^^^^^^^^^^^^^^^ Can be simplified
             .forEach(word -> System.out.println(word));  // Unnecessary lambda
        
        // ✅ PREFER: Method references for cleaner code
        words.stream()
             .map(String::toUpperCase)               // Method reference
             //   ^^^^^^^^^^^^^^^^^^^ Cleaner and more readable
             .forEach(System.out::println);         // Method reference
             //        ^^^^^^^^^^^^^^^^^ Direct method reference
        
        System.out.println("\n2. KEEP LAMBDAS SHORT AND FOCUSED");
        
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        
        // ❌ AVOID: Complex logic in lambda (hard to read and test)
        List<String> results = numbers.stream()
                                     .filter(n -> {
                                         // Complex logic in lambda - hard to maintain
                                         if (n % 2 == 0) {
                                             if (n > 5) {
                                                 return n % 3 == 0;
                                             }
                                         }
                                         return false;
                                     })
                                     .map(n -> "Number: " + n + " is valid")
                                     .collect(Collectors.toList());
        
        // ✅ PREFER: Extract complex logic to separate methods
        List<String> betterResults = numbers.stream()
                                           .filter(LambdaBestPractices::isComplexConditionMet)  // Method reference
                                           //      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Clear method name
                                           .map(LambdaBestPractices::formatValidNumber)         // Method reference
                                           .collect(Collectors.toList());
        
        System.out.println("Filtered results: " + betterResults);
        
        System.out.println("\n3. USE APPROPRIATE FUNCTIONAL INTERFACES");
        
        // ❌ AVOID: Using generic Function when specific interface exists
        Function<String, Void> printer = s -> {
            System.out.println(s);
            return null;  // Awkward return for void operation
            //     ^^^^ Unnecessary return value
        };
        
        // ✅ PREFER: Use Consumer for operations that don't return values
        Consumer<String> betterPrinter = System.out::println;
        //       ^^^^^^^^^^^^^^^^^^^^^^ Consumer is designed for void operations
        words.forEach(betterPrinter);
        
        System.out.println("\n4. HANDLE EXCEPTIONS PROPERLY");
        
        List<String> numberStrings = Arrays.asList("1", "2", "abc", "4", "5");
        
        // ❌ AVOID: Ignoring exceptions in lambdas
        List<Integer> parsed = numberStrings.stream()
                                           .map(s -> {
                                               try {
                                                   return Integer.parseInt(s);
                                               } catch (NumberFormatException e) {
                                                   return null;  // Poor error handling
                                                   //     ^^^^ Null values can cause issues later
                                               }
                                           })
                                           .filter(Objects::nonNull)  // Need to filter nulls
                                           .collect(Collectors.toList());
        
        // ✅ PREFER: Use Optional for better error handling
        List<Integer> betterParsed = numberStrings.stream()
                                                 .map(LambdaBestPractices::safeParseInt)  // Returns Optional
                                                 //   ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Safe parsing method
                                                 .filter(Optional::isPresent)            // Filter present values
                                                 .map(Optional::get)                     // Extract values
                                                 .collect(Collectors.toList());
        
        System.out.println("Safely parsed numbers: " + betterParsed);
        
        System.out.println("\n5. AVOID SIDE EFFECTS IN LAMBDAS");
        
        List<String> items = Arrays.asList("item1", "item2", "item3", "item4");
        List<String> processed = new ArrayList<>();
        
        // ❌ AVOID: Side effects in stream operations (not thread-safe, unclear intent)
        items.stream()
             .filter(item -> item.length() > 4)
             .forEach(item -> processed.add(item.toUpperCase()));  // Side effect
             //               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Modifying external state
        
        // ✅ PREFER: Use collect() to gather results
        List<String> betterProcessed = items.stream()
                                           .filter(item -> item.length() > 4)
                                           .map(String::toUpperCase)
                                           .collect(Collectors.toList());  // Functional approach
                                           //       ^^^^^^^^^^^^^^^^^^^^ No side effects
        
        System.out.println("Processed items: " + betterProcessed);
        
        System.out.println("\n6. USE PARALLEL STREAMS JUDICIOUSLY");
        
        List<Integer> largeList = generateLargeList(1000);
        
        // Consider parallel processing for CPU-intensive operations on large datasets
        long startTime = System.nanoTime();
        
        // Sequential processing
        long sequentialResult = largeList.stream()
                                        .filter(n -> isPrime(n))           // CPU-intensive operation
                                        //      ^ Each number
                                        //           ^^^^^^^^^^^ Prime checking logic
                                        .count();
        
        long sequentialTime = System.nanoTime() - startTime;
        
        startTime = System.nanoTime();
        
        // Parallel processing (use carefully - overhead might not be worth it for small operations)
        long parallelResult = largeList.parallelStream()     // Parallel stream
                                      //^^^^^^^^^^^^^^^ Use multiple threads
                                      .filter(n -> isPrime(n))
                                      .count();
        
        long parallelTime = System.nanoTime() - startTime;
        
        System.out.printf("Sequential: %d primes in %.2f ms%n", sequentialResult, sequentialTime / 1_000_000.0);
        System.out.printf("Parallel: %d primes in %.2f ms%n", parallelResult, parallelTime / 1_000_000.0);
        
        System.out.println("\n7. PREFER IMMUTABILITY");
        
        // ✅ PREFER: Working with immutable data
        List<Person> people = Arrays.asList(
            new Person("John", 30),
            new Person("Jane", 25),
            new Person("Bob", 35)
        );
        
        // Transform to new objects rather than modifying existing ones
        List<Person> adults = people.stream()
                                   .filter(person -> person.age >= 18)      // Filter adults
                                   .map(person -> new Person(               // Create new objects
                                       person.name.toUpperCase(),   // Transform name
                                       person.age))                 // Keep age
                                   .collect(Collectors.toList());   // Collect immutable result
        
        adults.forEach(System.out::println);
        
        System.out.println("\n8. USE APPROPRIATE COLLECTORS");
        
        Map<String, Integer> nameToAge = people.stream()
                                              .collect(Collectors.toMap(
                                                  Person::getName,           // Key mapper
                                                  Person::getAge,            // Value mapper
                                                  (existing, replacement) -> existing  // Merge function for duplicates
                                                  //^^^^^^^^^ ^^^^^^^^^^^ Handle key conflicts
                                              ));
        
        // Grouping with custom collectors
        Map<String, String> ageGroups = people.stream()
                                             .collect(Collectors.groupingBy(
                                                 person -> person.age < 30 ? "Young" : "Mature",  // Classifier
                                                 Collectors.mapping(        // Downstream collector
                                                     Person::getName,       // Transform to name
                                                     Collectors.joining(", ")  // Join names with comma
                                                 )));
        
        System.out.println("Age groups: " + ageGroups);
    }
    
    // Helper methods for best practices examples
    
    private static boolean isComplexConditionMet(int n) {
        // Extract complex logic to separate method for clarity and testability
        return n % 2 == 0 && n > 5 && n % 3 == 0;
        //     ^^^^^^^^^ Even number check
        //                   ^^^^^ Greater than 5
        //                          ^^^^^^^^^ Divisible by 3
    }
    
    private static String formatValidNumber(int n) {
        return "Number: " + n + " is valid";
        //     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Clear formatting logic
    }
    
    private static Optional<Integer> safeParseInt(String s) {
        try {
            return Optional.of(Integer.parseInt(s));  // Successful parse
            //     ^^^^^^^^^ Wrap in Optional
        } catch (NumberFormatException e) {
            return Optional.empty();  // Return empty Optional for invalid input
            //     ^^^^^^^^^^^^^^^^^ No exception propagation
        }
    }
    
    private static List<Integer> generateLargeList(int size) {
        return java.util.stream.IntStream.range(1, size)
                                        .boxed()           // Convert to Integer objects
                                        .collect(Collectors.toList());
    }
    
    private static boolean isPrime(int n) {
        if (n < 2) return false;
        if (n == 2) return true;
        if (n % 2 == 0) return false;
        
        for (int i = 3; i * i <= n; i += 2) {  // CPU-intensive operation
            if (n % i == 0) return false;
        }
        return true;
    }
    
    // Simple Person class for examples
    static class Person {
        final String name;  // Immutable field
        final int age;      // Immutable field
        
        Person(String name, int age) {
            this.name = name;
            this.age = age;
        }
        
        String getName() { return name; }
        int getAge() { return age; }
        
        @Override
        public String toString() {
            return String.format("Person{name='%s', age=%d}", name, age);
        }
    }
}
```

### 9.2 Performance Considerations

```java
import java.util.*;
import java.util.function.Predicate;
import java.util.stream.Collectors;
import java.util.stream.IntStream;

public class PerformanceConsiderations {
    
    public static void main(String[] args) {
        System.out.println("=== LAMBDA PERFORMANCE CONSIDERATIONS ===");
        
        System.out.println("\n1. LAMBDA VS ANONYMOUS CLASS PERFORMANCE");
        
        List<Integer> numbers = IntStream.range(1, 1_000_000)
                                       .boxed()
                                       .collect(Collectors.toList());
        
        // Measure lambda performance
        long startTime = System.nanoTime();
        
        long evenCountLambda = numbers.stream()
                                     .filter(n -> n % 2 == 0)    // Lambda expression
                                     //      ^^^^^^^^^^^^ Simple predicate
                                     .count();
        
        long lambdaTime = System.nanoTime() - startTime;
        
        // Measure anonymous class performance  
        startTime = System.nanoTime();
        
        long evenCountAnonymous = numbers.stream()
                                        .filter(new Predicate<Integer>() {  // Anonymous class
                                            @Override
                                            public boolean test(Integer n) {
                                                return n % 2 == 0;          // Same logic
                                            }
                                        })
                                        .count();
        
        long anonymousTime = System.nanoTime() - startTime;
        
        // Measure method reference performance
        startTime = System.nanoTime();
        
        long evenCountMethodRef = numbers.stream()
                                        .filter(PerformanceConsiderations::isEven)  // Method reference
                                        //      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Static method reference
                                        .count();
        
        long methodRefTime = System.nanoTime() - startTime;
        
        System.out.printf("Lambda: %d evens in %.2f ms%n", evenCountLambda, lambdaTime / 1_000_000.0);
        System.out.printf("Anonymous: %d evens in %.2f ms%n", evenCountAnonymous, anonymousTime / 1_000_000.0);  
        System.out.printf("Method Ref: %d evens in %.2f ms%n", evenCountMethodRef, methodRefTime / 1_000_000.0);
        
        System.out.println("\n2. STREAM VS LOOP PERFORMANCE");
        
        List<String> words = generateWordList(100_000);
        
        // Traditional for-each loop
        startTime = System.nanoTime();
        
        List<String> upperCaseLoop = new ArrayList<>();
        for (String word : words) {                    // Traditional loop
            if (word.length() > 5) {                   // Filter condition
                upperCaseLoop.add(word.toUpperCase()); // Transform and add
            }
        }
        
        long loopTime = System.nanoTime() - startTime;
        
        // Stream API
        startTime = System.nanoTime();
        
        List<String> upperCaseStream = words.stream()
                                           .filter(word -> word.length() > 5)      // Filter
                                           //      ^^^^ ^^^^^^^^^^^^^^^^^^^ Same condition
                                           .map(String::toUpperCase)               // Transform
                                           .collect(Collectors.toList());          // Collect
        
        long streamTime = System.nanoTime() - startTime;
        
        // Parallel stream
        startTime = System.nanoTime();
        
        List<String> upperCaseParallel = words.parallelStream()   // Parallel processing
                                             //^^^^^^^^^^^^^^^ Use multiple threads
                                             .filter(word -> word.length() > 5)
                                             .map(String::toUpperCase)
                                             .collect(Collectors.toList());
        
        long parallelTime = System.nanoTime() - startTime;
        
        System.out.printf("Loop: %d results in %.2f ms%n", upperCaseLoop.size(), loopTime / 1_000_000.0);
        System.out.printf("Stream: %d results in %.2f ms%n", upperCaseStream.size(), streamTime / 1_000_000.0);
        System.out.printf("Parallel: %d results in %.2f ms%n", upperCaseParallel.size(), parallelTime / 1_000_000.0);
        
        System.out.println("\n3. BOXING/UNBOXING PERFORMANCE");
        
        // ❌ AVOID: Boxing in primitive operations
        startTime = System.nanoTime();
        
        List<Integer> boxedNumbers = IntStream.range(1, 1_000_000)
                                            .boxed()                    // Convert int to Integer
                                            //^^^^^^ Boxing operation - expensive
                                            .filter(n -> n % 2 == 0)    // Unboxing for comparison
                                            .collect(Collectors.toList());
        
        long boxedTime = System.nanoTime() - startTime;
        
        // ✅ PREFER: Primitive streams when possible
        startTime = System.nanoTime();
        
        int[] primitiveResults = IntStream.range(1, 1_000_000)
                                        .filter(n -> n % 2 == 0)    // No boxing/unboxing
                                        //      ^^^^^^^^^^^^ Primitive operations
                                        .toArray();                 // Direct to array
        
        long primitiveTime = System.nanoTime() - startTime;
        
        System.out.printf("Boxed: %d results in %.2f ms%n", boxedNumbers.size(), boxedTime / 1_000_000.0);
        System.out.printf("Primitive: %d results in %.2f ms%n", primitiveResults.length, primitiveTime / 1_000_000.0);
        
        System.out.println("\n4. LAZY EVALUATION BENEFITS");
        
        List<String> largeWordList = generateWordList(1_000_000);
        
        // Lazy evaluation - only processes what's needed
        startTime = System.nanoTime();
        
        Optional<String> firstLongWord = largeWordList.stream()
                                                     .filter(word -> word.length() > 10)    // Filter
                                                     //      ^^^^ ^^^^^^^^^^^^^^^^^^^^^ Only applied as needed
                                                     .map(String::toUpperCase)               // Transform
                                                     //   ^^^^^^^^^^^^^^^^^^^ Only applied to filtered items
                                                     .findFirst();                           // Short-circuit operation
                                                     //   ^^^^^^^^^ Stops at first match
        
        long lazyTime = System.nanoTime() - startTime;
        
        // Eager evaluation - processes all items
        startTime = System.nanoTime();
        
        List<String> allLongWords = largeWordList.stream()
                                                .filter(word -> word.length() > 10)     // Process all
                                                .map(String::toUpperCase)                // Transform all
                                                .collect(Collectors.toList());           // Collect all
        
        String firstEager = allLongWords.isEmpty() ? null : allLongWords.get(0);
        
        long eagerTime = System.nanoTime() - startTime;
        
        System.out.printf("Lazy (findFirst): Found in %.2f ms%n", lazyTime / 1_000_000.0);
        System.out.printf("Eager (collect all): %d results in %.2f ms%n", allLongWords.size(), eagerTime / 1_000_000.0);
        
        System.out.println("\n5. COLLECTION SIZE CONSIDERATIONS");
        
        testCollectionSize(1_000, "Small");      // Small collection
        testCollectionSize(100_000, "Medium");   // Medium collection
        testCollectionSize(1_000_000, "Large");  // Large collection
        
        System.out.println("\n6. MEMORY USAGE PATTERNS");
        
        // Memory-efficient processing with streams
        long memoryBefore = getUsedMemory();
        
        // Process large dataset without storing intermediate results
        long result = IntStream.range(1, 1_000_000)
                             .filter(n -> n % 3 == 0)       // Filter multiples of 3
                             .map(n -> n * n)               // Square them
                             .filter(n -> n > 100)          // Filter large squares  
                             .count();                      // Count final results
                             //   ^^^^^ No intermediate collections stored
        
        long memoryAfter = getUsedMemory();
        
        System.out.printf("Stream processing: %d results, Memory used: %d KB%n", 
                        result, (memoryAfter - memoryBefore) / 1024);
        
        // Compare with collecting intermediate results
        memoryBefore = getUsedMemory();
        
        List<Integer> step1 = IntStream.range(1, 1_000_000)
                                     .filter(n -> n % 3 == 0)
                                     .boxed()                    // Store intermediate result
                                     .collect(Collectors.toList());
        
        List<Integer> step2 = step1.stream()
                                  .map(n -> n * n)
                                  .collect(Collectors.toList()); // Another intermediate result
        
        long result2 = step2.stream()
                           .filter(n -> n > 100)
                           .count();
        
        memoryAfter = getUsedMemory();
        
        System.out.printf("Multi-step: %d results, Memory used: %d KB%n", 
                        result2, (memoryAfter - memoryBefore) / 1024);
    }
    
    // Helper methods
    
    private static boolean isEven(Integer n) {
        return n % 2 == 0;
        //     ^^^^^^^^^ Simple predicate logic
    }
    
    private static List<String> generateWordList(int size) {
        Random random = new Random(42);  // Fixed seed for consistent results
        List<String> words = new ArrayList<>();
        
        for (int i = 0; i < size; i++) {
            words.add(generateRandomWord(random));
        }
        return words;
    }
    
    private static String generateRandomWord(Random random) {
        int length = random.nextInt(15) + 3;  // Words of 3-17 characters
        StringBuilder word = new StringBuilder();
        
        for (int i = 0; i < length; i++) {
            word.append((char) ('a' + random.nextInt(26)));  // Random lowercase letter
        }
        return word.toString();
    }
    
    private static void testCollectionSize(int size, String label) {
        List<Integer> numbers = IntStream.range(1, size)
                                       .boxed()
                                       .collect(Collectors.toList());
        
        // Test stream performance on different sizes
        long startTime = System.nanoTime();
        
        long evenCount = numbers.stream()
                               .filter(n -> n % 2 == 0)
                               .count();
        
        long streamTime = System.nanoTime() - startTime;
        
        // Test parallel stream performance
        startTime = System.nanoTime();
        
        long evenCountParallel = numbers.parallelStream()
                                       .filter(n -> n % 2 == 0)
                                       .count();
        
        long parallelTime = System.nanoTime() - startTime;
        
        System.out.printf("%s (%d items): Sequential %.2f ms, Parallel %.2f ms%n",
                        label, size, streamTime / 1_000_000.0, parallelTime / 1_000_000.0);
    }
    
    private static long getUsedMemory() {
        Runtime runtime = Runtime.getRuntime();
        return runtime.totalMemory() - runtime.freeMemory();
        //     ^^^^^^^^^^^^^^^^^^^ ^^^^^^^^^^^^^^^^^^ Calculate used memory
    }
}
```

---

## Summary

### Key Concepts Mastered

| Feature | Description | Best Use Case |
|---------|-------------|---------------|
| **Lambda Expressions** | Anonymous functions for functional interfaces | Simplifying callback code, stream operations |
| **Method References** | Shorthand for lambda expressions | When lambda just calls existing method |
| **Consumer** | Accepts input, returns void | Printing, logging, side-effect operations |
| **Predicate** | Returns boolean result | Filtering, validation, condition checking |
| **Function** | Transforms input to output | Data transformation, mapping operations |
| **Supplier** | Provides value without input | Lazy initialization, default value generation |
| **Stream API** | Functional data processing | Complex data transformations and aggregations |
| **Optional** | Safe null handling | Avoiding NullPointerException, explicit absence |

### Performance Guidelines

1. **Use primitive streams** (IntStream, LongStream, DoubleStream) for better performance
2. **Prefer method references** over lambda expressions when possible
3. **Avoid side effects** in stream operations for thread safety
4. **Consider parallel streams** only for CPU-intensive operations on large datasets
5. **Use lazy evaluation** benefits with operations like findFirst(), anyMatch()
6. **Be mindful of boxing/unboxing** costs with wrapper types

### Common Pitfalls to Avoid

- ❌ Complex logic in lambda expressions
- ❌ Side effects in stream operations  
- ❌ Unnecessary boxing/unboxing of primitives
- ❌ Using parallel streams for small datasets
- ❌ Ignoring exceptions in lambda expressions
- ❌ Creating lambdas when method references suffice

### Best Practices Summary

- ✅ Keep lambda expressions short and focused
- ✅ Extract complex logic to separate methods
- ✅ Use appropriate functional interfaces
- ✅ Handle exceptions with Optional or proper error handling
- ✅ Prefer immutability and functional programming principles
- ✅ Use meaningful variable names even in short lambdas
- ✅ Test lambda expressions as thoroughly as other code

This comprehensive guide covers Java 8 Lambda Expressions and Functional Interfaces with detailed examples and line-by-line explanations to help you master functional programming in Java.
                                                                  