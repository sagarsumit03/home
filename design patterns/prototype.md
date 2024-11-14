# Prototype Design pattern
* allows you to create copies of existing objects without making your code dependent on their classes.
* It involves creating new objects by copying an existing object.
* Real time example would be the Clone() Api by java.

Example: 
```java
  
public class Prototype implements Cloneable {  
    private NameTag tag;  
    private int size;  
  
  //getters and setters
  
  public Object clone() throws CloneNotSupportedException {  
	  Prototype prototype;  
        try{  
		  prototype = (Prototype) super.clone();  
        }  
  catch (CloneNotSupportedException e) {  
	  throw new RuntimeException("Super class doesn't implement cloneable");  
        }  
  
  //work specific to the MyObj class  
	  prototype.tag = (NameTag) tag.clone();  //We want a deep copy of the NameTag  
	  prototype.size = size;       //ints are primitive, so this is a copy operation  
  
	  return prototype;  
    }  
}

  
public class NameTag implements Cloneable{  
  
    private String name;  
    private String tag;  
  
   //getters and setters
  
  @Override  
  protected Object clone() throws CloneNotSupportedException {  
  return super.clone();  
    }  
}
```
The Main class is: 
```java
public static void main(String[] args) throws CloneNotSupportedException {  
    NameTag tag = new NameTag("sumit", "norsecosmo");  
    Prototype type = new Prototype(tag, 10);  
    Prototype type2 = (Prototype) type.clone();  
    NameTag tag2 = type2.getTag();  
    tag2.setTag("zerodeaths");  
    System.out.println("clone: "+type2);  
    System.out.println(type);  
}
```
