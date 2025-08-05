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

