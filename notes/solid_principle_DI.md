# SOLID Principles and Dependency Injection (DI)

SOLID is a set of 5 principles that help us write code that is easy to understand, change, and maintain.

## 1. What is SOLID?

- **S** — Single Responsibility Principle
- **O** — Open/Closed Principle
- **L** — Liskov Substitution Principle
- **I** — Interface Segregation Principle
- **D** — Dependency Inversion Principle

---

## S — Single Responsibility Principle (SRP)

**Simple meaning:** A class should have one main job.

Imagine a class that saves user details and also sends emails. If email logic changes, we need to modify that class.

Instead, separate the responsibilities.

```java
class UserService {
    void saveUser() {
        System.out.println("User saved");
    }
}

class EmailService {
    void sendEmail() {
        System.out.println("Email sent");
    }
}
```

Here:
- `UserService` handles user-related work.
- `EmailService` handles email-related work.

**Remember:** One class = one main job.

---

## O — Open/Closed Principle (OCP)

**Simple meaning:** We should be able to add new functionality without repeatedly changing existing code.

Suppose our application sends notifications. Today, we send emails. Tomorrow, we may want to send SMS messages.

```java
interface NotificationService {
    void send();
}

class EmailNotification implements NotificationService {
    public void send() {
        System.out.println("Email sent");
    }
}

class SmsNotification implements NotificationService {
    public void send() {
        System.out.println("SMS sent");
    }
}
```

Now, we can add another notification type by creating a new class that implements `NotificationService`.

We don't need to rewrite the existing notification classes.

**Remember:** Extend existing behavior instead of constantly modifying existing code.

---

## L — Liskov Substitution Principle (LSP)

**Simple meaning:** A child class should work correctly wherever its parent type is expected.

```java
class Bird {
    void eat() {
        System.out.println("Bird is eating");
    }
}

class Sparrow extends Bird {
    void fly() {
        System.out.println("Sparrow is flying");
    }
}
```

We can use a `Sparrow` wherever a `Bird` is expected.

```java
Bird bird = new Sparrow();
bird.eat();
```

Output:

```text
Bird is eating
```

The child class still supports the behavior promised by its parent.

**Remember:** A child class should not break the expected behavior of its parent.

**Important:** Not every bird can fly. So, putting a `fly()` method in the parent `Bird` class could cause problems for birds such as penguins.

---

## I — Interface Segregation Principle (ISP)

**Simple meaning:** A class should not be forced to implement methods it doesn't need.

Imagine a simple printer that can print but cannot scan.

A bad design would be:

```java
interface Machine {
    void print();
    void scan();
}
```

Now, even a simple printer must implement `scan()`.

A better design is to create separate interfaces.

```java
interface Printer {
    void print();
}

interface Scanner {
    void scan();
}

class SimplePrinter implements Printer {
    public void print() {
        System.out.println("Printing document");
    }
}
```

Now, `SimplePrinter` only implements the functionality it needs.

**Remember:** Keep interfaces small and focused.

---

## D — Dependency Inversion Principle (DIP)

**Simple meaning:** A class should depend on an interface rather than directly depending on a specific implementation.

Imagine a `UserService` that sends notifications.

Instead of depending directly on `EmailNotification`, it should depend on a common interface.

```java
interface NotificationService {
    void send();
}

class EmailNotification implements NotificationService {
    public void send() {
        System.out.println("Email sent");
    }
}

class SmsNotification implements NotificationService {
    public void send() {
        System.out.println("SMS sent");
    }
}

class UserService {
    private final NotificationService notificationService;

    UserService(NotificationService notificationService) {
        this.notificationService = notificationService;
    }

    void registerUser() {
        notificationService.send();
    }
}
```

Now, `UserService` doesn't care whether the notification is sent through email or SMS. It only knows about the `NotificationService` interface.

**Remember:** Depend on an interface (contract), not a specific implementation.

---

# 2. What is Dependency Injection (DI)?

**Simple meaning:** Dependency Injection means giving a class the objects it needs from outside instead of making the class create those objects itself.

### First, understand dependency

Suppose `UserService` needs `EmailService` to send an email.

Here:
- `UserService` is the class doing the work.
- `EmailService` is its dependency because `UserService` needs it.

### Without Dependency Injection

```java
class EmailService {
    void sendEmail() {
        System.out.println("Email sent");
    }
}

class UserService {
    private final EmailService emailService = new EmailService();

    void registerUser() {
        emailService.sendEmail();
    }
}
```

Here, `UserService` creates the `EmailService` object itself using `new`.

The problem is that `UserService` is tightly connected to `EmailService`.

### With Dependency Injection

