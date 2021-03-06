---
author: Usama
date: 2019-01-02
description: Braindump for OCAJP, Oracle Certified Associate, Java SE 8 Programmer I
excerpt_separator: <!--more-->
url: /java-oca-braindump
aliases:
  - /blog/2019/01/02/java-oca
title: Java notes
draft: true
---


I wrote these while preparing for OCAJP exam. Most of it is in form of code with output right next to it as a comment. 
<!--more-->

## Chapter 1 and 2

### Classes

One java file can have multiple classes, maximum *only one* class can be public.

```java
// Three.java
class One {}
class Two {}
public class Three {} // only one public class, same name as file
class Four {}
```
also
```java
// Numbers.java
// No public class
class One {}
class Two {}
class Three {}
class Four {}
```

Java file must be same name and same case as the public class in that file.

Compile `javac myclass.java` run `java myclass`.

Signature of main method is
 - `public static void main(String[] args)`
 - `public static void main(String args[])`
 - `public static void main(String... args)`

Arguments start from index 0. On `java myclass test` first argument `args[0]` contains `test` and `args[1]` gives `java.lang.ArrayIndexOutOfBoundsException`.


### Packages

Packages solve naming conflicts, e.g. have same class name in different packages. i.e. `java.util.Data` and `java.sql.Date`

classes and interfaces without package are part of *default package*. *default package* does not have a name and automatically imported in packageless classes defined in same directory. Classes from default package cannot be used in a named package even if they are in the same directory.

```java
// file a.java
class Foo {
}

// file b.java
class Bar {
    Foo f; // Foo can be accessed here
}
```

`import java.lang.*` is done automatically without explicitly writing it. Therefore importing `java.lang.*` explicitly is considered redundant.

Explicit import of same class from different packages gives compiler error.
`import java.util.Date; import java.sql.Date;` error: `The import java.sql.Date collides with another import statement`

Import of two packages containing same class gives ambiguous type error
`import java.util.*; import java.sql.*;` error `The type Date is ambiguous`


Some packages to remember.

- java.lang.System
- java.util.ArrayList



### Initialization

Fields and instance initializer blocks run in the order they appear in a file.

Static initializer >>> (`new instance()`) >>> Instance Initializer >>> Constructor

```java
public class Chick {
    {//8.
        System.out.println("6: instance init block 1 ");
        name = "foo1"; // forward reference is write only

        // System.out.println("setting field " + name); // compile error, illegal forward reference 'name'
    }

    private String name = "Fluffy"; //9.

    {//10.
        System.out.println("7: instance init block 2");
        System.out.println("8: setting field " + name);
        name = "foo2";
    }

    static {//1.
        System.out.println("1: static block 1");
        // System.out.println("static block 1" + age); // illegal forward reference 'age'
        age = 10;
        color = "black";
    }

    static String color; //2.
    static int age = 20; //3.

    static { //4.
        System.out.println("2: static block 2: " + age); // age = 20
        age = 10;
        color = "red";
    }

    public Chick() {//8.
        name = "Tiny";
        System.out.println("9: setting constructor");
    }

    public static void main(String[] args) {
        System.out.println("4: in main"); //6.
        Chick chick;
        System.out.println("5: creating instance");
        chick = new Chick(); //7.
        System.out.println(chick.name); //11.
    }

    static { //5.
        System.out.println("3: static block 3: " + age + color);
    }
}
```

**Local variables** must be initialized explicitly.
**Class variables** are automatically given default values when declared.

Local variables hide class variables.

### Types

primitive types

| type      | wrapper   | size                  | default  | start          | end           | comments                             |
| --------- | --------- | --------------------- | -------- | -------------- | ------------- | ------------------------------------ |
| `boolean` | Boolean   |                       | false    | true           | false         |                                      |
| `byte`    | Byte      | 8-bit integral        | 0        | -128           | 127           |                                      |
| `short`   | Short     | 16-bit integral       | 0        | -32,768        | 32,767        |                                      |
| `char`    | Character | 16-bit integral       | '\u0000' | 0              | 65,535        |                                      |
| `int`     | Integer   | 32-bit integral       | 0        | -2,147,483,648 | 2,147,483,647 | Integer.MIN_VALUE, Integer.MAX_VALUE |
| `long`    | Long      | 64-bit integral       | 0        |                |               | 0L                                   |
| `float`   | Float     | 32-bit floating-point | 0.0f     |                |               | requires `f` at the end.             |
| `double`  | Double    | 64-bit floating-point | 0.0      |                |               | 0.0D                                 |

A constant value representing data of a specific type in code is called a `literal`.

| literal                           | type              |
| --------------------------------- | ----------------- |
| 'a', '0'                          | `char`            |
| "baba"                            | `String`          |
| 9793, 1001, -0                    | `int`             |
| 297.45, 0.15d                     | `double`          |
| 493.252f, 370F                    | `float`           |
| 66930L, 55l                       | `long`            |
| 0b1001L                           | `long`            |
| 010 == 8, 07 == 7, 01 == 1        | `int` octal       |
| 0XA == 10, 0x1F == 31             | `int` hexadecimal |
| 0b10 == 2                         | `int` binary      |
| 0b10_1, 0x1_F, 3_3.5, 123_456___7 | all valid numbers |

