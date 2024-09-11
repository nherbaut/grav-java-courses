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
title: Abstract Classes
---
# Abstract Classes

We have seen that inheritance is a way to share code in a parent class.
Sometimes this class represents an abstraction for which there is really
no sense in creating an instance. In this case, we can consider that
the generalization is *abstract*.

#### NOTE
In contrast, a class that is not abstract is called a *concrete class*.

## Declaring an Abstract Class

If we take our example of the Vehicle class:

```java
package {{ROOT_PKG}}.driving;

public class Vehicle {

  private final String brand;
  protected float speed;

  public Vehicle(String brand) {
    this.brand = brand;
  }

  public void accelerate(float speedChange) {
    this.speed += speedChange;
  }

  public void decelerate(float speedChange) {
    this.speed = Math.max(this.speed - speedChange, 0f);
  }

  // ...

}
```

This class can have several subclasses such as *Car* or *Motorbike*.
Ultimately, the *Vehicle* class allows the emergence of a type through
which it is possible to manipulate instances of *Car* or *Motorbike*.
There is little interest in this context to create an instance of *Vehicle*.
We can easily prevent it, for example, by declaring the constructor with
a **protected** scope. In Java, we also have the option to declare this
class as **abstract**.

```java
package {{ROOT_PKG}}.driving;

public abstract class Vehicle {

  private final String brand;
  protected float speed;

  public Vehicle(String brand) {
    this.brand = brand;
  }

  public void accelerate(float speedChange) {
    this.speed += speedChange;
  }

  public void decelerate(float speedChange) {
    this.speed = Math.max(this.speed - speedChange, 0f);
  }

  // ...

}
```

The **abstract** keyword added in the class declaration now indicates
that this class represents an abstraction for which it is not possible
to directly create an instance of this type.

```java
  Vehicle v = new Vehicle("X"); // COMPILATION ERROR: THE CLASS IS ABSTRACT
```

#### NOTE
In Java, it is not possible to combine **abstract** and **final** in the
declaration of a class because it would make no sense. An abstract class
cannot be instantiated, so there must necessarily be one or more subclass(es).

## Declaring an Abstract Method

An abstract class can declare abstract methods. An abstract method has
a signature but no body. This means that a class inheriting from this
method must redefine it to provide an implementation (unless that class
is itself abstract).

For instance, a vehicle can indicate its number of wheels. Instead of using
an attribute to store the number of wheels, the number of wheels can be
made an abstract property of the class.

```java
package {{ROOT_PKG}}.driving;

public abstract class Vehicle {

  private final String brand;
  protected float speed;

  public Vehicle(String brand) {
    this.brand = brand;
  }

  public abstract int getNbWheels();


  // ...

}
```

All concrete classes inheriting from *Vehicle* must now provide an
implementation of the *getNbWheels* method in order to compile.

```java
package {{ROOT_PKG}}.driving;

public class Car extends Vehicle {

  public Car(String brand) {
    super(brand);
  }

  @Override
  public int getNbWheels() {
    return 4;
  }

  // ...

}
```

```java
package {{ROOT_PKG}}.driving;

public class Motorbike extends Vehicle {

  public Motorbike(String brand) {
    super(brand);
  }

  @Override
  public int getNbWheels() {
    return 2;
  }

  // ...

}
```

An abstract method can serve several purposes. As in the previous example,
it can be used to gain abstraction in our model. But it can also allow a
subclass to adapt the behavior of an algorithm or a software component.

```java
package {{ROOT_PKG}}.spreadsheet;

public abstract class Spreadsheet {

  public void update() {
    drawLinesAndColumns();
    int firstRow = getFirstDisplayedRow();
    int firstColumn = getFirstDisplayedColumn();
    int lastRow = getLastDisplayedRow();
    int lastColumn = getLastDisplayedColumn();

    for (int row = firstRow; row <= lastRow; ++row) {
      for (int column = firstColumn; column <= lastColumn; ++column) {
        String content = getContent(row, column);
        displayContent(row, column, content);
      }
    }
  }

  protected abstract String getContent(int row, int column);

  private void displayContent(int row, int column, String content) {
    // ...
  }

  private int getLastDisplayedColumn() {
    // ...
  }

  private int getLastDisplayedRow() {
    // ...
  }

  private int getFirstDisplayedColumn() {
    // ...
  }

  private int getFirstDisplayedRow() {
    // ...
  }

  private void drawLinesAndColumns() {
    // ...
  }

}
```

In the above example, we imagine a *Spreadsheet* class that allows displaying
a table on the screen based on the visible rows and columns. It is an abstract
class, and classes that specialize this class must provide an implementation
of the abstract method *getContent* to provide the content of each cell
displayed by the spreadsheet.