```java
class EmailService {
    void sendEmail() {
        System.out.println("Email sent");
    }
}

class UserService {
    private final EmailService emailService;

    UserService(EmailService emailService) {
        this.emailService = emailService;
    }

    void registerUser() {
        emailService.sendEmail();
    }
}
```

We provide the object from outside:

```java
EmailService emailService = new EmailService();

UserService userService = new UserService(emailService);

userService.registerUser();
```

Now, `UserService` receives the object through its constructor instead of creating it.

### Difference

| Without DI | With DI |
|---|---|
| Class creates its dependency. | Class receives its dependency. |
| Uses `new` inside the class. | Dependency is provided from outside. |
| Harder to replace the dependency. | Easier to replace the dependency. |

**Remember:**

- Without DI: `UserService` creates the object.
- With DI: Someone else provides the object to `UserService`.

---

# 3. How Dependency Injection Works in Spring Boot

In Spring Boot, we usually don't need to create every service object manually.

Spring manages these objects for us. An object managed by Spring is called a **Spring Bean**.

### Example

**Step 1: Create an interface**

```java
public interface NotificationService {
    void send();
}
```

**Step 2: Implement the interface**

```java
import org.springframework.stereotype.Service;

@Service
public class EmailNotificationService
        implements NotificationService {

    @Override
    public void send() {
        System.out.println("Email sent");
    }
}
```

**Step 3: Inject the dependency**

```java
import org.springframework.stereotype.Service;

@Service
public class UserService {

    private final NotificationService notificationService;

    public UserService(NotificationService notificationService) {
        this.notificationService = notificationService;
    }

    public void registerUser() {
        notificationService.send();
    }
}
```

### What happens internally?

1. Spring finds classes marked with `@Service`.
2. Spring creates and manages their objects (beans).
3. Spring sees that `UserService` needs a `NotificationService`.
4. Spring finds a matching implementation, such as `EmailNotificationService`.
5. Spring passes that object to the `UserService` constructor.
6. `UserService` can now use the notification service.

**Important:** If multiple Spring Beans implement `NotificationService`, Spring needs to know which one to inject. We can use `@Primary` or `@Qualifier` to specify the implementation.

---

# 4. Types of Dependency Injection

There are three common types.

### 1. Constructor Injection

The dependency is provided through the constructor.

```java
public UserService(EmailService emailService) {
    this.emailService = emailService;
}
```

**Generally recommended** because dependencies are explicit and can be stored in `final` fields.

### 2. Setter Injection

The dependency is provided through a setter method.

```java
public void setEmailService(EmailService emailService) {
    this.emailService = emailService;
}
```

Useful when a dependency is optional or needs to be changed after construction.

### 3. Field Injection

The dependency is injected directly into a field, commonly using `@Autowired`.

```java
@Autowired
private EmailService emailService;
```

It is generally less preferred than constructor injection because dependencies are less explicit and the field cannot be declared `final`.

---

# 5. DIP vs DI

These terms sound similar, but they are different.

| DIP | DI |
|---|---|
| Dependency Inversion Principle | Dependency Injection |
| A design principle | A technique |
| Says to depend on abstractions rather than concrete classes | Provides the required object from outside |
| Helps us design loosely coupled code | Helps supply dependencies to classes |

**In simple words:**

- **DIP** tells us how to design our dependencies.
- **DI** tells us how to provide those dependencies.

They work well together, but they are not the same thing.

---

# 6. Quick Revision

| Concept | Easy meaning |
|---|---|
| SRP | One class should have one main job. |
| OCP | Add new functionality without constantly changing existing code. |
| LSP | A child type should work wherever its parent type is expected. |
| ISP | Don't force a class to implement methods it doesn't need. |
| DIP | Depend on interfaces rather than specific implementations. |
| DI | Provide the required objects from outside the class. |
| Spring Bean | An object created and managed by Spring. |

## Final Summary

**SOLID helps us design better classes.**

**Dependency Injection helps us provide the objects those classes need.**

Together, they help us write code that is easier to maintain, test, and extend.

# SOLID Principles & Dependency Injection — Interview Questions

## 1. Basic Questions

1. What are SOLID principles?
2. Why do we use SOLID principles in software development?
3. What are the five SOLID principles? Explain each in simple words.
4. What is the difference between tight coupling and loose coupling?
5. What is an interface in Java, and why do we use it?
6. What is the difference between an interface and an abstract class?
7. How do SOLID principles make code easier to maintain and test?

## 2. Single Responsibility Principle (SRP)

1. What is the Single Responsibility Principle?
2. What does "a class should have only one reason to change" mean?
3. Can you give an example of an SRP violation in Java?
4. How would you refactor a class that handles database operations, business logic, and email notifications?
5. Does SRP mean that a class can have only one method?

## 3. Open/Closed Principle (OCP)

