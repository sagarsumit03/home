# Singleton:
Singleton is a creational design pattern that lets you ensure that a class has only one instance, while providing a global access point to this instance.

###### EXAMPLE:
`java.lang.Runtime#getRuntime()`

1. imagine that you created an object, but after a while decided to create a new one. Instead of receiving a fresh object, you’ll get the one you already created.

    Note that this behavior is impossible to implement with a regular constructor       since a constructor call must always return a new object by design.


2. Just like a global variable, the Singleton pattern lets you access some object from anywhere in the program. However, it also protects that instance from being overwritten by other code.

### Solution:
All implementations of the Singleton have these two steps in common:

* Make the default constructor private, to prevent other objects from using the new operator with the Singleton class.
* Create a static creation method that acts as a constructor. Under the hood, this method calls the private constructor to create an object and saves it in a static field. All following calls to this method return the cached object.

