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
title: Abstract Interfaces
---
# Abstract Interfaces

An interface defines a set of services that a client can obtain from an object. An interface introduces a pure abstraction that allows for maximum decoupling between a service and its implementation. We often find interfaces at the heart of many libraries and frameworks. The mechanism of interfaces also introduces a simplified form of multiple inheritance.

## Declaring an Interface

An interface is declared using the **interface** keyword.

```java
public interface Account {

}
```

Just like a class, an interface has a scope, a name, and a declaration block. An interface is declared in its own file, which has the same name as the interface. For the example above, the file should be named *Account.java*.

An interface describes a set of methods by providing only their signatures.

```java
public interface Account {

  void deposit(int amount) throws InterruptedOperationException,
                                   AccountLockedException;

  int withdraw(int amount) throws InterruptedOperationException,
                                  AccountLockedException;

  int getBalance() throws InterruptedOperationException;

}
```

An interface introduces a new abstraction type that defines, through its methods, a set of permitted interactions. A class can then implement one or more interfaces.

#### NOTE
The methods of an interface are by default **public** and **abstract**. It is not possible to declare a different scope than **public**.

## Implementing an Interface

A class indicates the interfaces it implements using the **implements** keyword.
A concrete class must provide an implementation for all methods of an interface, either within its declaration or through inheritance.

```java
public class BankAccount implements Account {

  private final String number;
  private int balance;

  public BankAccount(String number) {
    this.number = number;
  }

  @Override
  public void deposit(int amount) {
    this.balance += amount;
  }

  @Override
  public int withdraw(int amount) throws InterruptedOperationException {
    if (balance < amount) {
      throw new InterruptedOperationException();
    }
    return this.balance -= amount;
  }

  @Override
  public int getBalance() {
    return this.balance;
  }

  public String getNumber() {
    return number;
  }
}
```

Implementing methods of an interface follows the same rules as overwriting.

#### NOTE
If the class implementing the interface is an abstract class, then it is not
required to provide an implementation for the methods of the interface.

Even if the mechanisms of interfaces are close to those of abstract classes,
these two concepts are clearly distinct. An abstract class allows to share
an implementation within an inheritance hierarchy by introducing a more abstract type.
An interface allows defining possible interactions between an object and its clients.
An interface acts as a contract that both parties must fulfill. As the interface does not impose fitting into an inheritance hierarchy, it is relatively simple to adapt a class to implement an interface.

An interface introduces a new type of relationship that would be like *is-like-a*.

For example, it is possible to create an account management system using the *Account* interface. It is then easy to provide an implementation of this interface for a bank account, an electronic wallet, an online account, etc.

A class can implement multiple interfaces if necessary. To do this, simply list the names of the interfaces separated by a comma.

```java
package {{ROOT_PKG}}.animal;

public interface Carnivorous {

  void eat(Animal animal);

}
```

```java
package {{ROOT_PKG}}.animal;

public interface Herbivorous {

  void eat(Plant plant);

}
```

```java
package {{ROOT_PKG}}.animal;

public class Human extends Animal implements Carnivorous, Herbivorous {

  @Override
  public void eat(Animal animal) {
    // ...
  }

  @Override
  public void eat(Plant plant) {
    // ...
  }
}
```

In the above example, the *Human* class implements the *Carnivorous* and *Herbivorous* interfaces. Thus, an instance of the *Human* class can be used in an application wherever the *Carnivorous* and *Herbivorous* types are expected.

```java
Humain humain = new Humain();

Carnivore carnivore = humain;
carnivore.manger(new Poulet()); // Poulet inherits from Animal

Herbivore herbivore = humain;
herbivore.manger(new Chou());   // Chou inherits from Vegetal
```

## Attributes and Static Methods

An interface can declare attributes. However, all attributes of an
interface are by default **public**, **static**, and **final**. It’s
not possible to modify the scope of these attributes. In other
words, an interface can only declare constants.

```java
public interface Compte {

   int PLAFOND_DEPOT = 1_000_000;

   void deposer(int montant) throws OperationInterrompueException, CompteBloqueException;

   int retirer(int montant) throws OperationInterrompueException, CompteBloqueException;

   int getBalance() throws OperationInterrompueException;

}
```

#### NOTE
One can specify **public**, **static**, and **final** in the declaration of an
interface attribute:

```java
public static final int PLAFOND_DEPOT = 1_000_000;
```

This is strictly equivalent to:

```java
int PLAFOND_DEPOT = 1_000_000;
```

An interface can also declare **static** methods. In this case,
these are methods equivalent to class methods, and the interface must
provide an implementation for these methods. These methods must explicitly
have the **static** keyword and they are public by default.

```java
public interface Compte {

   int PLAFOND_DEPOT = 1_000_000;

   static int getBalanceTotale(Compte... comptes) throws OperationInterrompueException {
      int total = 0;
      for (Compte c : comptes) {
         total += c.getBalance();
      }
      return total;
   }

   void deposer(int montant) throws OperationInterrompueException, CompteBloqueException;

   int retirer(int montant) throws OperationInterrompueException, CompteBloqueException;

   int getBalance() throws OperationInterrompueException;

}
```

