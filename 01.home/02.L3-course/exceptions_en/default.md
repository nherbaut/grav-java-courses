---
content:
  items: '@self.listing'
feed:
  limit: 10
partials:
  breadcrumbs:
    toggle: false
  header_subtitle:
    toggle: true
  metadata:
    where: header
process:
  markdown: true
  twig: false
show_sidebar: false
sitemap:
  lastmod: 22-08-2024 13:32
title: Exceptions
---
# Exceptions

Handling error scenarios is a significant part of programming. The sources of errors in a program can be manifold:

* A physical or software malfunction of the execution environment. For example, an error may occur when trying to access a file or memory.
* A state achieved by an object that doesn’t correspond to an anticipated scenario. Like when an operation attempts to set a negative value when it’s not allowed by the software’s specifications.
* A programming error. For instance, invoking a method on a variable that’s **null**.
* And many other scenarios…

The resilience of an application is often viewed as its ability to continue delivering satisfactory service in a compromised environment, i.e., when not all expected conditions are met.

In Java, error management is conflated with handling exceptional scenarios. For this, the exception mechanism is used.

## What is an exception?

An exception is a Java class representing a unique state, which inherits directly or indirectly from the Exception class. Conventionally, the name of the class should clarify the type of exception and should end with Exception.

Examples of exception classes from the standard API:

[NullPointerException](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/NullPointerException.html)
: Indicates that a **null** reference is used to invoke a method or access a property.

[NumberFormatException](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/NumberFormatException.html)
: Indicates the inability to convert a string to a number as the string doesn’t represent a valid number.

[IndexOutOfBoundsException](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/IndexOutOfBoundsException.html)
: Signals an attempt to access an array index outside of the permitted values.

To create a custom exception, simply derive a class from the java.lang.Exception class.

```java
public class EndOfTheWorldException extends Exception {

  public EndOfTheWorldException() {
  }

  public EndOfTheWorldException(String message) {
    super(message);
  }
}
```

An exception, being an object, maintains its state and can thus store pertinent details about its occurrence.

```java
import java.time.Instant;

public class EndOfTheWorldException extends Exception {

  private Instant date;

  public EndOfTheWorldException() {
    this(Instant.now());
  }

  public EndOfTheWorldException(Instant date) {
    super("The end of the world occurred on " + date);
    this.date = date;
  }

  public Instant getDate() {
    return date;
  }
}
```

## Throwing an exception

In programming languages that lack the exception mechanism, one usually uses a return code or a boolean to check if a function or method executed correctly. This mechanism is somewhat tedious, meaning a developer must test all values returned by invoked functions or methods.

Exceptions allow the code handling the error to be isolated, enhancing source code readability.

When a program identifies an exceptional state, it can signal this by *throwing* an exception using the **throw** keyword.

```java
if(isDiabolicalPlanSucceeded()) {
  throw new EndOfTheWorldException();
}
```

When an exception is thrown, the regular execution flow of the method is interrupted until the point where the exception is handled. If no handling point is found, the program terminates.

## Handling an Exception

To handle an exception, you first need to define a block of code using the
keyword **try**. This block of code represents the flow of execution for which
you might want to *catch* an exception that would be thrown in order
to implement specific handling. The **try** block can be followed by one
or more **catch** blocks to intercept an exception of a specific type.

```java
try {
  if (hero == null) {
    throw new NullPointerException("The hero cannot be null!");
  }

  boolean victory = hero.fight(evilSpirit);
  boolean planThwarted = hero.defuse(evilMachine);

  if (!victory || !planThwarted) {
    throw new EndOfTheWorldException();
  }

  hero.setVictoriousPose();

} catch (EndOfTheWorldException eotwe) {
  // ...
}
```

In the above example, if the variable *hero* is **null**, the processing
of the **try** block is interrupted on line 3 by a [NullPointerException](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/NullPointerException.html).
Otherwise, the block continues to execute. Line 13 will only execute if the condition
on line 9 is false. However, if this condition is true, the block’s processing
is interrupted by the throwing of an *EndOfTheWorldException* and the
execution resumes in the **catch** block from line 16.

