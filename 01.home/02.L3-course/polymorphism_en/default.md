---
title: 'Polymorphism'
sitemap:
    lastmod: '22-08-2024 13:32'
partials:
    header_subtitle:
        toggle: true
    metadata:
        where: header
    breadcrumbs:
        toggle: false
feed:
    limit: 10
show_sidebar: false
process:
    markdown: true
    twig: false
content:
    items: '@self.listing'
---

# Polymorphism

Polymorphism is an important mechanism in object-oriented programming. It allows modifying
the behavior of a child class compared to its parent class. Polymorphism allows using
inheritance as an extension mechanism by adapting the behavior of objects.

# Principle of Polymorphism

Let’s take the example of the *Animal* class. This class provides a *crier* method.
To simplify our example, the method simply writes the animal’s cry to the standard output.

```java
public class Animal {

  public void crier() {
    System.out.println("an animal's cry");
  }

}
```

We also have the *Chat* and *Chien* (Cat and Dog) classes that inherit from the *Animal* class.

```java
public class Chat extends Animal {

}
```

```java
public class Chien extends Animal {

}
```

These two classes are specializations of the *Animal* class. As such, they can **override**
the *crier* method.

```java
public class Chat extends Animal {

  public void crier() {
    System.out.println("Miaou!");
  }

}
```

```java
public class Chien extends Animal {

  public void crier() {
    System.out.println("Woof woof!");
  }

}
```

Each child class changes the behavior of the *crier* method. This means that an object of type *Chien*
for which we invoke the *crier* method will not provide the same behavior as an object of type *Chat*.
And this is true regardless of the type of the variable referencing these objects.

```java
Animal animal = new Animal();
animal.crier(); // displays "an animal's cry"

Chat chat = new Chat();
chat.crier();   // displays "Miaou!"

Chien chien = new Chien();
chien.crier();  // displays "Woof woof!"

animal = chat;
animal.crier(); // displays "Miaou!"

animal = chien;
animal.crier(); // displays "Woof woof!"
```

The above code example shows that the implementation of the *crier* method depends on the real type of the object,
not the type of the variable referencing it.

# An Exception: Private Methods

Methods with a **private** scope do not support polymorphism. Indeed, a **private** method is only accessible
by the class that declares it. Therefore, if both a parent class and a child class define a **private** method
with the same signature, the compiler treats them as two different methods.

# Method Overriding and Signature

The principle of polymorphism relies on method overriding in Java. For method overriding to work, the method
that overrides must have a *matching* signature to that of the original method.

The simplest case is the one in the previous example. The methods have exactly the same signature: the same scope,
the same return type, the same name, and the same parameters.

However, the method that overrides can have a slightly different signature.

A method that overrides can have a different scope only if it is more permissive than the scope of the original method.
So, it’s possible to increase the scope of the method but never decrease it:

* A method with a package scope can be overridden with a package scope, **protected**, or **public** scope.
* A **protected** method can be overridden with a **protected** or **public** scope.
* A **public** method can only be overridden with a **public** scope.

Changing the scope in the override is typically used to place an implementation in the parent class
but allow child classes to offer public access to this method.

In the previous chapter, we introduced the *Vehicule*, *Voiture* (Car), and *Moto* (Motorcycle) classes.
Assuming that only instances of *Voiture* can offer the *reculer* (reverse) method, we added this method to the *Voiture* class.
To do this, we had to modify the implementation of the *Vehicule* class by using a **protected** scope for the *vitesse* attribute.
We then saw that this was not entirely compliant with the 

```
`Open/Closed Principle`_
```

.

```java
public class Vehicule {

  private final String marque;
  protected float vitesse;

  public Vehicule(String marque) {
    this.marque = marque;
  }

  public void accelerer(float deltaVitesse) {
    this.vitesse += deltaVitesse;
  }

  public void decelerer(float deltaVitesse) {
    this.vitesse = Math.max(this.vitesse - deltaVitesse, 0f);
  }

  // ...

}
```

```java
public class Voiture extends Vehicule {

  public Voiture(String marque) {
    super(marque);
  }

  public void reculer(float vitesse) {
    this.vitesse = -vitesse;
  }

  // ...

}
```

Now we can revisit our implementation. In fact, it’s the *reculer* method that should be declared in the *Véhicule* (Vehicle) class with a **protected** scope. The *Voiture* (Car) class can simply override this method and make it **public**.