Primitive types are **non null-able** i.e. can not be assigned `null`.

`-0` is a valid literal.

#### Numeric Promotion

When applying operators, smaller type is promoted to larger data type. e.g. `5 + 1.5 == 5.5`

`byte`, `short` and `char` are always promoted to `int` in a binary operation.

```java
short x = 10;
short y = 3;
short z = x * y; // error, lossy conversion int to short
short z = (short)x * (short)y; // error, lossy conversion int to short
short z = (short)(x * y); // CORRECT
```

Here it looks like the result should be a floating point number, but because all terms are integer, the result is also an integer.

```java
int c = 5/10; // c = 0 // result will be an integer, not a float or double
double c = 5/10; // c = 0, NOT 0.5
```

Small primitives can assigned to large primitives

```java
int c = 'a';
long l1 = 5;
short s = (byte)25;
```

Larger datatype CANNOT be assigned to smaller datatype without casting

```java
long x = 10;
int y = 5;
y = y * x; // DOES NOT COMPILE, lossy conversion long to int

1L == 1.0f // true
1 == 1L // true
```

Smaller primitive datatype CANNOT be directly assigned to a bigger Boxed type

```java
long l1 = 10;
long l2 = 20l;
Long l3 = 10; // Compile error, int cannot be converted to Long

// similarly
Long l4 = 0b100; // Compile error, int cannot be converted to Long
Integer i1 = 0b1;
Integer i2 = 'a'; // Compile error, char cannot be converted to Integer
Integer i3 = (Character)'c';
```

Smaller boxed types CANNOT be assigned to bigger boxed types

```java
// <Bigger_Boxed_type> x = <Smaller_Boxed_Type>; // error

Long l1 = (Integer)(10); // compiler error
```

But, smaller boxed type can be assigned to bigger primitive type

```java
Short s = 1;
long l1 = s;
long l2 = (Byte)(byte)2;
```

#### Convert from string to primitive or Wrapper class

```java
// parse returns primitive
// valueOf returns wrapper

int num = parseInt("23"); 
Integer num = valueOf("23");
```


#### Identifier Naming Rules

```java
int $$$, ___, _a, x, $b, _1123;      // all valid
String 5xyz, 0_, -a, *_;             // invalid, can not start from number
long int, class, try, const, goto;   // invalid, reserved words
```



```java
public class Outer {
    protected int a;

    public class Inner {
        protected int a;

        public int foo(){
            return Outer.this.a;
        }
        public Outer getOuter(){
            return Outer.this;
        }
    }
}
```



### Garbage Collection

`System.gc()` hints JVM to run garbage collection. An object is collected only when it has no references left. An object becomes eligible for collection as soon as all reference to it are lost.

`void finalize()` this method, when overridden in a class, runs when an object is garbage collected. It may NEVER run i.e. program exits before garbage collection.



### Operators

precedence,

top to down then left to right

| operator               | symbol                                                         | types                     |
| ---------------------- | -------------------------------------------------------------- | ------------------------- |
| post unary, Arithmetic | x++, x--                                                       | primitive, except boolean |
| pre unary, Arithmetic  | ++x, --x                                                       | primitive, except boolean |
| unary                  | `+` `-` `!`                                                    |                           |
| Arithmetic             | `*` `/` `%`                                                    | primitive, except boolean |
| Arithmetic             | `+` `-`                                                        | primitive, except boolean |
| shift                  | `<<` `>>` `>>>`                                                |                           |
| comparison             | `<` `>` `<=` `>=` `intanceof`                                  |                           |
|                        | `==` `!=`                                                      |                           |
| Logical                | `&` `^` `|`                                                    |                           |
| Short-circuit logical  | `&&` `||`                                                      |                           |
| Ternary                | expression ? expression1 : expression2                         |                           |
| Assignment             | `=` `+=` `-=` `*=` `/=` `%=` `&=` `^=` `!=` `<<=` `>>=` `>>>=` |                           |


expressions are evaluated from left to right.

```java
int x = 2;
int y = 0;

// IMPORTANT
// here y is incemented to 1 but returns/evalutes old value
y = y++; // y = 0

y = y++ + y; // y = 0 + 1 --> y = 1

y = x++ * ++x; // 2*4=8
y = ++x * x++; // 3*3=9
y = x++ * x++; // 2*3=6
y = ++x * ++x; // 3*4=12

y = x++*++x;  // 2*4=8
y = x+++x;    // (x++)+x = 2+3
y = ++x-+x;   // (++x)-(+x) = 3-3=0
y = x+++x++;  // (x++)+(x++) = 2+3
y = ++x+++x;  // (++(x++))+x = (++(2))+x => error
```


`if(<boolean expression>)` it only accepts expressions which evaluate to boolean.

