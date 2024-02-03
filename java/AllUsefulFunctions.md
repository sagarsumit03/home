# All Important Questions:

## Immutable Class:

```
final class Dept {
    // the class should be final to not be extended
    // All variables should be private
    // all setters should be private or not present
    // constructor should be initializing variables with deep copy

    private String name;
    private String id;

    public String getName() {
        return name;
    }

    public String getId() {
        return id;
    }

    public Dept(String name, String id) {
        this.name = name;
        this.id = id;
    }

}
```

## Final class vs Final keyword:

```
A final class is simply a class that can't be extended.

final keyword before variablle declaration:
-- The object cannot be changed.
final String s = "Hello";
// Not allowed.
s = "Bye";

Immutable
The contents of the object cannot be changed.

```
