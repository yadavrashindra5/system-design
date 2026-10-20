# SOLID Principles — Java Practice Assignments

## Goal

Practice the five SOLID principles using simple Java programs.

### SOLID Quick Reference

| Principle | Meaning |
|---|---|
| SRP | One class should have one main responsibility. |
| OCP | Add new functionality without constantly modifying existing code. |
| LSP | A child type should work wherever its parent type is expected. |
| ISP | Don't force a class to implement methods it doesn't need. |
| DIP | Depend on abstractions rather than concrete implementations. |
| DI | Provide required objects from outside a class. |

**Instructions:**
- Solve each assignment yourself before looking for help.
- Use plain Java first. You do not need Spring Boot.
- Create a separate Java file or project for each assignment.
- Write a `main()` method to test your solution.
- Focus on understanding why you are making each design decision.

---

# Level 1 — Easy

## Assignment 1: Student Management System

**Principle:** Single Responsibility Principle (SRP)

### Problem

You have the following class:

```java
class Student {
    void saveStudent() {
        System.out.println("Student saved");
    }

    void generateReport() {
        System.out.println("Report generated");
    }

    void sendEmail() {
        System.out.println("Email sent");
    }
}
```

The class performs three different jobs.

### Your tasks

- Identify the responsibilities of the `Student` class.
- Separate these responsibilities into appropriate classes.
- Create a `main()` method to test all three operations.

### Expected result

```text
Student saved
Report generated
Email sent
```

### Questions to answer

1. Why was the original class difficult to maintain?
2. How does your solution follow SRP?

---

## Assignment 2: Payment Methods

**Principle:** Open/Closed Principle (OCP)

### Problem

Your application supports only card payments.

```java
class PaymentService {
    void pay(String paymentType) {
        if (paymentType.equals("CARD")) {
            System.out.println("Paid using card");
        }
    }
}
```

Now the business wants to support UPI and PayPal.

### Your tasks

- Create a `Payment` interface with a `pay()` method.
- Create `CardPayment`, `UpiPayment`, and `PayPalPayment` classes.
- Implement the `pay()` method in each class.
- Make it easy to add another payment type without modifying existing payment implementations.

### Expected result

```text
Paid using card
Paid using UPI
Paid using PayPal
```

### Questions to answer

1. Why can adding more `if-else` conditions become difficult?
2. How does your design make adding a new payment method easier?

---

## Assignment 3: Bird Behavior

**Principle:** Liskov Substitution Principle (LSP)

### Problem

Consider this code:

```java
class Bird {
    void eat() {
        System.out.println("Bird is eating");
    }

    void fly() {
        System.out.println("Bird is flying");
    }
}

class Sparrow extends Bird {
}

class Penguin extends Bird {
    @Override
    void fly() {
        throw new UnsupportedOperationException(
            "Penguins cannot fly"
        );
    }
}
```

The `Penguin` class inherits a behavior that it cannot support.

### Your tasks

- Redesign the classes so all birds can eat.
- Allow sparrows to fly.
- Do not force penguins to implement flying behavior.
- Use an interface if needed.

### Expected result

```text
Bird is eating
Sparrow is flying
Bird is eating
```

The program should not throw an exception when working with a penguin.

### Questions to answer

1. Why is the original design problematic?
2. How does your design follow LSP?

---

# Level 2 — Medium

## Assignment 4: Printer System

**Principle:** Interface Segregation Principle (ISP)

### Problem

You have one interface for every type of machine:

```java
interface Machine {
    void print();
    void scan();
    void fax();
}
```

But your application has two machines:

- `BasicPrinter`: can only print.
- `MultiFunctionPrinter`: can print, scan, and fax.

### Your tasks

- Identify the problem with the existing interface.
- Split it into smaller interfaces.
- Implement `BasicPrinter`.
- Implement `MultiFunctionPrinter`.
- Ensure that `BasicPrinter` doesn't need empty methods or exceptions for scanning and faxing.

### Expected result

```text
Printing document
Printing document
Scanning document
Sending fax
```

### Questions to answer

1. Why should an interface not contain unnecessary methods?
2. How does your design follow ISP?

---

## Assignment 5: User Repository

**Principle:** Dependency Inversion Principle (DIP)

### Problem

Your service directly depends on a MySQL repository.

```java
class MySqlUserRepository {
    void saveUser() {
        System.out.println("User saved in MySQL");
    }
}

class UserService {
    private final MySqlUserRepository repository =
        new MySqlUserRepository();

    void registerUser() {
        repository.saveUser();
    }
}
```

Now you want to use an in-memory repository for testing.

### Your tasks

- Create a `UserRepository` interface.
- Create `MySqlUserRepository` implementing `UserRepository`.
- Create `InMemoryUserRepository` implementing `UserRepository`.
- Change `UserService` to depend on the interface.
- Do not create the repository using `new` inside `UserService`.

### Expected result

You should be able to run the same service with either repository.

Example output when using MySQL:

```text
User saved in MySQL
```

Example output when using the in-memory repository:

```text
User saved in memory
```

### Questions to answer

1. Why should `UserService` depend on an interface?
2. Which part of your solution follows DIP?
3. What would you need to change in `UserService` to switch repositories?