`<boolean>?<expression value>:<expression value>` ternary

XOR => X^Y => True when only one of them is true, not both. Red pill OR Blue pill.



Switch

Java \< 5 = `switch(<byte, short, char, int>)`

Java 5 = `switch(<byte, short, char, int, enum>)`

Java 7 = `switch(<byte, short, char, int, enum, String, Byte, Short, Char, Integer>)`

Switch only supports [`byte` --> `Integer`] type variables, `String` and `Enum`.

`long`, `float`, `double` and `boolean` are NOT supported.

```java
switch(<enum, String, byte, short, char, int, Byte, Short, Char, Integer>)
{
    default: // only if none of the cases match
        break;
    case <compile time constant, literal, enum, final; assignable to switch variable without a cast>:
        break;
}
```

```java
final int x = 20; // compile time constant
final int y = getInt(); // NOT a compile time constant
final int z; z = 30; // NOT a compile time constant

int a = 5;
switch(a) {
    case x: //
    case y: // compile error, not a constant
    case z: // compile error, not a constant
}
```

Empty switch, or switch with only `default` case or any case is valid

```java
int x = 5
switch(x) {
}
switch(x) {
    default:
}
switch(x) {
    case 5:
}
```

#### Equality Operator

If numeric values are of different types, values are automatically promoted.

`5 == 5.00 // true`



### Loops

for

```java
for(int x=0, int y=0; x == y; x++) { // does not compile, invalid syntax, type can only be defined once
}
```

for-each

```java
String[] names = new String[4];
for(String name: names){
    System.out.print(name + " "); // prints null 4 times
}
```



#### Labels in loops

- `break label;`
- `continue label;`

`break` breaks the execution of given loop right there.
`continue` immediately starts the next iteration of that loop.

```java
LOOP_A: for(int i=0; i<10; i++) {
    System.out.println("loop a start");
    LOOP_B: for(int j=0; j<10; j++) {
        System.out.println("loop b start");
        LOOP_C: while(j>5) {
            System.out.println("loop c start");
            // break == break LOOP_C;
            break LOOP_B; // breaks loop b and goes to line 13
        }
        System.out.println("loop b end");
        continue LOOP_A; // continues execution of loop a, goes to line 1
    }
    System.out.println("loop a end"); // line: 13
}

// labels can also be applied to following constructs
label1: do{
    continue label1;
    break label1;
} while(true);

label2: if(true){
    // NO break,
    // NO continue
}

label3: switch(x){
    break label3;
    // NO continue
}

```

## Chapter 3

### Strings

```java
String s = 0; // error
String s = "1";
String s = "1" + 2; // 12
String s = 0 + "1"; // 01
String s = 1 + 2 + "3" + 4 + 5;   // 3345
String s = 1 + 2 + "3" + (4 + 5); // 339
```

Print string

```java
System.out.println('a'); // a
System.out.println('a' + 'b'); // 195 // because characters will be promoted to int with + operator
```

Strings are **immutable**. There is a *string pool* in JVM also known as *intern pool*.

```java
"name" // literal, goes in string pool
myobj.toString(); // not a literal, does not go in string pool, garbage collected
```

String functions

```java
String s = "animals";
        //  0123456

s.length(); // 7

s.charAt(1); // n
s.chartAt(7); // exception

s.indexOf('a'); // 0
s.indexOf('a', 1); // 4, start looking from index 1
s.indexOf('x'); // -1
s.indexOf("al"); // 4

s.substring(3); // "mals"
s.substring(string.indexOf('m')); // "mals
s.substring(inclusive, exclusive);
s.substring(3, 4); // "m"
s.substring(3, 7); // "mals"
s.substring(4, 4); // empty
s.substring(4, 3); // exception
s.substring(4, 8); // exception

s.startsWith("a"); // true
s.endsWith("S"); // false

s.contains("l"); // true

s.replace("a", "A"); // AnimAls
s.replace('a','A'); // AnimAls

"\t a b c\n".trim(); // "a b c"
```



#### StringBuilder

```java
StringBuilder alpha = new StringBuilder();
for(char current = 'a'; current <= 'z'; current++){
	alpha.append(current);
}
System.out.println(alpha);

// three ways to create StringBuilder
StringBuilder sb1 = new StringBuilder(); // default capacity 16
StringBuilder sb2 = new StringBuilder("animals"); // string, capcity == str.length() + 16
StringBuilder sb3 = new StringBuilder(10); // init with given capacity 10

// ***** length vs capacity ******//

StringBuilder sb = new StringBuilder();
sb.length();   // 0
sb.capacity(); // 16

// capacity is recalculated if appended string makes length more than capacity
// newCap = oldCap*2+2 >= length ? oldCap*2+2 : length
StringBuilder sb = new StringBuilder(1);
sb.length();    // 0
sb.capacity();  // 1

sb.append("1");
sb.length();    // 1
sb.capacity();  // 1

sb.append("2");
sb.length();    // 2
sb.capacity();  // 4 = 1*2 + 2

// if three chars are added, new cap will become 10 to fit them
// but 9 more chars will overflow capacity therefore cap will be same as length
sb.append("345678901"); // new length is even more than new capacity
sb.length();    // 11
sb.capacity();  // 11 = (10 >= 11 ? 10 : 11)

// insert(offset, ...), insert before given index
StringBuilder sb = new StringBuilder("animals");
sb.insert(7, "-"); // sb = animals-
sb.insert(0, "-"); // sb = -animals-
sb.insert(9, "-"); // sb = -animals--

//
StringBuilder sb = new StringBuilder("animals");
sb.delete(inclusive, exclusive);
sb.delete(1, 3);    // sb = amals
sb.deleteCharAt(3); // sb = amas
sb.deleteCharAt(5); // throws an exception
//
sb.reverse(); // sb = sama
```



