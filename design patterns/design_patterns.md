# different design patterns:
## 1.  Flyweight design pattern: 

* In Flyweight, object is immutable.
* Flyweight is about saving memory by not creating new objects and reusing existing ones when possible.
* In Flyweight you will not create a new object but an immutable object will be shared accross the clients.

## 2. Decorator design pattern:
* adding new behavior to an existing object dynamically. or decorating with extra functionality.
* Like adding toppings to the Pizza or adding chocolate or hazelnut to coffee.

## 3.  Facade design pattern:
* facade adds a new layer of abstraction to access the complex subsystems. 
* For Example when a computer starts it does boot, BIOS load, read memory etc. but the interface function would be `computer.start()`
## 4. Chain of Resposibility:
* **Java exception handling is another example of chain of responsibility design**. When an error occurs, the exception call will look for a handling class. If there is no handler, the super Exception class will be called to throw the exception.
* Same with filters.
## 5. Iterator:
* this pattern provides an effective way of accessing elements of a collection sequentially, without knowing how the collection is structured.
* the main idea is that an aggregate object such as an array or list will give you a way to access its elements without exposing its internal structure.
