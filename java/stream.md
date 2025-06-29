# 💡 Java Stream API Interview Problems with Examples

---

## ✅ Problem 1: Find the Sum of All Numbers

**Given:** A list of integers
**Task:** Find the sum of all the numbers in the list

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
int sum = numbers.stream()
    .mapToInt(y -> (int)y)
    .sum();
System.out.println(sum); // Output: 15
```

---

## ✅ Problem 2: Find All Duplicate Elements in a List

**Given:** A list of integers with possible duplicates
**Task:** Identify all duplicate elements

### 🔹 Approach 1: Using `Collectors.groupingBy()`

```java
List<Integer> number = Arrays.asList(1, 2, 3, 4, 2, 5, 6, 1, 7, 8, 3);
List<Integer> duplicates = number.stream()
    .collect(Collectors.groupingBy(e -> e, LinkedHashMap::new, Collectors.counting()))
    .entrySet().stream()
    .filter(e -> e.getValue() > 1)
    .map(Map.Entry::getKey)
    .toList();

System.out.println(duplicates); // Output: [1, 2, 3]
```

### 🔹 Approach 2: Using a `Set`

```java
HashSet<Integer> set = new LinkedHashSet<>();
List<Integer> duplicates = number.stream()
    .filter(e -> !set.add(e))
    .collect(Collectors.toList());

System.out.println(duplicates); // Output: [2, 1, 3]
```

---

## ✅ Problem 3: Group Employees by Department

**Given:** A list of `Employee` records
**Task:** Group the employees by department

```java
record Employee(String name, String department) {}

List<Employee> employees = Arrays.asList(
    new Employee("sum", "e"),
    new Employee("k", "e"),
    new Employee("j", "m"),
    new Employee("7", "m")
);

Map<String, List<Employee>> grouped = employees.stream()
    .collect(Collectors.groupingBy(Employee::department));

System.out.println(grouped);
// Output: {e=[Employee[name=sum, department=e], Employee[name=k, department=e]], m=[Employee[name=j, department=m], Employee[name=7, department=m]]}
```

---

## ✅ Problem 4: Find the Lowest Salary

**Given:** A list of `Emp` records with salaries
**Task:** Find the lowest salary among all employees

```java
record Emp(String name, Integer salary, String dept) {}

List<Emp> empl = Arrays.asList(
    new Emp("sum", 120, "e"),
    new Emp("k", 200, "e"),
    new Emp("j", 5500, "m"),
    new Emp("7", 1200, "m")
);

int lowestSalary = empl.stream()
    .min(Comparator.comparingInt(Emp::salary))
    .get()
    .salary();

System.out.println(lowestSalary); // Output: 120
```

---

## ✅ Problem 5: Find Highest Salary per Department

**Given:** Same list of employees
**Task:** For each department, find the employee with the highest salary

```java
Map<String, Emp> maxSalaryByDept = empl.stream()
    .collect(Collectors.groupingBy(
        Emp::dept,
        Collectors.collectingAndThen(
            Collectors.maxBy(Comparator.comparingInt(Emp::salary)),
            Optional::get
        )
    ));

System.out.println(maxSalaryByDept);
// Output: {e=Emp[name=k, salary=200, dept=e], m=Emp[name=j, salary=5500, dept=m]}
```

---

## ✅ Problem 6: Sort a Map by Value in Descending Order

**Given:** A map with key-value pairs
**Task:** Sort the map by value in descending order

```java
Map<String, Integer> m = Map.of("a", 3, "b", 1, "c", 2);

Map<String, Integer> sorted = m.entrySet().stream()
    .sorted(Map.Entry.<String, Integer>comparingByValue().reversed())
    .collect(Collectors.toMap(
        Map.Entry::getKey,
        Map.Entry::getValue,
        (e1, e2) -> e1,
        LinkedHashMap::new
    ));

System.out.println(sorted); // Output: {a=3, c=2, b=1}
```

---