## Interface Inheritance

An interface can inherit from other interfaces. Unlike classes which
can only have one parent class, an interface can have as many parent
interfaces as needed. To declare inheritance, the **extends** keyword is used.

```java
package {{ROOT_PKG}}.animal;

public interface Omnivore extends Carnivore, Herbivore {

}
```

A concrete class that implements an interface must therefore provide an implementation
for the methods of this interface as well as for all the methods of
the interfaces from which it inherits.

```java
package {{ROOT_PKG}}.animal;

public class Humain extends Animal implements Omnivore {

   @Override
   public void manger(Animal animal) {
      // ...
   }

   @Override
   public void manger(Vegetal vegetal) {
      // ...
   }

}
```

Interface inheritance introduces new types by aggregation. In
the example above, the notion of omnivore appears simply
as both a carnivore and an herbivore.

<a id="interface-marker"></a>

## Marker Interfaces

Since every interface introduces a new type, it’s possible to check
using the **instanceof** keyword whether a variable, parameter, or attribute
is indeed an instance compatible with this interface.

```java
Humain bob = new Humain();
if (bob instanceof Carnivore) {
  System.out.println("bob eats meat");
}
```

In Java, this capability is used to create marker interfaces. A
marker interface typically has no methods; it’s just used to introduce
a new type. It’s then possible to change the behavior of a method
if a variable, parameter, or attribute implements this interface.

```java
package {{ROOT_PKG}}.animal;

public interface Cannibale {
}
```

```java
package {{ROOT_PKG}}.animal;

public class Humain extends Animal implements Omnivore {

   @Override
   public void manger(Animal animal) {
      if (!(animal instanceof Humain) || this instanceof Cannibale) {
         // ...
      }
   }

   @Override
   public void manger(Vegetal vegetal) {
      // ...
   }

}
```

In the above example, *Cannibale* acts as a marker interface, allowing
a class inheriting from *Humain* to eat a *Humain* instance. To do this,
just declare that this new class implements *Cannibale*:

```java
package {{ROOT_PKG}}.animal;

public class Anthropophage extends Humain implements Cannibale {

}
```

Even if the *Anthropophage* class doesn’t redefine any method of its parent
class, declaring the marker interface *Cannibale* modifies its behavior.