### Arrays

```java
int[] arr;

int[] arr = new int[3];
int arr[] = new int[3];

int arr[] = new int[] {1, 2, 3};
int arr[] = {1, 2, 3};
int arr[] = new int[3] {1, 2, 3}; // compile err

int arr1[], num;
int[] arr1, arr2;

int []arr[];
int arr[][];
int[][] arr;
int[][] arr1, arr2;   // both 2d arrays
int arr1[], arr2[][]; // 1d and a 2d array
int[] arr1, arr2[]; // 1d and a 2d array
int[][] differentSize = { {1, 4}, {3}, {9,8,7} };

int[][] = { {} }; // compile error
int[][] = { {1, 3}, {} }; // compile error
int[] = {}; // compile error

int arr[][] = new int[3][];
int arr[][] = new int[][3]; // compile err
int arr[][] = new int[][]; // compile err

// sorting
int[] nums = {5, 1, 8, 2};
int[] sorted = Arrays.sort(nums);
// strings sorting
// String sorts in alphabetic order, and 1 sorts before 9. (Numbers sort before
// letters and uppercase sorts before lowercase)
String sorted_strings[] = {"05", "1", "10", "2", "B", "C", "a", "b"}; // 0112BCab

// searching, works correctly only on sorted arrays
int numbers[] = {1,2,5,7,9};
Arrays.binarySearch(numbers, 2); // 1
Arrays.binarySearch(numbers, 6); // -4 = (3*-1 - 1)
```



#### ArrayList

```java
ArrayList list1 = new ArrayList();
ArrayList list2 = new ArrayList(10 /*init size*/);
ArrayList list3 = new ArrayList(list2);
ArrayList<String> list4 = new ArrayList<String>();
ArrayList<String> list5 = new ArrayList<>();
List<String> list6 = new ArrayList<>();
ArrayList<String> list7 = new List<>(); // DOES NOT COMPILE

// it DOES NOT store primitives
ArrayList<int> li = new ArrayList<>(); // compile error

// also
ArrayList l = new ArrayList();
l.add(1); // valid


// Add
list.add(index, "item");
list.remove("item"); // true
list.remove("item"); // false

list.set(index, "item");
list.contains("item");
list.clear();
list.isEmpty();
list.size();

ArrayList copy = list.clone(); // shallow copy, points to original elements instead of copying, new elemnts will be added in its own memory

// Convert list to array
List<String> ls = new ArrayList<>();
ls.add("i1");
ls.add("i2");
Object[] arr = ls.toArray();

//
String[] targetArr = new String[2];
String[] strConverted = ls.toArray(targetArr); // if target array size can fit source list, it will be filled with items from list, otherwise new array will be created
System.out.println(strConverted == targetArr); // true

// Convert Array to List
// list is backed by the string array, change in one reflects in both, change in size is not allowed
String[] array = { "i1", "i2" };
List<String> ls = Arrays.asList(array);
ls.set(0, "test");
System.out.println(array[0]); // "test"
array[0] = "what";
System.out.println(ls.get(0)); // "what"
list.add("new"); // unsupported operation exception

// sorting
Collections.sort(list);
```



### length, length() and size()

`length` -- __arrays__ (`int[]`, `double[]`, `String[]`) -- immutable, to know the length of the arrays

`length()` -- __String related Object__ (`String`, `StringBuilder`, etc) -- to know the length of the String

`size()` --  __Collection Object__ (`ArrayList`, `Set`, etc) -- to know the size of the Collection

Now forget about `length()` consider just `length` and `size()`.

`length` is not a method, so it completely makes sense that it will not work on objects. It only works on arrays.
`size()` its name describes it better and as it is a method, it will be used in the case of those objects who work with collection (collection frameworks) as I said up there.

