# javaoopd

# Java Final Exam — Answer Notebook 
### Covers: L1 (Encapsulation & Polymorphism) · L2 (Overloading vs Overriding) · L3 (Abstract vs Interface) · L4 (Collections)

---

## 📘 L1 — Encapsulation and Polymorphism

### 1.1 — What is encapsulation?

**Definition:** Encapsulation is the OOP principle of **bundling data (fields) and the methods that operate on that data into a single unit (a class)**, while **restricting direct access** to the internal state from outside the class. It is often called "data hiding."

**How Java achieves it:**
1. Declare fields as `private` ú— this hides them from outside classes.
2. Provide `public` **getter** methods to read the value.
3. Provide `public` **setter** methods to modify the value — and inside the setter you can add **validation logic** (e.g., reject a negative age).

```java
public class Person {
    private int age;               // hidden field

    public int getAge() {          // getter
        return age;
    }

    public void setAge(int age) {  // setter with validation
        if (age >= 0) {
            this.age = age;
        }
    }
}
```

**Real-life analogy:** A **capsule medicine** — the active chemical (data) is sealed inside a shell (private field). You can't touch the chemical directly; you interact with it only through the intended route (swallowing = calling the getter/setter). Another common analogy: an **ATM machine** — you can deposit/withdraw money (public methods) but you never touch the bank's internal database (private fields) directly.

**Why it matters (exam point):** Encapsulation gives **control, security, and flexibility** — the internal implementation can change later without breaking code that uses the class, as long as the public method signatures stay the same.

---

### 1.2 — What is polymorphism? Compile-time vs Run-time

**Definition:** Polymorphism ("many forms") means **the same method name or the same reference type can behave differently depending on context** — either the arguments passed, or the actual object type at runtime.

| Aspect | Compile-time (Static) Polymorphism | Run-time (Dynamic) Polymorphism |
|---|---|---|
| Achieved by | **Method Overloading** | **Method Overriding** |
| Resolved when | At **compile time** (by the compiler, based on method signature) | At **run time** (by the JVM, based on actual object) |
| Also called | Early binding | Late binding |
| Requires inheritance? | No | Yes (parent-child relationship) |

**Example — Compile-time (Overloading):**
```java
class Calculator {
    int add(int a, int b) { return a + b; }
    double add(double a, double b) { return a + b; }
}
// Compiler decides which add() to call based on argument types
```

**Example — Run-time (Overriding):**
```java
class Animal {
    void sound() { System.out.println("Some sound"); }
}
class Dog extends Animal {
    @Override
    void sound() { System.out.println("Bark"); }
}
// Animal a = new Dog();  a.sound();  → prints "Bark" — decided at RUN TIME
```

**Exam tip:** If asked "why is it called early/late binding?" — early binding means the compiler already knows *exactly which method* will run just by reading the code. Late binding means the compiler only knows the *reference type*; the actual method executed depends on the object created, which is only known when the program runs.

---

### 1.3 — Practical: `BankAccount` (Encapsulation + Overloading)

```java
public class BankAccount {
    private double balance;   // encapsulated field

    public BankAccount(double initialBalance) {
        this.balance = initialBalance;
    }

    public double getBalance() {          // getter
        return balance;
    }

    // Overload 1
    public void deposit(double amount) {
        balance += amount;
        System.out.println("Deposited: " + amount);
    }

    // Overload 2 — polymorphism via overloading
    public void deposit(double amount, String remarks) {
        balance += amount;
        System.out.println("Deposited: " + amount + " | Remarks: " + remarks);
    }
}

public class Main {
    public static void main(String[] args) {
        BankAccount acc = new BankAccount(1000.0);

        acc.deposit(500.0);                         // calls overload 1
        acc.deposit(200.0, "Salary credit");         // calls overload 2

        System.out.println("Final Balance: " + acc.getBalance());
    }
}
```
**What to say if asked to comment:** `balance` is `private` → encapsulation. `getBalance()` is the controlled read access. The two `deposit()` methods with different parameter lists → compile-time polymorphism (overloading).

---
---

## 📘 L2 — Method Overloading vs Overriding (Early vs Late Binding)

### 2.1 — Compare Overloading vs Overriding

| Feature | Overloading | Overriding |
|---|---|---|
| **Definition** | Same method name, different parameter list, in the **same class** | Same method name **and** same parameter list, in a **subclass**, replacing the parent's version |
| **Class involved** | One class (or class + subclasses, but no inheritance required) | Two classes — parent and child (inheritance required) |
| **Parameters** | Must differ (number, type, or order) | Must be **identical** |
| **Return type** | Can differ | Must be same or a covariant (subtype) return |
| **Binding time** | Compile time (early binding) | Run time (late binding) |
| **Access modifier** | Can be anything | Cannot be more restrictive than the parent's method |
| **Purpose** | Convenience — same operation, different input forms | Specialization — child gives its own version of inherited behavior |

### 2.2 — Early Binding vs Late Binding

- **Early Binding (Static Binding):** The compiler resolves *which method to call* while compiling the code — because it can determine this purely from the method signature written in the source. This happens with overloaded methods, `static` methods, and `private` methods.
- **Late Binding (Dynamic Binding):** The JVM resolves *which method to call* only at run time, by checking the **actual object** stored in memory (not the reference type). This happens with overridden instance methods.