The **catch** block both identifies the type of exception concerned
by the handling block and declares a variable allowing
access to the exception during the **catch** block’s execution. A
**catch** block is executed if an exception of the same type or a subtype
as declared by the block is thrown during execution. Beware, if an exception
triggers the handling of a **catch** block, the execution flow resumes
after the **catch** blocks.

```java
try {
  if (hero == null) {
    throw new NullPointerException("The hero cannot be null!");
  }

  boolean victory = hero.fight(evilSpirit);
  boolean planThwarted = hero.defuse(evilMachine);

  if (!victory || !planThwarted) {
    throw new EndOfTheWorldException();
  }

  hero.setVictoriousPose();

} catch (Exception e) {
  // ...
}
```

In the above code, the **catch** block is associated with exceptions of type
[Exception](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Exception.html). As all exceptions in Java inherit directly or indirectly
from this class, this block will execute to handle the [NullPointerException](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/NullPointerException.html)
on line 3 or the *EndOfTheWorldException* on line 10.

**catch** blocks are considered during execution in the order of their
declaration. Declaring a **catch** block for a parent exception before
a **catch** block for a child exception is considered a compilation error.

```java
try {
  if (hero == null) {
    throw new NullPointerException("The hero cannot be null!");
  }

  boolean victory = hero.fight(evilSpirit);
  boolean planThwarted = hero.defuse(evilMachine);

  if (!victory || !planThwarted) {
    throw new EndOfTheWorldException();
  }

  hero.setVictoriousPose();

} catch (Exception e) {
  // ...
} catch (EndOfTheWorldException eotwe) {
  // COMPILATION ERROR
}
```

In the previous example, it’s important to understand that [Exception](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Exception.html) is the parent
class of *EndOfTheWorldException*. Thus, if an *EndOfTheWorldException* type exception
is thrown, only the first **catch** block will execute. The second is
therefore simply dead code and will generate a compilation error. To make it work,
the order of the **catch** blocks needs to be reversed.

```java
try {
  if (hero == null) {
    throw new NullPointerException("The hero cannot be null!");
  }

  boolean victory = hero.fight(evilSpirit);
  boolean planThwarted = hero.defuse(evilMachine);

  if (!victory || !planThwarted) {
    throw new EndOfTheWorldException();
  }

  hero.setVictoriousPose();

} catch (EndOfTheWorldException eotwe) {
  // ...
} catch (Exception e) {
  // ...
}
```

Now, a first **catch** block provides specific handling for
exceptions of type *EndOfTheWorldException* or its child types, and a second
**catch** block handles all other exceptions.

Sometimes, the code inside the **catch** block is the same for different types of exceptions.
If these exceptions have a common parent class, it’s possible to declare
a **catch** block simply for this parent class to avoid code duplication. In our example, the common ancestor between [NullPointerException](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/NullPointerException.html)
and *EndOfTheWorldException* is the [Exception](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Exception.html) class. Therefore, if we declare a **catch** block
for the [Exception](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Exception.html) type, we are providing a handling block for
all types of exceptions, which isn’t really the intended goal. In
such a situation, you can specify multiple exception types in
the **catch** block, separated by **|**:

```java
try {
  if (hero == null) {
    throw new NullPointerException("The hero cannot be null!");
  }

  boolean victory = hero.fight(evilSpirit);
  boolean planThwarted = hero.defuse(evilMachine);

  if (!victory || !planThwarted) {
    throw new EndOfTheWorldException();
  }

  hero.setVictoriousPose();

} catch (NullPointerException | EndOfTheWorldException ex) {
  // common handling for both exception types...
}
```

#### NOTE
The execution of a **catch** block can very well be interrupted by an exception.
The execution of a **catch** block can even lead to the rethrowing of the
exception that was just caught.

## Exception Propagation

If an exception isn’t caught by a **catch** block, then it moves up
the call stack until a **catch** block takes care of the exception.
If the exception moves to the top of the thread’s call stack, then the thread
stops. If it’s the main thread, then the application stops
with an error.