```java
public class Vehicule {

  private final String marque;
  private float vitesse;

  public Vehicule(String marque) {
    this.marque = marque;
  }

  public void accelerer(float deltaVitesse) {
    this.vitesse += deltaVitesse;
  }

  public void decelerer(float deltaVitesse) {
    this.vitesse = Math.max(this.vitesse - deltaVitesse, 0f);
  }

  protected void reculer(float vitesse) {
    this.vitesse = -vitesse;
  }

  // ...

}
```

```java
public class Voiture extends Vehicule {

  public Voiture(String marque) {
    super(marque);
  }

  public void reculer(float vitesse) {
    super.reculer(vitesse);
  }

  // ...

}
```

In the above example, the **super** keyword allows calling the implementation of the method provided by the *Vehicule* class. Thus, the *vitesse* attribute can remain **private**, and child classes of *Vehicule* can choose whether or not to provide public access to the *reculer* method.

A method that overrides can have a different return type from the original method, as long as it’s a subclass of the return type. If we revisit the inheritance example of the *Animal*, *Chien*, and *Chat* (Dog and Cat) classes, we can define an *EleveurAnimal* (AnimalBreeder) class that creates an instance of *Animal* when the *elever* (raise) method is called:

```java
public class EleveurAnimal {

  public Animal elever() {
    return new Animal();
  }

}
```

Now, if we create the *EleveurChien* (DogBreeder) class, which inherits from the *EleveurAnimal* class, the overridden *elever* method can declare that it returns a *Chien* (Dog) type. Since *Chien* inherits from *Animal*, the overriding method presents itself as a specialization.

```java
public class EleveurChien extends EleveurAnimal {

  public Chien elever() {
    return new Chien();
  }

}
```

# The super Keyword

Method overriding hides the method of the parent class. However, as we saw previously with the example of the *reculer* method, when a child class’s implementation calls a method from the parent class, it uses the **super** keyword. Calling the parent class’s implementation is very useful when you want to perform actions before and/or after the method call without duplicating the original code.

```java
public class Voiture extends Vehicule {

  public Voiture(String marque) {
    super(marque);
  }

  public void accelerer(float deltaVitesse) {
    // do something before

    super.accelerer(deltaVitesse);

    // do something after
  }

  // ...

}
```

However, there is a limitation: if a method has been overridden multiple times in the inheritance hierarchy, the **super** keyword can only call the implementation of the immediate parent class. If the *Voiture* class has overridden the *accelerer* method and we create the *VoitureDeCourse* (RaceCar) class, which inherits from *Voiture*, it is impossible to directly call the original implementation from the *Vehicule* class within the *VoitureDeCourse* class.

# The @Override Annotation

Annotations are special types in Java that start with **@**. Annotations are used to add information to a class, attribute, method, parameter, or variable. An annotation provides information at compile-time, during class loading into the JVM, or during code execution. The Java language itself uses annotations relatively sparingly. However, one commonly used annotation is @Override, which is very useful for polymorphism. This annotation is placed before the method signature to indicate that this method is an override of an inherited method. It allows the compiler to check that the method’s signature indeed matches that of a parent class’s method. If not, the compilation fails.

```java
public class Voiture extends Vehicule {

  public Voiture(String marque) {
    super(marque);
  }

  @Override
  public void reculer(float vitesse) {
    super.reculer(vitesse);
  }

  // ...

}
```

# Class Methods

Class methods (declared with the **static** keyword) do not support method overriding. If a child class declares a **static** method with the same signature as in the parent class, these methods will simply be seen by the compiler as two distinct methods.

```java
public class Parent {

   public static void classMethod() {
     System.out.println("Calling the method of the Parent class");
   }

}
```

```java
public class Child extends Parent {

   public static void classMethod() {
     System.out.println("Calling the method of the Child class");
   }

}
```

In the code above, the *Child* class inherits from the *Parent* class, and both of them implement a **static** method called *classMethod*. The following code can be confusing:

```java
Parent a = new Child();
a.classMethod();

Child b = new Child();
b.classMethod();
```

The result of executing this code is:

```text
Calling the method of the Parent class
Calling the method of the Child class
```

Since the methods are **static**, method overriding does not apply, and the method called depends on the type of the variable and not on the type of the object referenced by the variable. This example illustrates why it is strongly recommended to call **static** methods using the class name rather than a variable to avoid ambiguity.

