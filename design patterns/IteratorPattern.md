# Iterator:
*Iterator* is a behavioral design pattern that lets you traverse elements of a collection without exposing its underlying representation (list, stack, tree, etc.).

### Problem:
There should be a way to go through each element of the collection without accessing the same elements over and over.

its an easy job if you have a collection based on a list. You just loop over all of the elements. But how do you sequentially traverse elements of a complex data structure, such as a tree? For example, one day you might be just fine with depth-first traversal of a tree. Yet the next day you might require breadth-first traversal.

### Solution:
The main idea of the Iterator pattern is to extract the traversal behavior of a collection into a separate object called an iterator.
In addition to implementing the algorithm itself, an iterator object encapsulates all of the traversal details, such as the current position and how many elements are left till the end. Because of this, several iterators can go through the same collection at the same time, independently of each other.

#### Real World Example:
Google Maps uses graph traversal


##### Example:
`java.util.iterator`, `java.util.splititerator`, `java.util.Scanner`