The propagation mechanism allows for separation of the application part that generates
the exception from the part that handles it.

Taking our previous example, it can be greatly improved.
Indeed, the *fight* and *defuse* methods should interrupt
with an exception rather than returning a boolean. The thrown exception
carries richer information than a mere boolean as it has a type
and an internal state.

```java
try {
  if (hero == null) {
    throw new NullPointerException("The hero cannot be null!");
  }

  hero.fight(evilSpirit);
  hero.defuse(evilMachine);
  hero.setVictoriousPose();

} catch (EndOfTheWorldException ex) {
  // ...
}
```

The code becomes much clearer. It’s understood that the **try** block can
be interrupted by an exception of type *EndOfTheWorldException* and
the block’s code isn’t cluttered with variables and **if** statements
specifically used for error management.

Java requires that methods signal the types of exceptions
they can throw. So, the above code will only compile if at least
one of the **try** block’s statements can generate an *EndOfTheWorldException*.
This allows the compiler to detect any dead code. The declaration of
exceptions thrown by a method is part of its signature and uses
the **throws** keyword.

```java
public class Hero {

  public void fight(Villain villain) throws EndOfTheWorldException {
    // ...
  }

  public void defuse(Trap trap) throws EndOfTheWorldException {
    // ...
  }

  public void setVictoriousPose() {
    // ...
  }
}
```

Thanks to exceptions, it’s now possible to interrupt a method. You
can even interrupt a constructor. This will halt the object’s construction
and thus prevent having an instance in an invalid state.

```java
public class Hero {

  public Hero(String characterClass) throws InvalidCharacterClassException {
    if (characterClass == null || "".equals(characterClass)) {
      throw new InvalidCharacterClassException();
    }
  }
```

The declaration of exceptions in a method’s signature serves both
to document the method’s behavior in the code itself while
ensuring at compile time that exception cases are handled by the code.

```java
public Merchandise buy(long amount, Currency currency)
  throws InsufficientCreditException, RefusedCurrencyException,
         UnavailableMerchandiseException {
  // ...
}
```

In the above example, even without access to the source code, the signature
alone informs about the error cases one might encounter
when calling the *buy* method.

## Exceptions and Polymorphism

Since the declaration of exceptions thrown by a method is part of its signature, certain rules must be respected for method overriding to ensure proper polymorphism.

According to the 

```
`Liskov substitution principle`_
```

, in method overriding, preconditions cannot be strengthened by the subclass, and postconditions cannot be weakened by the subclass. Relating this to exception mechanisms, it means that an overridden method cannot throw additional exceptions. However, it can throw more specific exceptions. Java does not distinguish between exceptions that signal a breach of preconditions or postconditions. Thus, it’s up to developers to ensure postconditions are not weakened in the subclass.

Thus, if the *SuperHero* class inherits from the *Hero* class, it can override methods without declaring any exceptions.

```java
public class SuperHero extends Hero {

  @Override
  public void fight(Villain villain) {
    // ...
  }

  @Override
  public void defuse(Trap trap) {
    // ...
  }
}
```

This new class can also change the exception types declared by overridden methods, provided these types are child classes of the original exceptions.

```java
public class SuperHero extends Hero {

  @Override
  public void defuse(Trap trap) throws DiabolicalPlanException {
    // ...
  }

}
```

The preceding code compiles only if the *DiabolicalPlanException* directly or indirectly inherits from *EndOfTheWorldException*.

```java
public class DiabolicalPlanException extends EndOfTheWorldException {
  // ...
}
```

#### NOTE
Even if it’s clumsy, it’s possible to keep the exception declaration in the signature even if the method doesn’t throw these exception types. The compiler doesn’t verify if a method effectively throws all exception types declared in its signature.

## The finally Block

Following the **catch** blocks, a **finally** block can be declared. A **finally** block is always executed, whether the **try** block terminated normally or due to an exception.

