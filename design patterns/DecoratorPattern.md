# Decorator Pattern

Attach additional responsibilities to an object dynamically at runtime.

# Strategy vs Decorator Pattern:

The main difference between strategy and decorator is :
1. if we are writing a sorting function. based on different parameters we can choose the sorting strategy. Thats `Strategy Pattern` 

    ```java
    java.util.Comparator#compare() uses differnt strategy to sort the collection, 

    // The sorting method is the Context. The Comparator is the Strategy.
    List<String> names = Arrays.asList("John", "Anna", "Mike");

    // Strategy 1: Sort alphabetically
    names.sort((a, b) -> a.compareTo(b)); 

    // Strategy 2: Sort by length
    names.sort((a, b) -> Integer.compare(a.length(), b.length())); 


    ```
2. If on a collection, you are making them immutable or create wrapper around different streams then its decorator.

    ```java
    // FileInputStream is the core component providing raw text reading
    FileInputStream fis = new FileInputStream("file.txt"); 

    // BufferedInputStream decorates it to add memory buffering performance
    BufferedInputStream bis = new BufferedInputStream(fis); 

    // DataInputStream decorates it further to allow reading primitive Java data types
    DataInputStream dis = new DataInputStream(bis);



    OR,

    Collections.unmodifiableList or Collections.synchronizedList etc. They are wrapper on top of the core list.
    ```


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

