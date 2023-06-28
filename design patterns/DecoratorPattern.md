# Decorator Pattern

Attach additional responsibilities to an object dynamically. Decorators provide a flexible alternative to subclassing for extending functionality.

This pattern says that the class must be closed for modification but open for extension, a new functionality can be added without disturbing existing functionalities. The concept is very useful when we want to add special functionalities to a specific object instead of the whole class. In this pattern, we use the concept of object composition instead of inheritance.



```java
public class CoffeeMaker {
    // if we want to add milk.
    public Coffee makeCoffee(){
        Coffee coffee = new SimpleCoffee();
       // milk twice and sugar thrice:
        for(int i = 0; i < 2; i++){
            coffee = new Milk(coffee);
        }
        for(int i = 0; i < 3; i++){
            coffee = new Sugar(coffee);
        }
        return coffee;
    }
}
```
For this we create a Interface of type Coffee which will have the common functions.

```java
interface Coffee {
    public double getCost();
    public String getIngredients();
}
```
Now we can have different types of coffee like `SimpleCoffee` or `Cappuccino` which has the basic cost of either a `Cappuccino` or `Espresso`

```java
class SimpleCoffee implements Coffee {
    @Override
    public double getCost(){
    return 2;

    @Override
    public String getIngredients(){
    return "Plain Coffee";
    }
}
```
This class is base class for Decorators. It also should be extending the main `coffee` interface.

```java
abstract class CoffeeDecorator implements Coffee {
    protected final Coffee decoratedCoffee;

    public CoffeeDecorator (Coffee coffee) {
        this.decoratedCoffee = coffee;
    }

    public double getCost(){
        return decoratedCoffee.getCost();
    }

    public String getIngredients(){
        return decoratedCoffee.getIngredients();
    }
}
```
By Extending this decorator superclass, we can add different decorators like extraMilk, extraSugar or extraChocolate etc. This can be added to the `SimpleCoffee` class.

```java
class WithMilk extends CoffeeDecorator {
    public WithMilk(Coffee coffee) {
    super (coffee) ;
    }

    public double getCost(){
        return super.getCost() + 0.5;
    }

    public String getIngredients () {   
        return super.getIngredients () + ", Milk";
    }
}

class WithSugar extends CoffeeDecorator {
    public WithSugar (Coffee coffee) {
        super (coffee) ;
    }

    public double getCost(){
        return super.getCost() + 0.5;
    }

    public String getIngredients () {
        return super.getIngredients() + ", Sugar";
    }
}
```