#### NOTE
If a **try** block ends due to an exception and there’s no appropriate **catch** block, the **finally** block is executed, and then the exception is propagated.

```java
try {
  if (hero == null) {
    throw new NullPointerException("The hero cannot be null!");
  }

  hero.fight(evilSpirit);
  hero.defuse(evilMachine);
  hero.setVictoriousPose();

} catch (EndOfTheWorldException eotwe) {
  // ...
} finally {
  // This block will always be executed
  playEndCredits();
}
```

#### NOTE
A **finally** block executes even if the **try** block executes a **return** statement. In this case, the **finally** block is executed first, followed by the **return** statement.

The **finally** block is most commonly used for managing resources other than memory. If the program opens a connection or a file, the processing is done in the **try** block, and then the **finally** block takes care of releasing the resource.

```java
java.io.FileReader reader = new java.io.FileReader(filename);
try {
  int nbCharRead = 0;
  char[] buffer = new char[1024];
  StringBuilder builder = new StringBuilder();
  // reader.read call might throw a java.io.IOException
  while ((nbCharRead = reader.read(buffer)) >= 0) {
    builder.append(buffer, 0, nbCharRead);
  }
  // Explicit return doesn't prevent the execution of the finally block.
  return builder.toString();
} finally {
  // This block is mandatorily executed after the try block.
  // Thus, the file's read stream is closed before the method's return.
  reader.close();
}
```

## The try-with-resources