Now come to **`length()`**:
String is not a primitive array (so we can't use `.length`) and also not a Collection (so we cant use `.size()`) that's why we also need a different one which is `length()` (keep the differences and serve the purpose).



### Date Time

```java
LocalDate; // date only
LocalTime; // time only
LocalDateTime; // date and time

.now(); // current date/time

LocalDate d = LocalDate.of(2018, Month.December, 14);
LocalTime t = LocalTime.of(5, 30, 12/*optional*/, 300/*optional, nanos*/);
LocalDateTime dt = LocalDateTime.of(Y, M, D, h, m, s/*optional*/, ns/*optional*/);
```

.

.

.


## Chapter 4

### Methods

```java
// naming, same as variables
public void mth0_$();
public void $();
public void _();
public void 4(); // illegal

// optional exception
public void method () throws IllegalArgumentException, InterruptedException {}

// varargs
public void walk(int x, int... nums) { System.out.println(nums[0]); }
walk(1, null); // will throw exception because arg array is null
walk(2);
walk(3, new int[]{5, 7});
```

### Access Modifiers

```java
private < (default) < protected < public
```

`private` Only accessible in same class

default access: Accessible to classes within same package only, C# => internal

`protected` accessible within same package with reference (with dot `.`) and in child classes only, in C# accessible only in child classes. C# => protected+internal

`public` accessible everywhere

```java
package one;
class Bird {
    protected void fly(){}
}

package two;
class Swan extends one.Bird {
    public void flap(){
        fly();
    }
    public void testFlight(){
        Bird b = new Bird();
        b.fly(); // compile error
    }
}
```

protected in C# is only accessible through child classes.

### Static

static method can be called from a `null` instance, C# does not allow accessing static members from instance

```java
class MyObj {
    public static int count = 1;
}
MyObj obj = null;
System.out.println(obj.count); // prints 1
```

`static final` must be only given value in definition or only initialized in a static block

```java
static final int a;
static final int b = 2;
static final int c; // compile error
static {
    a = 1;
    b = 2; // compile error
}
```

### Static import

Static methods and fields can be imported using `import static`, NOT `static import`. can only import static methods, not classes

```java
import static java.util.Arrays.asList;

public class testStaticImport {
    public static void main(String... args){
        List<Integer> list1 = asList(1, 2, 3, 4, 5);
        List<Integer> list2 = Arrays.asList(1, 2, 3, 4, 5); // compile error
        // reason: missing import java.util.Arrays
    }
}
```

### Function overload

```java
public void run(int distance);
public boolean run(int distance); // compile error, can not overload on differnt return type only
public void run(double distance);
public void run(int distance, double from);
public boolean run(double from, int distance);
```

Most specific method is picked first

```java
public void show(String str) { System.out.println("String"); }
public void show(Object obj) { System.out.println("Object"); }
show(""); // will print "String"
show(0);  // will print "Object"

// order of calling for int literal
int       > long             > Integer   > int...
primitive > larger primitive > autoboxed > vararg
```

### Order of Initialization

1. Initialize super class first
2. Static variables and static initializers, in the order they appear
3. Instance variables and instance initializers, in the order the appear
4. Constructor

```java
public class InitOrder {
    private String name = "dog";
    private static int c = 0;
    
    { System.out.println(name); }
    static { System.out.println(c); }
    static { c += 10; System.out.println(c); }
    
    public InitOrder() {
        System.out.println("constructor");
    }
}
// output
/*
0
10
dog
constructor
*/
```

### Immutable class

remove setters, input through constructor only

return value data, don't return reference to modifiable object

### Lambdas

https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html this article is a great explanation of what lambdas are for and how they are used.

Lambda is quick and short implementation of an interface (functional interface which has only one abstract function)

```java
// assume a simple class
class Person {
    private int age;
    private String name;
    private String email;

    public Person(String name, int age, String email) {
    	this.name = name;
        this.age = age;
        this.email = email;
    }
    public int getAge(){ return age; }
    public String getName(){ return name; }
    public String getEmail(){ return email; }
    public void print(){  }
}
```

To search all persons matching a specific criteria, we can create an interface to match person and its various implementations for different kind of searches e.g.

```java
public interface MatchPerson {
    boolean match(Person p);
}
public class IsElderMale implements MatchPerson {
    boolean match(Person p) {
        return p.age > 50 && p.gender == Person.Sex.MALE;
    }
}
public class IsKid implements MatchPerson {
    boolean match(Person p) {
        return p.age < 5;
    }
}
```

e

```java
// a -> a.canDo();
// {Animal a} -> { return a.canFly(); }
// {Animal a} ->  return a.canFly(); // omit braces because single statement, like if

public interface CheckInterface {
    boolean test(Animal a);
}

// old way
public CanFly implements CheckInterface {
    public boolean test(Animal a){
        return a.canFly();
    }
}
public CanHop implements CheckInterface {
    public boolean test(Animal a) {
        return a.canHop();
    }
}

public print(Animal a, CheckInterface c) {
    if(c.test(a))
        System.out.println("can do");
}

// using lambda

///////////////////
// java provides Predicate<T> for simple test interfaces like CheckInterface
/*
interface Predicate<T> {
    boolean test(T t);
}
interface Consumer<T> {
    void accept(T t);
}
interface Function<T, R> {
    R apply(T t);
}
*/

// using above interfaces provided by java, we can redefine our methods like following
public printPredicate(Animal a, Predicate<Animal> p){
    if(p.test(a))
        System.out.println("can do");
}
// why just print items filterd with lambda, we can even make print operation generic
public processAnimal(Animal a, Predicate<Animal> p, Consumer<Animal> op) {
    if(p.test(a))
        op.accept(a);
}
// taking it even further, get result from a function and pass it to Consumer
public processFunction(Animal a, Predicate<Animal> p, Consumer<Integer> op, Function<Animal, Integer> func) {
    if(p.test(a)) {
        int age = f.apply(a);
        op.accept(age);
    }
}

// Now example of different print methods, including lambdas
public static void main(String ...args){
    Animal a = new Animal();
    a.setCanFly(true);

    // old ways
    // either provide the existing implementation in here
	print(a, new CanFly());
    // or for a different check, create new anonymous class
    print(a, new CheckInterface(){ public boolean test(Animal a){ return a.age() > 5; }} );

    // using lambda
    print(a, {Animal a} -> { return a.age > 5; });
    // short form
    print(a, a -> a.age > 5);
    // using lambda on predicate in same way
    printPredicate(a, a -> a.age > 5);
    // taking it one step further
    processAnimal(a, a -> a.age > 5, a -> {System.out.println("can do");});
    // include Function<T, R>
    processFunction(a,
                    a -> a.age > 5,
                    age -> {System.out.println("can do"+age);},
                    a -> a.age
                   );
}
```


## Chapter 5

Either `this()` or `super()` can only be used in the first line in a constructor. First statement of every constructor is either `this()` or `super()`.

Default constructor exists only only when no constructor is provided.

In all child class constructors, a hidden `super()` call to parent is present if not explicitly provided. This can cause compile error if parent doesn't have a no-argument constructor.

Hidden `super()` call can be replaced with an explicit `super(...)` or `this(...)` call.

```java
public class Mammal {
	public Mammal(int age) {} // Constructor
}
public class Elephant extends Mammal {
    public Elephant() { // COMPILE ERROR
        // super(); // hidden call to default super constructor which does not exist
        // can be fixed using super(10);
    }
}
public class Dog extends Mammal {
    // COMPILE ERROR
    // because default constructor of this class has a super() call
}
public class Monkey {
    public Monkey(){ this(10); }
    public Monkey(int age) { // COMPILE ERROR
        // super(); // hidden call to default super constructor which does not exist
        // can be fixed using super(10);
    }
}
```

### Overriding

Child class method must have ***same or more access*** than parent be it an abstract class or interface.
Child method ***return type must be same or subclass*** of parent
Child can only throw same or subclass of checked exception in parent, or no exception
Child cannot throw checked exception that is new or broader than parent
Child can re-declare private methods of parent.
Child can only override static method with static method, and instance method with instance method. Overriding/hiding static with instance or vice versa is compile error. C# allows doing both ways.
Child ***CAN NOT override/hide final*** methods

```java
class Person {
    Person walk(){ return new Person(); }
}
class Student extends Person {
    Student walk(){ return new Student(); } // override with subtype, covariant return type
}
```


When parent method is called from child, which calls a static method (same in parent and child), an instance method (same in parent and child) and a variable (same in parent and child)

- static parent method is called
- instance method of child is called
- variable of parent is called

Static methods are hidden in child classes because don't belong to an instance. They belong to class itself.

```java
// example
class Parent {
    public String i = "parent instance var";
    public static String s = "parent static var";

    public static void staticmethod(){
        System.out.println("parent static method " + s);
    }

    public void instancemethod(){
        System.out.println("parent instance method " + i + " and " + s);
    }

    public void dad(){
        System.out.println("Dad()");
        staticmethod();
        instancemethod();
    }
}

public class Child extends Parent {
    public String i = "child instance var";
    public static String s = "child static var";

    public static void staticmethod(){
        System.out.println("child static method " + s);
    }

    public void instancemethod(){
        System.out.println("child instance method " + i + " and " + s);
    }

    public void son(){
        System.out.println("Son()");
        staticmethod();
        instancemethod();
    }

    public static void main (String ...args){
        Child cc = new Child();
        cc.son();
        //Son()
        //child static method child static var
        //child instance method child instance var and child static var

        cc.dad();
        //Dad()
        //parent static method parent static var
        //child instance method child instance var and child static var

        Parent pc = new Child();
        //Dad()
        //parent static method parent static var
        //child instance method child instance var and child static var

        Parent pp = new Parent();
        System.out.println(cc.i); // child instance var
        System.out.println(pc.i); // parent instance var
        System.out.println(pp.i); // parent instance var
    }
}
```

Overridden methods must have same or more access than parent.

```java
interface walkable {
    void walk();
}

class Person implements walkable {
    walk() {} // Compile error, interface methods are public there this must also be public
}
```

`private` methods are hidden from child class. If called from where `private` is visible, it will be called.

```java
// example
class Parent {
    private void instancemethod(){
        System.out.println("parent private instance method ");
    }
    public static void main (String ...args){
        Parent p1 = new Child();
        p1.instancemethod();
        // output: parent private instance method

        Child c1 = new Child();
        c1.instancemethod();
        // output: child instance method
        // its because child can not see the parents private method
    }
}

public class Child extends Parent {
    public void instancemethod() {
        System.out.println("child instance method ");
    }
}
```

### Abstract

A class or method which is not completely defined is abstract. `abstract class` and `interface` are two abstract type of classes.

#### Abstract class

Abstract class = Concrete class + Abstract Methods

Can not be instantiated.

Can not be `final`.  e.g. `abstract final class {}` is invalid.

Can be empty.

Can `extend` only concrete or abstract class.

Can `implement` interface.

May not provide implementation of inherited `abstract` methods from `abstract class` or `interface`.

Abstract method = `abstract <<type>> methodname();`

#### Abstract method

Can not have a defition.

Can not be `final`.

Can not be `private`.

Only exist in `abstract class` and `interface`.

Child of abstract class must either provide implementation OR marked as abstract.

```java
public abstract class Walker [extends (concrete|abstract)] {
    public abstract void walk();
    public abstract void jog();
    public void useLegs(){ }
}
// keywords in <<...>> are always assumed if not written explicity
```

#### Interface

Interface can be empty.

Interface cannot be final.

`abstract` keyword is assumed with interface and its methods.

Can contain only constants (`final`) which are assumed `public static`.

```java
public <<abstract>> interface CanWalk {
    <<public>> <<static>> <<final>> int LEGS = 4;
    <<public>> <<abstract>> void walk();
    <<public>> default void useLegs(){ }
    <<public>> static int getlegs() { return 4; }
}
public interface CanRun extends CanWalk {
    <<public>> void useLegs()<{}>;
}
// keywords in <<...>> are always assumed if not written explicity
```

`interface extends interface` Interface cannot implement another interface.

`[abstract] class implements interface` Class cannot extend interface.

static methods are not inherited and are only referenced using interface name.

#### Multiple inheritance with interfaces

Interfaces with same method signatures can not be applied on a class until that class redefines that method.

All non-final, non-static, non-private methods are virtual methods in Java because they can be overridden at runtime.

```java
interface House{
    public default String getAddress() {
        return "101 Main Str";
    }
}
interface Hotel {
    public default String getAddress() {
        return "Regent Str";
    }
}
interface Bungalow extends House {
    public default String getAddress() {
        return "101 Smart Str";
    }
}

class MyHouse implements Bungalow, House { } // No problem here because Bunglow is already a House

class BizzareBuilding implements Hotel, House { } // Compile error, unrelated method getAddress() is inherited from both House and Hotel

public class TestClass {
    public static void main(String[] args) {
        House ci = new MyHouse();
        System.out.println(ci.getAddress());
    }
}
```

## Chapter 6 - Exceptions

### Exceptions Tree Diagram

```mermaid
graph LR

th("Throwable [Checked]")
err("Error [Unchecked]")
exc("Exception [Checked]")
so(StackOverflowError)
asrt(AssertionError)
ncd(NoClassDef)
io("IOException [Checked]")
rt("RuntimeException [Unchecked]")
np(NullPointerException)
cc(ClassCastException)
arth(ArithimeticException)
nfmt(NumberFormatException)
iarg(IllegalArgumentException)
istate(IllegalStateException)
sec(SecurityException)
iob(IndexOutOfBoundsException)
aiob(ArrayIndexOutOfBoundsException)
fnf(FileNotFoundException)

th --> err
th --> e2("Exception [Checked]")

exc --> rt
rt --> np
rt --> cc
rt --> arth
rt --> iob
iob --> aiob
rt --> sec
rt --> iarg
rt --> istate
iarg --> nfmt
err --> so
err --> asrt
err --> ncd
exc --> io
io --> fnf


classDef unchecked fill:#f9f,stroke:#333,stroke-width:4px;
class err,rt,so,asrt,ncd,np,cc,arth,iob,aiob,sec,iarg,nfmt,istate unchecked;
```

Errors are thrown by JVM and should not be handled or declared.
Some known errors are `ExceptionInInitializerError`, `StackOverflowError`, `NoClassDefFoundError`


```java
// correct order of try catch finally
try {}
catch() {}
finally {}

// catch is optional if finally is present
try {}
finally {}
```

Exceptions are handled by `catch`. If not handled, they will be thrown.

```java
public static void main(String ... arg) {
    try {
        System.out.println("try");
        throw new IndexOutOfBoundsException();
    }
    finally {
        System.out.println("finally");
    }
}
// try
// finally
//
// Exception in thread "main" java.lang.IndexOutOfBoundsException at MyClass.main(MyClass.java:5)
```

### Checked vs Unchecked

Methods throwing checked exceptions must be written within `try catch` block.
Methods throwing unchecked exception do not need it.

All exceptions under `RuntimeException` and `Error` are *unchecked*, all remaining exceptions including Throwable are *checked* exceptions

### Subclass exceptions

Most specific exceptions must be catched first. If parent exception is handling all further exceptions, other catch blocks are uncreachable.

```java
try {
    badcode();
} catch (Throwable e) {
    // handle exception
} catch (Exception e) { // compile error, unreachable code
    // handle exception
}
```

Doing it the other way is fine

```java
public void badcode() throws RuntimeException{}
try {
    badcode();
} catch (RuntimeException e) {
    // handle exception
} catch (Exception e) {
    // handle exception
} catch (Throwable e) {
    // handle exception
}
```

If we try to catch a *checked exception* which is not in try block, catch block is unreachable

```java
public void goodcode(){}
try {
    goodcode(); // does not throw any exception
} catch (java.io.IOException e) { // compile error, unreachable code
    // handle exception
}
```

Overriding method CANNOT throw checked exception that is new or broader than parent. It can throw less exceptions than parent.

Child constructors, on the other hand, must throw same or broader exceptions than parent.  

Reason: there is a hidden super() call to the parent constructor.

```java
import java.io.*;

class CanNotHopException extends Exception {}

class Hopper {
    Hopper() throws CanNotHopException { } // parent constructor exception
    public void hop() throws CanNotHopException { }
    public void jump() throws CanNotHopException { }
}
class Bunny extends Hopper {
    Bunny() throws Exception { } // broader exception in constructor is valid
    Bunny(int x) throws Exception, CanNotHopException, IOException { } // new exceptions are valid
    Bunny(int x, int y) { } // Compile error, CanNotHopException must be caught or declared to be thrown

    public void hop() { }  // valid
    public void jump() throws CanNotHopException, IOException { }  // compile error, overridden method hop() does not throw IOException
}
```

and

```java
class CanNotHopException extends Exception {}
class Hopper {
    public void hop() throws Exception { }
}
class Bunny extends Hopper {
    public void hop() throws [CanNotHopException|Exception| ] { }
}
```

This is only for checked exceptions. Unchecked exceptions can be declared anywhere. No declaration is required.

```java
class Hopper {
    public void hop() { }
}
class Bunny extends Hopper {
    public void hop() { throw new IllegalStateException(); }
}
```

## other notes

inheritance

default constructor is created only if none is given in a class

there is a call to default super constructor if not called inside any child constructor

```java
public class A {
    // if no constructor defined
    // then following is generated
    // a() {}
}
public class B extends A {
    B(){
        // if no call to super constructor explicitly
        // then contains following by default
        // super();
    }
}
public class C extends A {
    // no constructor defined
    // then following is generated
    // C(){
    //   super();
    // }
}
```



functional interface

```java
@FunctionalInterface
public interface SimpleFuncInterface {
  // only one abstract method
  public void doWork();
  // any number of default methds
  default public void print(int i){System.out.println(i);}
  // any number of static methods
  static int pi(){return 3.17;}
}
```



**interface**

```java
public interface A {
    public /*static final*/ int a = 10;
	// fields in interface are by default public static final
    // but it is not usally explicitly written.
}
```
C# does not allow fields or constants in interface



overriding

```java
class ShapeBuilder {
    ...
    public Shape build(String name) { }
    ....
}****

class CircleBuilder extends ShapeBuilder{
    ...
    @Override
    public Circle build(String name) { } // Circle is subtype of Shape
    ....
}
```

overriding method can opt to not throw any exception if parent method throws exception, this is because no exception is valid subset of exceptions.



types

**integral types**: byte, short, char, int, long,

| type  |                |               |
| ----- | -------------- | ------------- |
| byte  | -128           | 127           |
| short | -32,768        | 32,767        |
| char  | 0              | 65,535        |
| int   | -2,147,483,648 | 2,147,483,647 |

casting

casting from type A to type B when A can already fit in B. e.g. byte, char or short to int, long or float does not need a cast.



operators

*, / and % have the same level of precedence.

bitwise operators &, ^, | have specified behaviour when used on boolean

>   When both operands of a `&`, `^`, or `|` operator are of type `boolean` or `Boolean`, then the type of the bitwise operator expression is `boolean`. In all cases, the operands are subject to unboxing conversion ([§5.1.8](https://docs.oracle.com/javase/specs/jls/se7/html/jls-5.html#jls-5.1.8)) as necessary.

>   For `&`, the result value is `true` if both operand values are `true`; otherwise, the result is `false`.

>   For `|`, the result value is `false` if both operand values are `false`; otherwise, the result is `true`.

>   For `^`, the result value is `true` if the operand values are different; otherwise, the result is `false`.



short-circuit

```java
boolean a(){return true;}
boolean b(){return false;}

boolean c = b() && a(); // short-circuit, a() is not called
boolean d = b() & a();  // no short-circuit, both b() and a() are called
```



None > Widening > Autoboxing > Varargs

```java
static void m(int l){System.out.println("nothing");}
static void m(long l){System.out.println("Widening Long");}
static void m(Integer i){System.out.println("Boxing Integer");}
static void m(Integer... i){System.out.println("Varargs Integer");}
public static void main(String args[]){
   int a=30;
   m(a);
}
```

public > protected > package (default) > private

---