The marker interface principle is sometimes used in the standard Java API.
For example, the [clone](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#clone--) method declared by [Object](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html) throws a
[CloneNotSupportedException](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/CloneNotSupportedException.html) if called on an instance that doesn’t implement
the [Cloneable](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Cloneable.html) interface. This allows for a default method to create
a copy of an object without enabling the functionality. The class
must declare its intention to be cloneable using the marker interface.

## Default Implementation

Sometimes it’s challenging to evolve an application that intensively
uses interfaces. Let’s reconsider our *Compte* example. Suppose we want
to add the *transfer* method, which transfers the balance from one account to another.

```java
public interface Compte {

   void deposer(int montant) throws OperationInterrompueException,
                                    CompteBloqueException;

   int retirer(int montant) throws OperationInterrompueException,
                                   CompteBloqueException;

   int getBalance() throws OperationInterrompueException;

   void transferer(Compte destination) throws OperationInterrompueException,
                                              CompteBloqueException;

}
```

By adding a new method to our interface, we must provide an implementation
for this method in all the classes we created to keep them compiling.
However, if other development teams use our code and have also created
implementations for the *Compte* interface, they will need to adapt their
code when integrating the latest version of our interface.

As interfaces are specifically used to decouple two implementations, they’re
often used in libraries and frameworks. On the one hand, interfaces introduce
greater flexibility, but on the other hand, they lead to significant rigidity.
It can be challenging to evolve them without risking breaking existing implementations.

To partially address this issue, an interface can provide a default
implementation for its methods. Thus, if a concrete class implementing
this interface doesn’t implement a default method, the interface’s code
will execute. A default method must necessarily have the **default** keyword
in its signature.

```java
public interface Compte {

   void deposer(int montant) throws OperationInterrompueException,
                                    CompteBloqueException;

   int retirer(int montant) throws OperationInterrompueException,
                                   CompteBloqueException;

   int getBalance() throws OperationInterrompueException;

   default void transferer(Compte destination) throws OperationInterrompueException,
                                                      CompteBloqueException {
      if (destination == this) {
         return;
      }
      int montant = this.getBalance();
      if (montant <= 0) {
         return;
      }
      destination.deposer(montant);
      boolean retraitOk = false;
      try {
         this.retirer(montant);
         retraitOk = true;
      } finally {
         if (!retraitOk) {
            destination.retirer(montant);
         }
      }
   }

}
```

A class implementing *Compte* doesn’t need to provide an implementation
for the *transferer* method. The *CompteBancaire* class we implemented
at the beginning of this chapter will continue to compile and work as
expected while having an additional method.

## Interface Segregation

In object-oriented programming, the 

```
`Interface Segregation Principle`_
```

 states
that a client should not be forced to depend on methods it does not use from an object.
The goal is to limit the interactions between an object and its clients to the bare minimum,
ensuring minimal coupling and thus simplifying evolutions and refactoring.
In Java, the 

```
`Interface Segregation Principle`_
```

 has two consequences:

1. The type of variables, parameters, and attributes should be chosen judiciously
   to restrict them to the minimum necessary type in the code.
2. An interface should not declare *too many* methods.

The first point implies that it’s preferable to manipulate objects through their interfaces
rather than using the object’s actual type. A classic example in Java concerns the
collections API. These are classes that manage a collection of objects,
providing more advanced features than arrays. For example, the [java.util.ArrayList](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/ArrayList.html)
class manages a list of objects, allowing append, insert, delete, accessing an element by index,
and complete traversal.

A program that creates an [ArrayList](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/ArrayList.html) to store a set of elements will never use a variable of type
[ArrayList](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/ArrayList.html) but instead will use a variable of the type of an interface implemented by this class.

```java
// Using the List interface
List myList = new ArrayList();
```

```java
// Using the Collection interface
Collection myList = new ArrayList();
```

```java
// Using the Iterable interface
Iterable myList = new ArrayList();
```

The more an application part relies on interfaces to interact with other parts of an application,
the easier it is to introduce new classes implementing the expected interfaces and use them directly.

The second point is related to the [SOLID](https://fr.wikipedia.org/wiki/SOLID_%28informatique%29) principle of 

```
`single responsibility`_
```

.
An interface is designed to represent a type of relationship between the class
implementing it and its clients. The more methods an interface has, the more likely
it represents multiple types of relationships. In such cases, inheritance between interfaces
and/or implementing multiple interfaces become a good solution to isolate each relationship.

Let’s revisit our example of the *Compte* interface. If our system consists of a consultation subsystem,
a withdrawal subsystem, and an account management subsystem, then this interface
should probably be separated into multiple interfaces to isolate each responsibility.

An interface used by the consultation subsystem:

```java
public interface CompteConsultable {

  int getBalance() throws OperationInterrompueException;

}
```

An interface used by the withdrawal subsystem:

```java
public interface OperationDeRetrait {

  int retirer(int montant) throws OperationInterrompueException,
                                  CompteBloqueException;

}
```

A more complex interface used by the account management system:

```java
public interface Compte extends CompteConsultable, OperationDeRetrait {

  void deposer(int montant) throws OperationInterrompueException,
                                   CompteBloqueException;

  default void transferer(Compte destination) throws OperationInterrompueException,
                                                     CompteBloqueException {
    if (destination == this) {
      return;
    }
    int montant = this.getBalance();
    if (montant <= 0) {
      return;
    }
    destination.deposer(montant);
    boolean retraitOk = false;
    try {
      this.retirer(montant);
      retraitOk = true;
    } finally {
      if (!retraitOk) {
        destination.retirer(montant);
      }
    }
  }
}
```

## Dependency Inversion

When we learned about constructors, we saw that we could achieve
dependency injection by passing the necessary objects
as constructor parameters rather than letting the new instance create these objects itself.
With the concept of interfaces, we can achieve dependency injection by completely decoupling
the use of the injected object from its implementation.

If we want to create a class to represent a bank transaction,
we can implement it as follows:

```java
import java.time.Instant;

public class TransactionBancaire {

  private final Compte compte;
  private final int montant;
  private Instant date;

  public TransactionBancaire(Compte compte, int montant) {
    this.compte = compte;
    this.montant = montant;
  }

  public void effectuer() throws OperationInterrompueException, CompteBloqueException {
    if (isEffectuee()) {
      return;
    }
    compte.retirer(montant);
    date = Instant.now();
  }

  public void annuler() throws OperationInterrompueException, CompteBloqueException {
    if (! isEffectuee()) {
      return;
    }
    compte.deposer(montant);
    date = null;
  }

  public boolean isEffectuee() {
    return date != null;
  }

  public Instant getDate() {
    return date;
  }
}
```

In the above implementation, we achieve **dependency inversion**.
The transaction doesn’t know the exact nature of the *Compte* object it manipulates.
The *TransactionBancaire* class will work regardless of the underlying implementation
of the *Compte* interface.

Dependency inversion is a principle of object-oriented programming that states that
if a class A is dependent on class B, it may be desirable that not only should
class A receive an instance of B through injection, but also that B should only
be known through an interface.

Dependency inversion is often used to isolate the software layers of an architecture.
Within an application, there may be a set of classes to manage user operations
and another set of classes to ensure information persistence.

![image](langage_java/images/interface/dependency_inversion.png)

The software architecture can use dependency inversion to ensure that user operations
that need to perform persistent operations make calls through interfaces that are injected.
On one side, one can imagine implementing different classes for persistence to save
information in files, databases, or remote servers (or even nowhere if you want to run the code in a test environment).
On the other side, you can create and evolve a persistence system with minimal dependence
on user operations since the persistence system only needs to provide implementations
that comply with the interfaces.