```java
Parent.classMethod();
Child.classMethod();
```

# Final Method

A method can have the **final** keyword in its signature. This means that this method cannot be overridden by classes that inherit from it. Attempting to override a **final** method leads to a compilation error. Using the **final** keyword for a method is reserved for very specific (and rare) cases. For example, if you want to ensure that a method always has the same behavior even in inheriting classes.

#### NOTE
Even though **static** methods do not allow method overriding, they can still be declared as **final**. In this case, it is not possible to add a class method with the same signature in inheriting classes.

# Constructors and Polymorphism

Constructors are special methods that cannot be overridden. Constructors create a call sequence that ensures they are executed starting from the highest class in the inheritance hierarchy. Since all Java classes inherit from the *Object* class, this means that the constructor of *Object* is always called first.

However, a constructor can call a method, and in that case, polymorphism applies. Since constructors are called in the order of the inheritance hierarchy, this means that a constructor always invokes a overridden method before the child class implementing it has been initialized.

For example, if we have a *VehiculeMotorise* (MotorizedVehicle) class that overrides the *accelerer* method to account for fuel consumption:

```java
public class VehiculeMotorise extends Vehicule {

  private Moteur moteur;

  public VehiculeMotorise(String marque) {
    super(marque);
    this.moteur = new Moteur();
  }

  @Override
  public void accelerer(float deltaVitesse) {
    moteur.consommer(deltaVitesse);
    super.accelerer(deltaVitesse);
  }

  // ...

}
```

Now, if we enhance the *Vehicule* class to create an instance with a minimum speed:

```java
public class Vehicule {

  private final String marque;
  protected float vitesse;

  public Vehicule(String marque) {
    this.marque = marque;
    this.accelerer(10);
  }

  public void accelerer(float deltaVitesse) {
    this.vitesse += deltaVitesse;
  }

  // ...

}
```

What will happen when executing this code:

```java
VehiculeMotorise motorizedVehicle = new VehiculeMotorise("DeLorean");
```

The constructor of *VehiculeMotorise* starts by calling the constructor of *Vehicule*. The latter implicitly calls the constructor of *Object* (which does nothing), then initializes the *marque* attribute, and finally calls the *accelerer* method. Since this method is overridden, it’s actually the implementation provided by *VehiculeMotorise* that is called. This implementation starts by calling a method on the *moteur* (engine) attribute, which has not been initialized yet. Therefore, its value is null, and creating an instance of *VehiculeMotorise* systematically fails with a [NullPointerException](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/NullPointerException.html) error.

This example illustrates that calling methods in a constructor can lead to complex situations. It is strongly recommended to call methods in a constructor whose behavior cannot be modified by overriding: either methods declared as **private** or methods declared as **final**.

# Hiding Attributes by Inheritance

It is possible to declare an attribute with the same name in a child class as in the parent class. However, this does not correspond to method overriding or the principle of polymorphism. The attribute in the child class simply hides the attribute in the parent class.

If the attribute is **private**, creating an attribute with the same name in a child class has no particular impact. This allows isolating the internal state of the parent class from its child class.

If the attribute is package-private, **protected**, or **public**, then the attribute in the parent class is simply hidden in the child class. If a child class wants to access the attribute in the parent class, it can do so through the **super** keyword.

```java
public class Person {

  protected String name;

  public Person(String name) {
    this.name = name;
  }

  // ...

}
```

```java
public class MaskedHero extends Person {

  private String name;

  public MaskedHero(String name, String secretIdentity) {
    super(name);
    this.name = secretIdentity;
  }

  @Override
  public String toString() {
    // Hmm! Not very clear
    return this.name + " whose secret identity is " + super.name;
  }

  // ...

}
```

Due to the potential for confusion, it is preferable never to create an attribute with the same name as that of a package-private, **protected**, or **public** attribute in any of its parent classes.

# The Open-Closed Principle

The [Open-Closed Principle](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle) states that a class should be designed to be open for extension but closed for modification.

On one hand, if a class inherits from another class, it should be able to add new behaviors with new methods. However, method overriding should not be used to create an implementation that has significantly different behavior from the parent class.

On the other hand, if a class inherits from another class, it should not be able to modify the behavior described by the parent class. In Java, to prevent modifying the behavior of a class, you can declare its attributes as **private**, and the most critical methods can be declared as **final**.
