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

### Code Example:

    public class Singleton {

        private static Singleton instance;

        private Singleton(){}  //private constructor.

        public static Singleton getInstance(){
            if (instance == null){ //if there is no instance available... create new one
                instance = new SingletonClass();
            }
            return instance;
        }
    }

1. The problem here is that the Singleton can still be broken by reflection, cloning or Serializing.
    ```
    public class SingletonMain {
       public static void main(String[] args) {
            //Create the 1st instance
            Singleton instance1 = Singleton.getInstance();

            //Create 2nd instance using Java Reflection API.
            Singleton instance2 = null;
            try {
                Class<Singleton> singletonClass = Singleton.class;
                Constructor<Singleton> cons = singletonClass.getDeclaredConstructor();
                cons.setAccessible(true);
                instance2 = cons.newInstance();
            } catch (NoSuchMethodException | InvocationTargetException | IllegalAccessException | InstantiationException e) {
                e.printStackTrace();
            }

            //now lets check the hash key.
            System.out.println("Instance 1 hash:" + instance1.hashCode());
            System.out.println("Instance 2 hash:" + instance2.hashCode());
       }
    }
    ```
Here we created a new instance of singleton class by using reflection, but that shouldn't be allowed. Through reflection,
we are able to access private constructor.
Solution:
We can throw execption, if the constructor is called in any way:
    
    public class Singleton {

        private static Singleton instance;

        //private constructor.
        private Singleton(){
            //Prevent form the reflection api.
            if (instance != null){
                throw new RuntimeException("Use getInstance() method to get the single instance of this class.");
            }
        } 

        public static Singleton getInstance(){
            if (instance == null){ //if there is no instance available... create new one
                instance = new Singleton();
            }
            return instance;
        }
    }
    
    
    Since enums are inherently serializable, we don't need to implement it with a serializable interface. The reflection problem is also not there. Therefore, it is 100% guaranteed that only one instance of the singleton is present within a JVM. Thus, this method is recommended as the best method of making singletons in Java.
    
    
2. To fix Singleton pattern for Serialization, we should just use additional method readResolve(). This will return 
    the same instance as before.
    
    
        protected Object readResolve() {
            return getInstance();
        }
    
    
3. To Fix new Instance getting created using Cloning, we can use the clone() method, to throw the `CloneNotSupportedException`
    ```
    @Override
    protected Object clone() throws CloneNotSupportedException  
    { 
        throw new CloneNotSupportedException(); 
    } 
    
    ```
