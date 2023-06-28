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
```java
interface Coffee {
   public double getCost();
   public String getIngredients();
}
```
```java
class SimpleCoffee implements Coffee {
    @Override
    public double getCost(){
    // TODO Auto-generated method stub
    return 2;

    @Override
    public String getIngredients(){
    // TODO Auto-generated method stub
    return "Plain Coffee";
    }
}
```
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