1. What is the Open/Closed Principle?
2. What does "open for extension but closed for modification" mean?
3. How can interfaces help us follow OCP in Java?
4. How would you add a new payment method without modifying existing payment logic?
5. Is using `if-else` always a violation of OCP?
6. What is the relationship between OCP and polymorphism?

## 4. Liskov Substitution Principle (LSP)

1. What is the Liskov Substitution Principle?
2. What does it mean to replace a parent object with a child object?
3. How can a child class violate LSP?
4. Why is throwing `UnsupportedOperationException` from an overridden method sometimes a warning sign?
5. Explain the Rectangle and Square example of LSP.
6. How is LSP related to inheritance and polymorphism?

## 5. Interface Segregation Principle (ISP)

1. What is the Interface Segregation Principle?
2. Why should we avoid large interfaces?
3. How would you refactor an interface that contains methods some classes don't need?
4. What is the difference between ISP and SRP?
5. Can a Java class implement multiple interfaces?
6. How does ISP help when designing services in a real application?

## 6. Dependency Inversion Principle (DIP)

1. What is the Dependency Inversion Principle?
2. What does "depend on abstractions, not concrete implementations" mean?
3. What is the difference between a high-level module and a low-level module?
4. How can an interface reduce coupling between two classes?
5. What is the difference between DIP and Dependency Injection?
6. How does DIP help when changing from one database or payment provider to another?

## 7. Dependency Injection (DI)

1. What is Dependency Injection?
2. What is a dependency in Java?
3. What is the difference between creating an object using `new` and receiving it through injection?
4. What are the advantages of Dependency Injection?
5. What are the three common types of Dependency Injection?
6. Why is constructor injection generally preferred over field injection?
7. What is the difference between Dependency Injection and Dependency Inversion?
8. How does Dependency Injection make unit testing easier?
9. What is an IoC container?
10. What is a Spring Bean?
11. How does Spring Boot inject dependencies?
12. What happens if Spring finds multiple beans of the same interface?
13. What are `@Autowired`, `@Primary`, and `@Qualifier` used for?
14. Can we use Dependency Injection without Spring?

## 8. Scenario-Based Questions

These questions test whether you can apply the principles to real code.

1. A class handles user registration, email sending, and database saving. Which SOLID principle might it violate, and how would you fix it?
2. A payment service contains 15 `if-else` conditions for different payment methods. How would you redesign it?
3. A `Bird` parent class has a `fly()` method, but a `Penguin` child class cannot fly. What is the design problem?
4. A simple printer must implement `print()`, `scan()`, and `fax()`, even though it only supports printing. How would you fix the design?
5. An `OrderService` directly creates a `MySQLDatabase` object. How would you reduce coupling?
6. Your application uses email notifications today but needs SMS notifications tomorrow. How would you design it to support both?
7. Your Spring Boot application has two implementations of `PaymentService`. What happens when you inject `PaymentService` into another service, and how can you resolve any ambiguity?
8. How would you write a unit test for a service that depends on a payment gateway?
9. Can adding too many interfaces and abstractions make code harder to maintain? Explain.
10. Give an example where you would deliberately choose a simple implementation instead of applying every SOLID principle.

## 9. Practical Coding Questions

Try solving these without looking at the answers.

### Easy

- [ ] Create a Java class that violates SRP, then refactor it.
- [ ] Create a `NotificationService` interface with email and SMS implementations.
- [ ] Demonstrate constructor injection using two Java classes.
- [ ] Create separate `Printer` and `Scanner` interfaces.

### Medium

- [ ] Refactor a payment service containing multiple `if-else` conditions to follow OCP.
- [ ] Design a bird hierarchy that follows LSP.
- [ ] Refactor a class that directly creates its database dependency to use DIP and DI.
- [ ] Write a unit test for a service by injecting a fake or mock dependency.

### Challenge

- [ ] Design an order-processing system using all five SOLID principles.
- [ ] Implement multiple payment methods in Spring Boot using interfaces and constructor injection.
- [ ] Configure two implementations of one Spring service and select the required one using `@Qualifier`.
- [ ] Explain which SOLID principles your design follows and why.

---

## 10. Questions to Prioritize Before an Interview

If you have limited preparation time, start with these:

1. Explain all five SOLID principles with examples.
2. Explain SRP and how you would refactor a class with multiple responsibilities.
3. Explain OCP using a payment system.
4. Explain LSP using the Rectangle and Square example.
5. Explain ISP using a printer example.
6. Explain DIP with an interface-based Java example.
7. What is Dependency Injection, and why is it useful?
8. Why is constructor injection preferred in Spring Boot?
9. What is the difference between DIP and DI?
10. How do SOLID principles improve testability and maintainability?