Resource management can also be done with the [try-with-resources](https://docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html) syntax.

```java
try (java.io.FileReader reader = new java.io.FileReader(filename)) {
  int nbCharRead = 0;
  char[] buffer = new char[1024];
  StringBuilder builder = new StringBuilder();
  while ((nbCharRead = reader.read(buffer)) >= 0) {
    builder.append(buffer, 0, nbCharRead);
  }
  return builder.toString();
}
```

After the **try** keyword, one or more variable initializations are declared in parentheses. These variables must be of a type that implements the [AutoCloseable](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/AutoCloseable.html) or [Closeable](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/io/Closeable.html) interfaces. These interfaces declare only one method: **close**. The compiler automatically adds a **finally** block after the **try** block to call the **close** method on each variable that isn’t **null**.

For this code:

```java
try (java.io.FileReader reader = new java.io.FileReader(filename)) {
  // ...
}
```

The compiler will produce bytecode corresponding to:

```java
{
  java.io.FileReader reader = new java.io.FileReader(filename)
  try {
    // ...
  } finally {
    if (reader != null) {
      reader.close();
    }
  }
}
```

The [try-with-resources](https://docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html) syntax is both easy to read and helps avoid forgetting to release resources, as the compiler automatically inserts the code for us.

## Caused by Exceptions

It’s often beneficial to encapsulate one exception within another. For example, consider a method that attempts a remote operation on a server. If the remote server is unreachable, the program would need to catch an IOException_. But this might not be very meaningful to the rest of the application. Instead, the method could opt to throw an application-defined exception, like OperationNonDisponibleException.

```java
public class OperationNonDisponibleException extends Exception {

  public OperationNonDisponibleException(Exception cause) {
    super(cause);
  }
}
```

While this exception doesn’t inherit from IOException_, it does expose a constructor that accepts an exception. This indicates that the exception was caused by another exception.

```java
try {

  // ... input/output operations to the server

} catch (IOException ioe) {
  throw new OperationNonDisponibleException(ioe);
}
```

The Exception_ class provides the getCause_ method (inherited from Throwable_) to determine the exception causing the current issue.

## Errors and Runtime Exceptions

Examining the exception hierarchy in detail reveals the following inheritance model:

![image](langage_java/images/exceptions/hierarchie_exception.png)

The Throwable_ class indicates that types of this kind can be used with the **throw** keyword. Furthermore, Throwable_ provides utility methods. For example, the printStackTrace_ method displays the application’s call stack on the standard error output.

```java
try {
  double d = 1/0; // results in an ArithmeticException
} catch (ArithmeticException e) {
  // Display the call stack on the standard error output
  e.printStackTrace();
}
```

The Error_ class inherits from Throwable_ just like Exception_. Error_ is the base class for serious errors that the application shouldn’t intercept. When such an error occurs, it typically means that the JVM’s runtime environment is unstable. For instance, the OutOfMemoryError_ class indirectly inherits from this class, indicating that the JVM no longer has enough memory (typically to allocate space for new objects).

The RuntimeException_ class signifies execution problems mostly resulting from application bugs. Subclasses of this exception class include:

- ArithmeticException_: Indicates an invalid arithmetic operation, such as a division by zero.
- NullPointerException_: Indicates an attempt to access a method or attribute through a **null** reference.
- ClassCastException_: Indicates an invalid typecast was attempted.

Generally, exceptions inheriting from RuntimeException_ aren’t caught or handled by the application. At most, they are caught at the top of the call stack to notify users of an error or log the incident.

Classes like Error_, RuntimeException_, and their subclasses are termed *unchecked exceptions*. This means the compiler doesn’t require these exceptions to be declared in method signatures. They typically represent serious JVM internal issues or bugs. Virtually all Java methods might throw such exceptions.

Returning to our vehicle example, the methods to accelerate and decelerate should ensure the passed parameter is a positive number. If not, they can throw an IllegalArgumentException_, a runtime exception provided by the standard API, signaling an invalid parameter. This exception doesn’t need to be declared in the method’s signature.

```java
package {{ROOT_PKG}}.conduite;

public class Vehicule {

  private final String marque;
  protected float vitesse;

  public Vehicule(String marque) {
    this.marque = marque;
  }

  public void accelerer(float deltaVitesse) {
    if (deltaVitesse < 0) {
      throw new IllegalArgumentException("deltaVitesse must be positive");
    }
    this.vitesse += deltaVitesse;
  }

  public void decelerer(float deltaVitesse) {
    if (deltaVitesse < 0) {
      throw new IllegalArgumentException("deltaVitesse must be positive");
    }
    this.vitesse = Math.max(this.vitesse - deltaVitesse, 0f);
  }

  // ...

}
```

#### NOTE
It’s still useful to document runtime exceptions caused by precondition or postcondition violations. This directly documents these conditions in the code.

```java
/**
 * Accelerates the vehicle.
 *
 * @param deltaVitesse the speed to add to the current speed.
 * @throws IllegalArgumentException if deltaVitesse is a negative number.
 */
public void accelerer(float deltaVitesse) throws IllegalArgumentException {
  if (deltaVitesse < 0) {
    throw new IllegalArgumentException("deltaVitesse must be positive");
  }
  this.vitesse += deltaVitesse;
}
```

In contrast, all other exceptions are termed *checked exceptions*. Any method that might propagate a *checked exception* must declare it in its signature using the **throws** keyword.

## Choice between checked and unchecked

As developers, when we create new classes to represent exceptions, we have the choice of inheriting from the Exception class or the RuntimeException class. This means choosing between a *checked* and an *unchecked* exception. The distinction between the two categories has evolved over the various versions of Java.

#### NOTE
One should never create a class that inherits from Error. Classes inheriting from it are intended to signal a problem in the JVM.

It is generally considered preferable to create an *unchecked exception* when the exception represents a technical error, an event that doesn’t fall under the application’s domain but is rather related to its execution context. Typically, these are exceptions that the application can’t handle adequately other than to signal a problem to the users or administrators. For instance, if your application connects to a remote service and that service doesn’t respond, you might need to create an RemoteServiceUnavailableException exception. This type of exception is likely an *unchecked exception* and should inherit from RuntimeException.

On the other hand, exceptions that may have significance for the application’s domain should be *checked exceptions*. Typically, they represent specific states identified by domain analysts.

For example, if you are developing a banking application to perform transactions, some transactions might fail because a bank account isn’t adequately funded. To represent this state, one might create a SoldeInsuffisantException (InsufficientFundsException). It’s likely that this exception should be a *checked exception* so the compiler can ensure it’s appropriately handled.