**Why overriding is resolved at run time:** In Java, when you write `Animal a = new Dog();` the *reference* `a` is of type `Animal`, but the *object* is a `Dog`. Because a parent reference can point to many possible child types (polymorphic reference), the compiler cannot know in advance which subclass object will actually be assigned — so the decision is deferred to run time, when the actual object is known. Java uses a mechanism internally called **dynamic method dispatch** (via the object's vtable) to do this.

**Why overloading is resolved at compile time:** Overloaded methods live side-by-side in the same class with different signatures. The compiler can simply match the arguments you pass against the available signatures right away — no object identity is needed.

### 2.3 — Practical: `Shape` overriding + overloading

```java
abstract class Shape {
    abstract double area();  // to be overridden — late binding

    // Overloaded methods — early binding
    void describe(String name) {
        System.out.println("Shape: " + name);
    }
    void describe(String name, int sides) {
        System.out.println("Shape: " + name + ", Sides: " + sides);
    }
}

class Circle extends Shape {
    private double radius;
    Circle(double radius) { this.radius = radius; }

    @Override
    double area() { return Math.PI * radius * radius; }
}

class Rectangle extends Shape {
    private double length, width;
    Rectangle(double length, double width) {
        this.length = length; this.width = width;
    }

    @Override
    double area() { return length * width; }
}

public class Main {
    public static void main(String[] args) {
        Shape s1 = new Circle(5);       // parent reference, child object
        Shape s2 = new Rectangle(4, 6);

        System.out.println("Circle area: " + s1.area());       // late binding
        System.out.println("Rectangle area: " + s2.area());    // late binding

        s1.describe("Circle");                 // early binding
        s2.describe("Rectangle", 4);            // early binding
    }
}
```
**Sample output:**
```
Circle area: 78.53981633974483
Rectangle area: 24.0
Shape: Circle
Shape: Rectangle, Sides: 4
```

---
---

## 📘 L3 — Abstract Class vs Interface

### 3.1 — Definitions & Structural Differences

**Abstract class:** A class declared with the `abstract` keyword that **cannot be instantiated** on its own. It can contain a mix of fully implemented (concrete) methods and abstract (unimplemented) methods that subclasses must override.

**Interface:** A reference type that defines a **contract of behavior** — a set of method signatures (traditionally all abstract, though modern Java allows `default` and `static` methods) that any implementing class must provide.

| Aspect | Abstract Class | Interface |
|---|---|---|
| Fields | Can have instance variables (any access modifier, any state) | Only `public static final` constants |
| Constructors | Yes — can have constructors (called via `super()`) | No constructors |
| Method bodies | Can mix concrete + abstract methods | Traditionally all abstract; can have `default`/`static` bodies since Java 8, but no instance state |
| Multiple inheritance | A class can extend **only one** abstract class | A class can implement **many** interfaces |
| Keyword | `extends` | `implements` |
| Access modifiers on methods | Any (`private`, `protected`, `public`) | Implicitly `public` |

### 3.2 — When to choose which

- **Abstract class** — choose when classes share a strong **"is-a" relationship** and some **common implemented code/state** that shouldn't be repeated. *Example:* `Vehicle` (abstract) → `Car`, `Bike`, `Truck` all share fields like `speed`, and a common `startEngine()` implementation, but each must define its own `fuelType()`.
- **Interface** — choose when unrelated classes need to guarantee a **capability ("can-do")**, regardless of their place in the class hierarchy. *Example:* `Insurable` — both a `Car` and a `House` (completely unrelated classes) can implement `Insurable` because both "can be insured," even though they don't share a common parent.

### 3.3 — Practical: `Vehicle`, `Insurable`, `Car`

```java
abstract class Vehicle {
    void startEngine() {                 // concrete — shared by all vehicles
        System.out.println("Engine started.");
    }
    abstract String fuelType();          // abstract — each vehicle differs
}

interface Insurable {
    double calculatePremium();           // contract — any insurable object must implement
}

class Car extends Vehicle implements Insurable {
    @Override
    String fuelType() {
        return "Petrol";
    }

    @Override
    public double calculatePremium() {
        return 15000.0;   // flat example premium
    }
}

public class Main {
    public static void main(String[] args) {
        Car myCar = new Car();
        myCar.startEngine();
        System.out.println("Fuel type: " + myCar.fuelType());
        System.out.println("Premium: " + myCar.calculatePremium());
    }
}
```

**Why each construct was chosen (say this in your answer):**
- `Vehicle` is an **abstract class** because `Car` genuinely "is-a" `Vehicle`, and `startEngine()` is common, reusable code that every vehicle needs — no point rewriting it in every subclass.
- `Insurable` is an **interface** because "being insurable" is a capability that could equally apply to a `House` or a `Car` — classes with no relation to each other in the `Vehicle` hierarchy. Interfaces let `Car` pick up this unrelated capability without forcing an artificial inheritance chain.

---
---

## 📘 L4 — Collection Framework

### 4.1 — `ArrayList` vs `Vector` vs `LinkedList`

| Feature | ArrayList | Vector | LinkedList |
|---|---|---|---|
| Underlying structure | Resizable (dynamic) array | Resizable (dynamic) array | Doubly linked list |
| Synchronization | **Not synchronized** (not thread-safe) | **Synchronized** (thread-safe, but slower) | Not synchronized |
| Random access (`get(i)`) | Fast — O(1) | Fast — O(1) | Slow — O(n), must traverse nodes |
| Insert/Delete at middle | Slow — O(n), must shift elements | Slow — O(n) | Fast — O(1) once position is found (no shifting, just re-link pointers) |
| Growth strategy | Grows by 50% when full | Grows by 100% (doubles) when full | Grows dynamically, node by node |
| Best use case | Frequent read/access-heavy operations | Legacy thread-safe code (rarely used now — `Collections.synchronizedList()` is preferred) | Frequent insertions/deletions, especially at the ends (also used as a `Deque`/`Queue`) |

### 4.2 — `Set` and its implementations

**Definition:** `Set` is a `Collection` that **does not allow duplicate elements**. It models the mathematical idea of a set.

- **`HashSet`** — Backed by a hash table. **No guaranteed order** of elements. Fastest for basic add/remove/contains operations — O(1) average.
- **`LinkedHashSet`** — Extends `HashSet` but also maintains a doubly-linked list running through all entries, so it preserves **insertion order** when you iterate.
- **`TreeSet`** — Backed by a **Red-Black Tree** (a self-balancing binary search tree). It keeps elements in **sorted (natural or custom comparator) order** automatically. Insert/search/delete are O(log n).

**How TreeSet maintains order:** Every time an element is added, `TreeSet` compares it against existing elements using either the element's natural ordering (via `Comparable`'s `compareTo()`) or a custom `Comparator` passed to its constructor, and places it at the correct position in the tree structure. Because it's a balanced binary search tree, an in-order traversal always yields elements in sorted order.

