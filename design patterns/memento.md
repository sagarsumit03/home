# Memento Design Pattern:
* it provides a undo/redo functionality.
* pattern allows restoring the object without exposing internal implementations.
* pattern allows state management of objects.

> Memento real world example would be Undo/Redo in a text editor.

The Memento Design pattern contains below 3 Elements:
Where the **Originator** is the creating and manageing the state of the Object.
**Memento** is the actual snapshot of the object. (state).
**Caretaker** which takes care of restoring and creating snapshots. which in our example is the main class itself and we do not have a separate History class.
![](https://github.com/sagarsumit03/home/blob/master/design%20patterns/memento_class.png)
Example: 

```java 
public class TextEditor {  
  
  private String text;  
  
    public String getText() {  
  return text;  
    }  
  
  public TextEditor(String text) {  
  this.text = text;  
    }  
  
  public void insertText(String textTobeAdded, int insertPosition){  
  text = text.substring(0, insertPosition)+textTobeAdded+text.substring(insertPosition);  
        System.out.println("Inserted text: " + text);  
    }  
  
  public void deleteText(int start, int end){  
  text =  text.replace(text.substring(start, end), "");  
        System.out.println("Deleted text: " + text);  
    }  
  
  public Memento createMemento() {  
  return new Memento(text);  
    }  
  
  public void restoreMemento(Memento memento) {  
  this.text = memento.getText();  
    }  
  
}  
  
class Memento {  
  private String text;  
  
    public String getText() {  
  return text;  
    }  
  
  public Memento(String text) {  
  this.text = text;  
    }  
}
```

In the main class we can use it something like this: 
Where we are creating memento Object and then restoring it later as a undo/redo scenario.

```java
public static void main(String[] args) {  
    TextEditor editor = new TextEditor("sumit is the best");  
    Memento memento = editor.createMemento();  
    editor.insertText(" hi", 4);  
  
    Memento memento1 = editor.createMemento();  
    editor.insertText(" hello", 4);  
  
    Memento memento2 = editor.createMemento();  
    editor.deleteText(2, 4);  
  
    System.out.println("Current document text: " + editor.getText());  
  
    editor.restoreMemento(memento2);  
    System.out.println("Restored document text (before inserting text): " + editor.getText());  
  
    // Restore state to before deleting text  
    editor.restoreMemento(memento1);  
    System.out.println("Restored document text (before deleting text): " + editor.getText());  
  
    editor.restoreMemento(memento);  
    System.out.println("Restored document text (before deleting text): " + editor.getText());  
}
```
