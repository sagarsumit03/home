# Factory Pattern:

#### EXAMPLE:
we create a Spring application that handles document printing. For a given document, it is able to print it in either A4 or A5 format or either portrait and landscape layout. Each of these printing strategies extends a common interface:
```
public interface IPrintStrategy {
    public void print(Document document);
}

```

down here we have 4 implementation for the printing strategy, i.e A4 Portrait, A5 Portrait, A4 Landscape, A5 Landscape.

```
@Component("A4Landscape")
public class PrintA4LandscapeStrategy implements IPrintStrategy{
    @Override
    public void print(Document document) {
        System.out.println("Doing stuff to print an A4 landscape document");
    }
}
```



```
@Component("A5Landscape")
public class PrintA5LandscapeStrategy implements IPrintStrategy{
    @Override
    public void print(Document document) {
        System.out.println("Doing stuff to print an A5 landscape document");
     }
}
```


```
@Component("A4Portrait")
public class PrintA4PortraitStrategy implements IPrintStrategy{
    @Override
    public void print(Document document) {
        System.out.println("Doing stuff to print an A4 portrait document");
    }
}
```


```
@Component("A5Portrait")
public class PrintA5PortraitStrategy implements IPrintStrategy{
    @Override
    public void print(Document document) {
        System.out.println("Doing stuff to print an A5 portrait document");
     }
}
```


Now, we'll have to define an interface that will be implemented by the factory class through which we will retrieve printing strategies:

```
public interface PrintStrategyFactory {
    IPrintStrategy getStrategy(String strategyName);
}
```
this factory class will get a strategy name as input and will return an instance of IPrintStrategy.


In the main method we can use this:

```
printStrategyFactory.getStrategy("A5Portrait").print(doc);
printStrategyFactory.getStrategy("A5Portrait").print(doc);
```

---
### plain java Example:

Step 1
Create an interface:

```
public interface Shape {
   void draw();
}
```
Step 2
Create concrete classes implementing the same interface:
```
public class Rectangle implements Shape {

   @Override
   public void draw() {
      System.out.println("Inside Rectangle::draw() method.");
   }
}
```
```
public class Square implements Shape {

   @Override
   public void draw() {
      System.out.println("Inside Square::draw() method.");
   }
}
```
```
public class Circle implements Shape {

   @Override
   public void draw() {
      System.out.println("Inside Circle::draw() method.");
   }
}
```
Step 3
Create a Factory to generate object of concrete class based on given information:
```
public class ShapeFactory {
   //use getShape method to get object of type shape 
   public Shape getShape(String shapeType){
      if(shapeType == null){
         return null;
      }		
      if(shapeType.equalsIgnoreCase("CIRCLE")){
         return new Circle();
         
      } else if(shapeType.equalsIgnoreCase("RECTANGLE")){
         return new Rectangle();
         
      } else if(shapeType.equalsIgnoreCase("SQUARE")){
         return new Square();
      }
      
      return null;
   }
}
```
Step 4
Use the Factory to get object of concrete class by passing an information such as type:
```
public class FactoryPatternDemo {

   public static void main(String[] args) {
      ShapeFactory shapeFactory = new ShapeFactory();

      //get an object of Circle and call its draw method.
      Shape shape1 = shapeFactory.getShape("CIRCLE");

      //call draw method of Circle
      shape1.draw();

      //get an object of Rectangle and call its draw method.
      Shape shape2 = shapeFactory.getShape("RECTANGLE");

      //call draw method of Rectangle
      shape2.draw();

      //get an object of Square and call its draw method.
      Shape shape3 = shapeFactory.getShape("SQUARE");

      //call draw method of square
      shape3.draw();
   }
}
```
