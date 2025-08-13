## Hibernate Session States:

### Transient

1. The object is not associated with any Hibernate Session.

2. It doesn’t exist in the database and has no identifier (primary key).

3. Hibernate is unaware of the object.

  Example: new keyword is used to create the object.

```
Product p = new Product();  // Transient
p.setName("Mobile");
```

### Persistent

1. The object is associated with a Hibernate Session.

2. It represents a row in the database.

3. Any changes to the object will be automatically tracked and synchronized with the DB on flush() or commit().

Example: Retrieved from DB or saved using session.save().

```
Session session = sessionFactory.openSession();
session.beginTransaction();

Product p = session.get(Product.class, 1);  // Persistent
p.setName("Updated Name");  // Hibernate tracks this change

session.getTransaction().commit();
session.close();
```

### Detached

1. The object was associated with a session but now the session is closed or object is evicted.

2. It still has a DB identity (primary key), but Hibernate doesn’t track changes.

3. Can be reattached using session.update() or session.merge().

```
session.close();  // Session closed, object becomes Detached
p.setName("Another Update");

Session newSession = sessionFactory.openSession();
newSession.beginTransaction();
newSession.update(p);  // Reattached, becomes Persistent again
newSession.getTransaction().commit();
```


### Removed (Deleted)

1. The object is marked for deletion using session.delete().

2. It's still in the session until flush()/commit() is called.

3. After commit, the entity becomes transient (if reference is kept).

```
session.delete(p);  // Marked for deletion, Removed state
session.getTransaction().commit();  // DB row deleted
```


## Get() vs Load():

a. `get():`
1. Performs eager loading.
2. When `get()` is called, Hibernate immediately executes a SQL SELECT query to
fetch the object from the database.  
3. If no object with the given identifier is found in the database or session cache, `get()` returns `null`  

b. `load():`
1. Performs lazy loading.
2. When `load()` is called, Hibernate does not immediately 
3. fetch the object from the database. Instead, it returns a proxy object. The actual database query is executed 
only when a method (other than `getId()`) is called on the `proxy object`, requiring access to its properties.  
If no object with the given identifier is found, `load()` throws an `ObjectNotFoundException`

---
| Annotation   | Default Fetch Type |                 Explaination             |
| ------------ | ------------------ |------------------------------
| `@OneToMany` | **LAZY**           |If it were **EAGER**, fetching one parent could trigger fetching hundreds or thousands of children even if you don’t need them. **LAZY** ensures the collection is fetched only when accessed. |
| `@ManyToOne` | **EAGER**          |Fetching one extra row is cheap compared to fetching an entire collection. |


---
## How to check Fetch Lazy and Eager 
Here a Student can have many courses and many courses can have many students.

```java
public class Student {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @ManyToMany(fetch = FetchType.LAZY ,cascade = {CascadeType.MERGE})
    @JoinTable(
            name = "student_course",
            joinColumns = @JoinColumn(name = "student_id"),
            inverseJoinColumns = @JoinColumn(name = "course_id")
    )
    @JsonManagedReference
    private List<Course> courses = new ArrayList<>();
}

```

```java
public class Course {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;

    /**
     * What you’re seeing is the infinite nested JSON loop problem — a classic with
     * @ManyToMany in JPA + Jackson.
     * <p>
     * It happens because:
     * <p>
     *   - Student has a List<Course> courses
     *   - Course has a List<Student> students
     *   - Jackson tries to serialize a Student, sees Course → inside Course it sees
     *     students → inside students it sees courses → and so on... forever.
     */

    @JsonBackReference
    @ManyToMany(mappedBy = "courses")
    private List<Student> students = new ArrayList<>();
}
```

to Test this: 
If you see in the query you will only see Student query. but as soon as you do getCourses() you will get the secondary ones.

```java
System.out.println("---- FETCH ORDER ----");
Student student = studentRepo.findById(1L).orElse(new Student()); // SQL will fetch Order + Customer (EAGER)
// // 1 query for student

System.out.println("---- ACCESS ITEMS ----");
int size =  student.getCourses().size(); // SQL for items will run **now** (LAZY) // 1 query for items
```

As we are fetching only 1 student and N courses its not N+1 but as soon as you fetch N student using above query and 
then to loop on Students and fetch courses it becomes N+1 problem

```java
Order order = em.findAll();  // 1 query for order
for(Order order: OrderList){
  order.getItems().size();
}
```
## THIS BECOMES N+1 PROBLEM. TO AVOID THIS USE `@BatchSize(size = 10)`