### 4.3 — Practical: `ArrayList` vs `TreeSet` for student names

```java
import java.util.ArrayList;
import java.util.TreeSet;

public class Main {
    public static void main(String[] args) {
        // ArrayList — preserves insertion order
        ArrayList<String> list = new ArrayList<>();
        list.add("Rafi");
        list.add("Ayon");
        list.add("Mim");
        list.add("Karim");
        list.add("Bithi");

        System.out.println("ArrayList (insertion order):");
        for (String name : list) {
            System.out.println(name);
        }

        // TreeSet — auto-sorts, no duplicates
        TreeSet<String> set = new TreeSet<>();
        set.add("Rafi");
        set.add("Ayon");
        set.add("Mim");
        set.add("Karim");
        set.add("Bithi");

        System.out.println("\nTreeSet (sorted order):");
        for (String name : set) {
            System.out.println(name);
        }
    }
}
```

**Comment on the ordering difference (write this in your answer):** The `ArrayList` prints names in the **exact order they were added** (Rafi, Ayon, Mim, Karim, Bithi). The `TreeSet` prints them in **alphabetically sorted order** (Ayon, Bithi, Karim, Mim, Rafi) because `TreeSet` automatically reorganizes elements based on natural ordering (here, `String`'s lexicographic `compareTo()`), regardless of insertion sequence.

---

*End of Part 1 (L1–L4). Say "next part" and I'll continue with Part 2: L5–L8 (Multithreading/Exceptions, JDBC/MVC, JavaFX, Sockets & RMI).*

# Java Final Exam — Answer Notebook (Part 2 of 3)
### Covers: L5 (Multithreading & Custom Exceptions) · L6 (JDBC/MVC) · L7 (JavaFX) · L8 (Sockets & RMI)

---

## 📘 L5 — Multithreading & Custom Exception Handling

### 5.1 — Ways to implement multithreading in Java

**Definition:** A **thread** is the smallest unit of execution within a program. Multithreading lets a program run multiple parts (threads) **concurrently**, sharing the same process memory, which improves responsiveness and CPU utilization.

Three common ways:

1. **Extending the `Thread` class** — Override `run()`, then call `start()`.
2. **Implementing the `Runnable` interface** — Define `run()`, wrap it in a `Thread` object, then call `start()`.
3. **Using `ExecutorService` with `Callable`/`Runnable`** — A higher-level thread-pool based API (`java.util.concurrent`) that manages a pool of reusable threads instead of creating raw `Thread` objects manually. `Callable` additionally allows returning a result and throwing checked exceptions (via `Future<T>`).

**Which is preferred and why:** `implements Runnable` (or better, `ExecutorService`) is generally preferred over `extends Thread`.
- Java doesn't support multiple inheritance of classes — if your class already extends `Thread`, it can't extend anything else. Implementing `Runnable` keeps that door open.
- It separates the **task** (`run()` logic) from the **thread mechanism**, following better OOP design (composition over inheritance).
- `ExecutorService` is preferred in real/production applications because manually creating threads is expensive and hard to manage at scale; a thread pool reuses threads efficiently and gives you lifecycle control (shutdown, timeouts, `Future` results).

### 5.2 — Practical: Two threads printing 1–5

```java
// Approach 1: extends Thread
class MyThread extends Thread {
    @Override
    public void run() {
        for (int i = 1; i <= 5; i++) {
            System.out.println("MyThread: " + i);
        }
    }
}

// Approach 2: implements Runnable
class MyRunnable implements Runnable {
    @Override
    public void run() {
        for (int i = 1; i <= 5; i++) {
            System.out.println("MyRunnable: " + i);
        }
    }
}

public class Main {
    public static void main(String[] args) {
        MyThread t1 = new MyThread();          // via extends Thread
        Thread t2 = new Thread(new MyRunnable()); // via implements Runnable

        t1.start();   // never call run() directly — start() creates a new call stack
        t2.start();
    }
}
```
**Exam note:** Calling `.start()` (not `.run()`) is essential — `start()` tells the JVM to allocate a new thread of execution; calling `.run()` directly just executes the method normally on the current thread, defeating the purpose.

### 5.3 — Practical: Custom checked exception `InvalidRadiusException`

**Definition of custom exception:** A **checked exception** is one the compiler forces you to either catch or declare with `throws`. You create a custom one by extending `Exception` (for checked) — useful when you want a meaningful, specific error type for your domain (here: an invalid geometric radius).

```java
// Custom checked exception
class InvalidRadiusException extends Exception {
    public InvalidRadiusException(String message) {
        super(message);
    }
}

class Circle {
    private double radius;

    public Circle(double radius) throws InvalidRadiusException {
        if (radius < 0) {
            throw new InvalidRadiusException("Radius cannot be negative: " + radius);
        }
        this.radius = radius;
    }

    public double area() {
        return Math.PI * radius * radius;
    }
}

import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.print("Enter radius: ");
        double r = sc.nextDouble();

        try {
            Circle c = new Circle(r);
            System.out.println("Area: " + c.area());
        } catch (InvalidRadiusException e) {
            System.out.println("Error: " + e.getMessage());
        }
    }
}
```
**(Note: in real code, the `import` line must be at the very top of the file — keep class declarations after imports.)**

---
---

## 📘 L6 — JDBC with MySQL/Oracle (MVC Pattern)

### 6.1 — Steps to connect Java to MySQL/Oracle via JDBC

**Definition:** **JDBC (Java Database Connectivity)** is an API that lets Java programs execute SQL statements against a relational database.

**Steps:**
1. **Add the driver** — include the MySQL Connector/J (or Oracle `ojdbc`) `.jar` in the classpath/Maven dependency.
2. **Load & register the driver** — modern JDBC (4.0+) auto-registers via `DriverManager`, so this is usually automatic.
3. **Establish a connection** — `Connection con = DriverManager.getConnection(url, user, password);`
4. **Create a statement** — use `PreparedStatement` (safer, prevents SQL injection, precompiled) rather than plain `Statement`.
5. **Execute the query** — `executeUpdate()` for INSERT/UPDATE/DELETE, `executeQuery()` for SELECT (returns a `ResultSet`).
6. **Process the `ResultSet`** — iterate with `while(rs.next())` to read rows.
7. **Close resources** — close `ResultSet`, `Statement`, and `Connection` (or use try-with-resources).

**Key classes:**
- `DriverManager` — manages a list of database drivers, hands out `Connection` objects.
- `Connection` — represents an active session/link to the database.
- `PreparedStatement` — a precompiled SQL statement with placeholders (`?`) for safe parameter binding.
- `ResultSet` — a table-like cursor over the rows returned by a SELECT query.

### 6.2 — MVC Pattern mapped to a JDBC app

**Definition:** **MVC (Model-View-Controller)** separates an application into three responsibilities so each part can change independently:
- **Model** — represents the data/business object (plain fields + getters/setters).
- **View** — the presentation layer that the user sees/interacts with.
- **Controller** — the "glue" that takes input from the View, manipulates the Model, and talks to the data source.

**Mapping in a JDBC app:**
| MVC Role | Class |
|---|---|
| Model | `Student` (plain data holder — id, name, cgpa) |
| Controller (often called DAO — Data Access Object) | `StudentDAO` (contains `insert()`, `findAll()` using JDBC calls) |
| View | `Main` (collects user input, displays results — in a console app this is simplified; in a web app this would be a JSP/HTML page) |

### 6.3 — Practical: MVC-style `Student` code

```java
// ---- MODEL ----
class Student {
    private int id;
    private String name;
    private double cgpa;

    public Student(int id, String name, double cgpa) {
        this.id = id;
        this.name = name;
        this.cgpa = cgpa;
    }
    public int getId() { return id; }
    public String getName() { return name; }
    public double getCgpa() { return cgpa; }
}

// ---- CONTROLLER / DAO ----
import java.sql.*;

class StudentDAO {
    private static final String URL = "jdbc:mysql://localhost:3306/student_db";
    private static final String USER = "root";
    private static final String PASS = "password";

    public void insert(Student s) {
        String sql = "INSERT INTO Students (id, name, cgpa) VALUES (?, ?, ?)";
        try (Connection con = DriverManager.getConnection(URL, USER, PASS);
             PreparedStatement ps = con.prepareStatement(sql)) {

            ps.setInt(1, s.getId());
            ps.setString(2, s.getName());
            ps.setDouble(3, s.getCgpa());
            ps.executeUpdate();
            System.out.println("Student inserted successfully.");

        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}

// ---- VIEW ----
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.print("Enter ID: ");
        int id = sc.nextInt();
        System.out.print("Enter Name: ");
        String name = sc.next();
        System.out.print("Enter CGPA: ");
        double cgpa = sc.nextDouble();

        Student student = new Student(id, name, cgpa);
        StudentDAO dao = new StudentDAO();
        dao.insert(student);
    }
}
```

---
---

## 📘 L7 — JavaFX — House Loan Calculator

### 7.1 — JavaFX application structure

**Definition:** JavaFX is Java's modern GUI toolkit. Its structure:

- **`Application`** — the abstract base class every JavaFX app extends. The JVM calls its lifecycle methods (`init()`, `start()`, `stop()`) automatically when you call `launch()`.
- **`Stage`** — represents the actual **window** on screen (the "canvas frame"). Every JavaFX app has at least one, the "primary stage," passed into `start()`.
- **`Scene`** — represents the **content** inside a `Stage` at a given time (like a "page" you place inside the window). A `Stage` can swap between different `Scene`s.
- **`GridPane` / `VBox`** — **layout containers** ("panes") that arrange child UI nodes (buttons, labels, text fields). `GridPane` arranges children in a row/column grid; `VBox` stacks children vertically.

**Role of `start()`:** `start(Stage primaryStage)` is the **entry point method** JavaFX calls after the toolkit initializes. This is where you build your UI: create layout panes, add controls, wrap them in a `Scene`, attach the `Scene` to the `Stage`, and call `primaryStage.show()`.

### 7.2 — Practical: `GridPane` layout for Loan Calculator

```java
import javafx.application.Application;
import javafx.geometry.Insets;
import javafx.scene.Scene;
import javafx.scene.control.*;
import javafx.scene.layout.GridPane;
import javafx.stage.Stage;

public class LoanCalculator extends Application {
    private TextField loanField = new TextField();
    private TextField rateField = new TextField();
    private TextField yearsField = new TextField();
    private Label resultLabel = new Label();

    @Override
    public void start(Stage primaryStage) {
        GridPane grid = new GridPane();
        grid.setPadding(new Insets(10));
        grid.setHgap(10);
        grid.setVgap(10);

        grid.add(new Label("Loan Amount:"), 0, 0);
        grid.add(loanField, 1, 0);

        grid.add(new Label("Annual Rate (%):"), 0, 1);
        grid.add(rateField, 1, 1);

        grid.add(new Label("Number of Years:"), 0, 2);
        grid.add(yearsField, 1, 2);

        Button calcButton = new Button("Calculate");
        grid.add(calcButton, 1, 3);
        grid.add(resultLabel, 0, 4, 2, 1);

        calcButton.setOnAction(e -> calculate());

        primaryStage.setScene(new Scene(grid, 350, 250));
        primaryStage.setTitle("House Loan Calculator");
        primaryStage.show();
    }

    private void calculate() {
        // implemented in 7.3
    }

    public static void main(String[] args) {
        launch(args);
    }
}
```

### 7.3 — Event handler: compute Monthly Installment, Total Payment, Difference

**Formulas (amortization):**
```
r = AnnualRate / 12 / 100                          (monthly interest rate)
M = P × r × (1 + r)^n / [(1 + r)^n − 1]             (monthly installment)
T = M × n                                            (total payment)
D = T − P                                            (difference / total interest paid)
```

```java
private void calculate() {
    double P = Double.parseDouble(loanField.getText());
    double annualRate = Double.parseDouble(rateField.getText());
    int years = Integer.parseInt(yearsField.getText());

    double r = annualRate / 12 / 100;
    int n = years * 12;

    double M = P * r * Math.pow(1 + r, n) / (Math.pow(1 + r, n) - 1);
    double T = M * n;
    double D = T - P;

    resultLabel.setText(String.format(
        "Monthly Installment: %.2f%nTotal Payment: %.2f%nDifference: %.2f",
        M, T, D));
}
```
**Exam tip:** Mention that the formula is the standard **fixed-rate amortization formula** — it ensures equal monthly payments that cover both principal and interest over the loan term.

---
---

## 📘 L8 — Socket Programming & Java RMI (Chat System)

### 8.1 — Socket Programming vs Java RMI

**Definitions:**
- **Socket programming** — Low-level networking where two programs communicate by exchanging raw **bytes/streams** over a TCP/IP connection. You (the programmer) define your own message format/protocol.
- **Java RMI (Remote Method Invocation)** — A higher-level mechanism that lets a Java program call a **method on an object living in another JVM** almost as if it were local — RMI handles the networking, serialization, and marshaling behind the scenes.

| Aspect | Socket Programming | Java RMI |
|---|---|---|
| Abstraction level | Low-level (raw byte/text streams) | High-level (remote object method calls) |
| Protocol | You design your own message protocol | Java handles it (uses Java serialization internally) |
| Language requirement | Works across any language (cross-platform, e.g. Java client ↔ Python server) | Java-to-Java only (both ends must be JVMs) |
| Ease of use | More manual work (parsing messages) | Simpler for pure Java systems — feels like a local method call |

**When to prefer each:** Use **sockets** when you need cross-language communication, fine control over the protocol, or lightweight/simple data exchange (e.g., a chat message). Use **RMI** when both client and server are Java, and you want to invoke rich, complex object methods remotely without hand-writing a custom protocol.

### 8.2 — Practical: Server code (`ServerSocket`)

```java
import java.io.*;
import java.net.*;

public class ChatServer {
    public static void main(String[] args) throws IOException {
        ServerSocket serverSocket = new ServerSocket(5000);
        System.out.println("Server waiting for connection...");

        Socket clientSocket = serverSocket.accept();   // blocks until client connects
        System.out.println("Client connected.");

        BufferedReader in = new BufferedReader(
                new InputStreamReader(clientSocket.getInputStream()));
        PrintWriter out = new PrintWriter(clientSocket.getOutputStream(), true);

        String message = in.readLine();
        System.out.println("Client says: " + message);

        out.println("Message received: " + message);   // reply

        clientSocket.close();
        serverSocket.close();
    }
}
```

### 8.3 — Practical: Client code (`Socket`)

```java
import java.io.*;
import java.net.*;

public class ChatClient {
    public static void main(String[] args) throws IOException {
        Socket socket = new Socket("localhost", 5000);   // connect to server

        PrintWriter out = new PrintWriter(socket.getOutputStream(), true);
        BufferedReader in = new BufferedReader(
                new InputStreamReader(socket.getInputStream()));

        out.println("Hello Server!");                     // send message

        String reply = in.readLine();
        System.out.println("Server replied: " + reply);   // print reply

        socket.close();
    }
}
```
**Exam tip:** Always mention that `ServerSocket.accept()` is a **blocking call** — the server thread pauses there until a client actually connects.

---

*End of Part 2 (L5–L8). Say "next part" and I'll finish with Part 3: L9–L12 (Servlet+JSP+JDBC CRUD, Spring Boot REST, Servlet CRUD Quiz Game, GoF Design Patterns).*

# Java Final Exam — Answer Notebook (Part 3 of 3)
### Covers: L9 (Servlet + JSP + JDBC CRUD) · L10 (Spring Boot REST API with JPA) · L11 (Servlet CRUD — Quiz Game style) · L12 (GoF Design Patterns)

---

## 📘 L9 — Servlet + JSP + JDBC (CRUD Application)

### 9.1 — What is a Servlet? Servlet Life Cycle

**Definition:** A **Servlet** is a Java class that runs inside a **web/servlet container** (like Tomcat/Jetty) and handles HTTP requests/responses on the server side — essentially "Java's way of writing dynamic web pages" before JSP/frameworks took over the view layer.

**Life cycle (three key methods, managed automatically by the container):**
1. **`init()`** — called **once**, when the servlet is first loaded into memory. Used for one-time setup (e.g., opening a DB connection pool).
2. **`service()`** — called for **every request**. It internally dispatches to `doGet()`, `doPost()`, `doPut()`, `doDelete()` based on the HTTP method used.
3. **`destroy()`** — called **once**, when the container shuts down the servlet (e.g., app undeployed / server stopped). Used to release resources.

**Why only one instance handles many requests:** The container creates a **single servlet instance** and reuses it across multiple threads for efficiency, rather than creating a new object per request (unlike a plain Java object). This is why servlet fields must be handled carefully — shared mutable instance fields can cause thread-safety bugs across concurrent requests.

### 9.2 — Role of JSP vs Servlet in MVC (Model 2 architecture)

**Definition — Model 2 Architecture:** A design pattern for web apps that cleanly separates:
- **Model** — the data/business logic (POJOs + DAO classes talking to JDBC).
- **View** — JSP pages, whose *only job* is to display data (HTML + minimal JSP expression tags), not contain business logic.
- **Controller** — a Servlet that receives the HTTP request, calls the Model to fetch/update data, then **forwards** to the appropriate JSP for rendering.

**Why separate them:** Mixing SQL/business logic directly inside JSP (called "Model 1" / scriptlet-heavy JSP) makes code messy and hard to maintain. Model 2 keeps **logic in Servlets/Java classes** and **presentation in JSP**, matching the general MVC principle of separation of concerns.

### 9.3 — Practical: CRUD Servlet + JDBC (Add + View records)

```java
// ---- MODEL ----
class Employee {
    private int id;
    private String name;
    private double salary;

    public Employee(int id, String name, double salary) {
        this.id = id; this.name = name; this.salary = salary;
    }
    public int getId() { return id; }
    public String getName() { return name; }
    public double getSalary() { return salary; }
}
```

```java
// ---- DAO (Model logic) ----
import java.sql.*;
import java.util.*;

class EmployeeDAO {
    private static final String URL = "jdbc:mysql://localhost:3306/company_db";
    private static final String USER = "root";
    private static final String PASS = "password";

    public void addEmployee(Employee e) throws SQLException {
        String sql = "INSERT INTO Employee (id, name, salary) VALUES (?, ?, ?)";
        try (Connection con = DriverManager.getConnection(URL, USER, PASS);
             PreparedStatement ps = con.prepareStatement(sql)) {
            ps.setInt(1, e.getId());
            ps.setString(2, e.getName());
            ps.setDouble(3, e.getSalary());
            ps.executeUpdate();
        }
    }

    public List<Employee> getAllEmployees() throws SQLException {
        List<Employee> list = new ArrayList<>();
        String sql = "SELECT * FROM Employee";
        try (Connection con = DriverManager.getConnection(URL, USER, PASS);
             PreparedStatement ps = con.prepareStatement(sql);
             ResultSet rs = ps.executeQuery()) {
            while (rs.next()) {
                list.add(new Employee(rs.getInt("id"), rs.getString("name"), rs.getDouble("salary")));
            }
        }
        return list;
    }
}
```

```java
// ---- CONTROLLER (Servlet) ----
import javax.servlet.*;
import javax.servlet.http.*;
import java.io.*;
import java.sql.SQLException;
import java.util.List;

public class EmployeeServlet extends HttpServlet {
    private EmployeeDAO dao = new EmployeeDAO();

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp)
            throws ServletException, IOException {

        int id = Integer.parseInt(req.getParameter("id"));
        String name = req.getParameter("name");
        double salary = Double.parseDouble(req.getParameter("salary"));

        try {
            dao.addEmployee(new Employee(id, name, salary));
            List<Employee> employees = dao.getAllEmployees();
            req.setAttribute("employees", employees);
            req.getRequestDispatcher("employeeList.jsp").forward(req, resp);  // forward to VIEW
        } catch (SQLException e) {
            throw new ServletException(e);
        }
    }
}
```

```jsp
<%-- ---- VIEW: employeeList.jsp ---- --%>
<%@ page import="java.util.List, Employee" %>
<html>
<body>
    <h2>Employee List</h2>
    <table border="1">
        <tr><th>ID</th><th>Name</th><th>Salary</th></tr>
        <%
            List<Employee> employees = (List<Employee>) request.getAttribute("employees");
            for (Employee emp : employees) {
        %>
        <tr>
            <td><%= emp.getId() %></td>
            <td><%= emp.getName() %></td>
            <td><%= emp.getSalary() %></td>
        </tr>
        <% } %>
    </table>
</body>
</html>
```
**Exam tip:** Point out the flow: **Browser → Servlet (Controller, does JDBC via DAO) → forwards data via `request.setAttribute()` → JSP (View, only displays)**. This is the Model 2 / MVC flow in action.

---
---

## 📘 L10 — Spring Boot REST API with Spring Data JPA

### 10.1 — Core Spring Boot annotations

| Annotation | Purpose |
|---|---|
| `@SpringBootApplication` | Marks the main class; combines `@Configuration`, `@EnableAutoConfiguration`, `@ComponentScan` — bootstraps the whole app |
| `@Entity` | Marks a Java class as a **JPA entity** — maps it to a database table |
| `@RestController` | Marks a class as a REST controller — combines `@Controller` + `@ResponseBody`, so return values are automatically serialized to JSON |
| `@RequestMapping` / `@GetMapping` / `@PostMapping` | Map HTTP requests (and specific methods) to controller handler methods |
| `@Autowired` | Tells Spring to **inject a dependency automatically** (e.g., inject a `Repository` into a `Service`) instead of you manually instantiating it — this is **Dependency Injection** |
| `@Repository` | Marks a class/interface as a data-access layer component; Spring wraps exceptions into its own consistent exception hierarchy |

**What is Spring Data JPA?** It's a layer on top of JPA/Hibernate that lets you get a **fully working CRUD repository just by declaring an interface** — e.g., `interface EmployeeRepository extends JpaRepository<Employee, Integer> {}` automatically gives you `save()`, `findAll()`, `findById()`, `deleteById()`, etc., with **zero SQL written by you**.

### 10.2 — Layered architecture: Controller → Service → Repository

**Definition:** A layered (n-tier) architecture separates responsibilities:
- **Controller layer** — receives HTTP requests, extracts input, delegates to Service, returns HTTP responses (status codes + JSON body).
- **Service layer** — contains **business logic** (validation, calculations, orchestrating multiple repository calls). This is the layer that would enforce a rule like "salary cannot be negative."
- **Repository layer** — talks directly to the database via Spring Data JPA, doing pure data access with no business logic.

**Why layer it this way:** Keeps concerns separate (easier testing — you can mock the Service in Controller tests, mock the Repository in Service tests), and lets business rules live in exactly one place rather than scattered across controllers.

### 10.3 — Practical: Full CRUD REST API for `Employee`

```java
// ---- ENTITY ----
import javax.persistence.*;

@Entity
public class Employee {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;
    private String name;
    private double salary;

    // Getters and Setters
    public int getId() { return id; }
    public void setId(int id) { this.id = id; }
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public double getSalary() { return salary; }
    public void setSalary(double salary) { this.salary = salary; }
}
```

```java
// ---- REPOSITORY ----
import org.springframework.data.jpa.repository.JpaRepository;

public interface EmployeeRepository extends JpaRepository<Employee, Integer> {
    // CRUD methods (save, findAll, findById, deleteById) come free — no code needed
}
```

```java
// ---- SERVICE ----
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import java.util.List;

@Service
public class EmployeeService {
    @Autowired
    private EmployeeRepository repository;

    public Employee addEmployee(Employee e) { return repository.save(e); }
    public List<Employee> getAllEmployees() { return repository.findAll(); }
    public Employee getEmployeeById(int id) { return repository.findById(id).orElse(null); }
    public void deleteEmployee(int id) { repository.deleteById(id); }
}
```

```java
// ---- CONTROLLER ----
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;
import java.util.List;

@RestController
@RequestMapping("/api/employees")
public class EmployeeController {
    @Autowired
    private EmployeeService service;

    @PostMapping
    public Employee addEmployee(@RequestBody Employee e) {
        return service.addEmployee(e);
    }

    @GetMapping
    public List<Employee> getAllEmployees() {
        return service.getAllEmployees();
    }

    @GetMapping("/{id}")
    public Employee getEmployee(@PathVariable int id) {
        return service.getEmployeeById(id);
    }

    @DeleteMapping("/{id}")
    public void deleteEmployee(@PathVariable int id) {
        service.deleteEmployee(id);
    }
}
```
**Exam tip:** `@RequestBody` tells Spring to convert incoming JSON into an `Employee` object automatically; `@PathVariable` extracts a value from the URL path (like `/api/employees/5` → `id = 5`). This automatic JSON↔object conversion is done by Spring's built-in **Jackson** integration.

---
---

## 📘 L11 — Servlet-based CRUD (Quiz/Game-style App)

### 11.1 — `HttpServletRequest` vs `HttpServletResponse`, and Session handling

**Definitions:**
- **`HttpServletRequest`** — represents the **incoming** HTTP request; used to **read** data (form parameters via `getParameter()`, headers, session, etc.).
- **`HttpServletResponse`** — represents the **outgoing** HTTP response; used to **write** data back (set content type, write HTML/JSON, set status codes, redirect).
- **`HttpSession`** — a server-side object that persists data **across multiple requests from the same client** (e.g., tracking a user's current score across quiz questions), obtained via `req.getSession()`. Without session tracking, each HTTP request is stateless and independent — the server would have no memory of the previous question or score.

**Why sessions matter for a quiz game specifically:** HTTP is stateless by nature — a fresh request carries no memory of past requests. To track a running score as the user answers multiple questions (multiple separate HTTP requests), the server needs `HttpSession` to store the score attribute in between requests, tied to that specific browser/client via a session cookie (`JSESSIONID`).

### 11.2 — `doGet()` vs `doPost()`

| Aspect | `doGet()` | `doPost()` |
|---|---|---|
| Data visibility | Parameters appended to the URL (visible, bookmarkable) | Parameters sent in the request body (hidden from URL) |
| Data size limit | Limited by URL length | Effectively unlimited (large form data OK) |
| Idempotent / safe? | Yes — meant for retrieving data, no side effects expected | No — meant for submitting data that changes server state |
| Typical use in quiz app | Loading/displaying a question | Submitting the answer to a question |

### 11.3 — Practical: Quiz Servlet using session to track score

```java
import javax.servlet.*;
import javax.servlet.http.*;
import java.io.*;

public class QuizServlet extends HttpServlet {

    // doGet — display a question
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp)
            throws ServletException, IOException {

        HttpSession session = req.getSession();
        if (session.getAttribute("score") == null) {
            session.setAttribute("score", 0);   // initialize score on first visit
        }

        resp.setContentType("text/html");
        PrintWriter out = resp.getWriter();
        out.println("<h2>What is the capital of Bangladesh?</h2>");
        out.println("<form method='post'>");
        out.println("<input type='text' name='answer'/>");
        out.println("<input type='submit' value='Submit'/>");
        out.println("</form>");
        out.println("<p>Current Score: " + session.getAttribute("score") + "</p>");
    }

    // doPost — check answer, update score in session
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp)
            throws ServletException, IOException {

        HttpSession session = req.getSession();
        String answer = req.getParameter("answer");
        int score = (int) session.getAttribute("score");

        if (answer != null && answer.trim().equalsIgnoreCase("Dhaka")) {
            score += 10;
            session.setAttribute("score", score);
        }

        resp.setContentType("text/html");
        PrintWriter out = resp.getWriter();
        out.println("<h3>Updated Score: " + score + "</h3>");
        out.println("<a href='quiz'>Next Question</a>");
    }
}
```
**Exam tip:** Emphasize `session.getAttribute("score")` returning `null` on the very first request — always guard against `NullPointerException` by initializing it if absent, exactly as shown.

---
---

## 📘 L12 — GoF Design Patterns

### 12.1 — What are Design Patterns? The three categories

**Definition:** A **design pattern** is a general, reusable, proven solution to a **commonly occurring problem** in software design. It's not finished code — it's a **template/blueprint** for how to structure classes and their interactions to solve a specific design problem cleanly. The "Gang of Four" (GoF) catalogued 23 classic patterns into three categories:

1. **Creational patterns** — deal with **object creation** mechanisms, trying to create objects in a way suitable to the situation (hiding the `new` keyword complexity). *Examples:* Singleton, Factory Method, Builder, Abstract Factory, Prototype.
2. **Structural patterns** — deal with **class/object composition** — how to assemble classes and objects into larger structures while keeping them flexible. *Examples:* Adapter, Decorator, Facade, Composite, Proxy.
3. **Behavioral patterns** — deal with **communication/responsibility distribution** between objects. *Examples:* Observer, Strategy, Command, State, Template Method.

### 12.2 — Singleton Pattern (Creational)

**Definition:** Ensures a class has **only one instance** throughout the application, and provides a **single global access point** to it. Useful for things like a configuration manager, logging service, or a shared database connection pool — where having multiple instances would waste resources or cause inconsistent state.

**Key implementation ingredients:**
- A `private static` instance variable.
- A `private` constructor (prevents `new` from outside the class).
- A `public static` method (e.g., `getInstance()`) that creates the instance only if it doesn't exist yet, then always returns that same instance.

```java
class DatabaseConnection {
    private static DatabaseConnection instance;

    private DatabaseConnection() {           // private constructor blocks external "new"
        System.out.println("Database connection created.");
    }

    public static DatabaseConnection getInstance() {
        if (instance == null) {
            instance = new DatabaseConnection();
        }
        return instance;
    }

    public void query(String sql) {
        System.out.println("Executing: " + sql);
    }
}

public class Main {
    public static void main(String[] args) {
        DatabaseConnection db1 = DatabaseConnection.getInstance();
        DatabaseConnection db2 = DatabaseConnection.getInstance();

        db1.query("SELECT * FROM users");

        System.out.println("Same instance? " + (db1 == db2));  // true
    }
}
```
**Output:** `"Database connection created."` prints only **once** — proving both `db1` and `db2` point to the same object.

### 12.3 — Observer Pattern (Behavioral)

**Definition:** Defines a **one-to-many dependency** between objects, so that when one object (the **Subject**) changes state, **all its dependents (Observers) are automatically notified and updated** — without the Subject needing to know the concrete details of each observer. Classic real-world analogy: a **YouTube channel (Subject)** and its **subscribers (Observers)** — when the channel uploads a new video, every subscriber gets notified automatically.

```java
import java.util.*;

// Observer interface
interface Observer {
    void update(String news);
}

// Subject
class NewsChannel {
    private List<Observer> subscribers = new ArrayList<>();

    public void subscribe(Observer o) {
        subscribers.add(o);
    }

    public void publishNews(String news) {
        System.out.println("Publishing: " + news);
        for (Observer o : subscribers) {
            o.update(news);      // notify every subscriber automatically
        }
    }
}

// Concrete Observer
class Subscriber implements Observer {
    private String name;
    public Subscriber(String name) { this.name = name; }

    @Override
    public void update(String news) {
        System.out.println(name + " received update: " + news);
    }
}

public class Main {
    public static void main(String[] args) {
        NewsChannel channel = new NewsChannel();

        Subscriber s1 = new Subscriber("Ayon");
        Subscriber s2 = new Subscriber("Rafi");

        channel.subscribe(s1);
        channel.subscribe(s2);

        channel.publishNews("Java 25 has been released!");
    }
}
```
**Sample output:**
```
Publishing: Java 25 has been released!
Ayon received update: Java 25 has been released!
Rafi received update: Java 25 has been released!
```

**Exam tip — why it's called "loosely coupled":** `NewsChannel` only depends on the `Observer` *interface*, never on the concrete `Subscriber` class. You can add/remove any number of new observer types without ever modifying `NewsChannel`'s code — this satisfies the **Open/Closed Principle** (open for extension, closed for modification).

---

*End of Part 3 (L9–L12). This completes the full answer notebook (L1–L12). If any question was worded slightly differently in your actual paper, tell me the exact wording and I'll adjust the answer to match it precisely.*


https://chatgpt.com/share/6a9ebccc-5d44-83ee-9bb0-627562379dc0?ogimg=plain