---

## Assignment 6: Notification Service

**Principles:** DIP and Dependency Injection (DI)

### Problem

A user registration service sends an email directly.

```java
class EmailService {
    void sendEmail() {
        System.out.println("Email sent");
    }
}

class UserService {
    private final EmailService emailService =
        new EmailService();

    void registerUser() {
        System.out.println("User registered");
        emailService.sendEmail();
    }
}
```

Now the application must support both email and SMS notifications.

### Your tasks

- Create a `NotificationService` interface.
- Create `EmailNotificationService`.
- Create `SmsNotificationService`.
- Inject `NotificationService` into `UserService` through its constructor.
- Create a `main()` method that uses both implementations with `UserService`.

### Expected result

```text
User registered
Email notification sent
User registered
SMS notification sent
```

### Questions to answer

1. What is a dependency in this example?
2. Where does dependency injection happen?
3. What is the difference between DIP and DI?
4. Why is constructor injection useful?

---

# Level 3 — Challenge

## Assignment 7: E-commerce Order System

**Principles:** All five SOLID principles and DI

### Problem

Build a small e-commerce application that processes customer orders.

The application must:

1. Calculate the order total.
2. Process a payment.
3. Save the order.
4. Send a notification after successful payment.

### Requirements

#### 1. Order calculation

Create an `Order` class that stores order details.

Create an appropriate class to calculate the order total.

#### 2. Payment processing

Support:

- Card payment
- UPI payment

Use an interface so new payment methods can be added easily.

#### 3. Order storage

Create an `OrderRepository` interface.

Implement:

- `InMemoryOrderRepository`
- `MySqlOrderRepository` (a simulated implementation is enough; no real database is required)

#### 4. Notification

Create a `NotificationService` interface.

Implement:

- `EmailNotificationService`
- `SmsNotificationService`

#### 5. Order service

Create an `OrderService` that coordinates the workflow.

It should receive its dependencies through its constructor.

### Expected workflow

```text
Order total calculated: 1500
Payment successful: UPI
Order saved
Email notification sent
```

The exact output can vary based on your implementation.

### Rules

- Avoid putting the entire workflow into one class.
- Do not create repository, payment, or notification dependencies inside `OrderService`.
- Prefer interfaces where different implementations are expected.
- Do not add unnecessary methods to interfaces.
- Make sure implementations follow the behavior promised by their interfaces.
- Add a new payment implementation without rewriting `OrderService`.

### Bonus tasks

- Handle payment failure.
- Do not save an order when payment fails.
- Do not send a success notification when payment fails.
- Write unit tests for `OrderService`.
- Try implementing the project using Spring Boot and constructor injection.

### Questions to answer

1. Which class is responsible for calculating the total?
2. Which class coordinates the order workflow?
3. How does your design follow each SOLID principle?
4. How can you test `OrderService` without using a real database?
5. What changes would be required to add a new payment method?

---

## Assignment 8: Employee Payroll System

**Principles:** SRP, OCP, ISP, DIP, and DI

### Problem

Build a payroll system for different employee types.

The application should support:

- Full-time employees
- Part-time employees
- Contract employees

Each type has a different salary calculation rule.

### Your tasks

1. Create an `Employee` model.
2. Create a `SalaryCalculator` interface.
3. Implement a separate salary calculator for each employee type.
4. Create a `PayrollService` that uses the calculator.
5. Inject dependencies through constructors.
6. Add a new employee type without rewriting existing calculators.
7. Separate salary calculation from report generation.
8. Avoid interfaces with unnecessary methods.

### Expected result

For example:

```text
Employee: Ram
Employee type: Full-time
Salary calculated: 50000

Employee: Sita
Employee type: Part-time
Salary calculated: 20000
```

The salary values are examples; choose your own calculation rules.

### Bonus tasks

- Add tax calculation.
- Generate a payroll report.
- Save payroll records using a repository interface.
- Write tests for different salary calculators.

### Questions to answer

1. Why should salary calculation be separate from report generation?
2. How does the design support a new employee type?
3. Why is `SalaryCalculator` an interface?
4. How does constructor injection help with testing?

---

# Practice Checklist

Mark each assignment as you complete it.

- [ ] Assignment 1 — Student Management (SRP)
- [ ] Assignment 2 — Payment Methods (OCP)
- [ ] Assignment 3 — Bird Behavior (LSP)
- [ ] Assignment 4 — Printer System (ISP)
- [ ] Assignment 5 — User Repository (DIP)
- [ ] Assignment 6 — Notification Service (DIP + DI)
- [ ] Assignment 7 — E-commerce Order System (All SOLID principles)
- [ ] Assignment 8 — Employee Payroll System (Multiple principles)

---

# How to Know You Have Learned SOLID

For every assignment, you should be able to explain:

1. What was wrong with the original design?
2. Which principle did you apply?
3. What did you change and why?
4. How would you add a new feature?
5. How would you test the code?

**Recommended order:** Complete Assignments 1–6 first. Then attempt Assignment 7 without looking at your previous code. Use Assignment 8 as additional practice.

The goal is not just to make the program work. The goal is to make the code easy to change without breaking unrelated functionality.
