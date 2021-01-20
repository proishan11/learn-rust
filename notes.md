# Rust Notes from Tensor Programming

- Rust use rustup as a version manager (comes with rustc - The Rust compiler)
- Cargo is the build tool and a package manager
  - $ cargo run (Build + Run)
  - $ cargo new project_name
  - $ cargo new project_name --bin (Creates binary project)
  - $ cargo build
  - $ cargo check (No code generation, useful for checking if code compiles or not)
  
  - Cargo create files Cargo.toml

  - Note the all the code should be contained in src/ . The main directory contains configurations, Licenses etc.

## Language

Rust is System level programming language while having powerful abstractions build-in unlike C++

A binary project has main() function.

## Basic Types

  - Variable definition <br>
    ``` let x = 15; ``` <br>
    ``` let mut x = 15; ```

    Variables are immutable by defaultP

  - Integer Types : i8, u8, i16, u16 ... till i64
  Float Types : f32, f64

  - Char type: 
    ``` let c : char = 'z'; ```
  
  - Tuples:
    ``` let t: (i32, f64, char) = (42, 1.0, 'j'); ``` <br>
    Tuple destructuring : 
    ``` let (_, _, x) = t; ``` <br>

    Tuples can be nested
    Example :
    ```rust

      let t = (1, (2, 'a'));
      println!("{}", t.0)
      println!("{}", t.1) // Doesn't work as tuple doesn't implement display trait

      println!("{:?}", t.1) // Prints (1, 'a', false)
      // This works because tuple has a debug trait build into it

      // Use :#? for pretty printing in debug mode 
    ```
  - Arrays:
  ``` rust
    let a = [1, 2, 3, 4];
    // Indexing : a[0] 
  ```

  ```rust
    let a: [i32, 5] = [1, 2, 3, 4, 5] // defines an array of type i32 containing 5 elements
  ```

    a.len() for size of array

    use std::mem
    mem::size_of_val(&a) // Gives size in bytes. Import this use std::mem;

  
  - Slicing Arrays <br>
    // Excludes last index <br>
    ```rust
      
      let b = &a[2 .. 4]; <br>
      println!("{}", b[0])
    
    ```
  - Strings
    Strings are not literal types in rust. Strings are growable, UTF-8 encoded bit of text.
    Example :
  ``` rust
    let s = "String"; // Slice of a string Type &str
    let ss = String::from("String!"); // This has a type String
    let s = "String".to_string(); // Type String
    let slice = &ss[0 .. 4];
  ```

  let s = h + &w // String concatenation


- Function that return anything returns an empty tuple. Main function return empty tuple

## Ownership & Borrowing

- In Rust, literals like Integers, Booleans, Slices of strings etc are stored in Stack
- Heap is for complicated data. C++, C can manipulate this memory directly which can lead to problems. Java has garbage collection. Rust lies somewhere in the middle and has ownership.

### Ownership

```rust
  let x = 1; // This means x owns 1
  let y = x;

  {
    let a = 1;
  }

  x + a; // Error as a isn't in scope
```

```rust
let s = String::from("String");
let y = s; // s gets entirely out of scope

println!("{}", s); // Error has we have moved the reference s to y

```

### Borrowing

```rust
let s = String::from("String")
let y = &s; // Borrowing
println!("{}", s); // Now this will not give any error

```

### Examples of borrowing and Ownership

```rust
fn take(v : Vec<i32>) {
  println("We took v: {}", v[0]);
}

fn main() {
  // Data on heap
  let mut v = Vec::new();

  for i in 1 .. 1000 {
    v.push(i);
  }

  // This is moving as it passes the data from one function to another
  take(v); // Passes the ownership and now v is no longer in scope. So cannot use v after this statement
  println!("Finished!")
}

```

```rust
fn cop(a: i32, b: i32) {
  println!("{}", a + b);
}

fn main() {
  let a = 32;
  let b = 45;

  // Since a, b lives on stack this will copy the data and no ownership will be passed
  cop(a, b);

  // Can refer a, b here as well
}

```


- Borrowing lets us have multiple references to a resource. But it is still adhering to a single owner. (More like pointers)

- References are also objects. Mutable references are moved and immutable references are copied. When a reference is dropped the borrower ends.

### Structs

```rust
#[derive(Debug)]
struct Object {
  width: u32,
  height: u32,
}

fn area(obj: Object) -> u32 {
  // No need of return statement
  obj.width * obj.height
}

// Created a new namespace
impl Object {
  fn area(&self) -> u32 {
    self.width * self.height
  }

  // No &self param means it is no method
  fn new(width: u32, height: u32) -> Object {
    Object {
      width: width,
      height: height,
    }
  }

  fn show(&self) {
    println!("{}x{} with area: {}", self.width, self.heigh, self.area());
  }
}

// Note that you can have another block of Object impl and it's allowed. To separate different kind of methods we can use it.
// Each impl contains related methods
impl Object {
  // More methods here
}

fn main() {
  let o = Object {
    width: 35,
    height: 32,
  };

  o.new() // Error
  let obj = Object::new(1, 2)

  println!("{}", area(&o));
  println!("{}", o.area());

  o.show()

  println({:?}, o) // Error: Struct doesn't implement the debug/display trait

  // Use derive annotation to print struct. #[derive(Debug)]
  // Note that you cannot derive display trait. You have to do it manually
}
```

```rust

  use std::fmt; // Display trait reside here

  imp fmt::Display for Object {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
      write!(f, "({}, {})", self.width, self.height)
    }
  }

```

### Control Flow

```rust

  if n < 5 {
    println!();
  } else if n %2 == 0 {
    //
  } else {
    //
  }

  // If else can be used in bindings. For example:
  // But cannot have different types in if else blocks
  let x = if c {
    30
  } else {
    40
  };

```

- Loops

``` rust
  // infinite loop
  loop {
    // statements and expressions
  }

  // Can have labels on loops

  `a : loop {
    println!("A");
    `b: loop {
      println!("B");

      if something {
        // Can use labels to break loops
        break `b
      }

      if true {
        continue
      } else {
        break
      }
    }
  }

  // Loop statements in bindings
  let x = loop {
    break 10;
  };
  println!("x: {}", x) // Prints => x : 10
```

```rust

  // while loops
  while n != 0 {
    // statements;
  }

  // for loops
  let a = vec![10, 20, 30];
  for i in a {
    println!(i);
  }

  // Ranges
  // Doesn't include 100
  for i in 1 .. 100 {
  
  }

```

```rust

  // Match

  let x = 4;
  match x {
    1 => println!(1), // Comma and not ;
    2 => println!(2),
    
    // default case
    _ => // statement
  }

  // A complicated example. Match is not just another if else statements. Here is why: 

  match x {
    1 => println!("One!"),

    //  Multiple cases sitting together
    2 | 3 | 5  | 7 | 11 => println!("Prime"),
    
    // Starts matching with this range including 19
    13 ... 19 => println!("A teen"),
    _ => println!("Ain't special"),
  }

  match pair {
    // Matches first element = 0
    (0, y) => println!("y : {}", y),

    (x, 0) => //
  }


  // Guards: Are useful when you further wanna specify how to match the data

  match pair {
    (x, y) if x == y => println!("Equal"),
    (x, y) if x + y != 0 => println!("Equal not zero"),
    _ => println!("No match"),
  }

  let p = 4;
  match p {
    n @ 1 ... 12 => println!("n : {}", n), // 4 gets bound to n and then prints n. Useful to clone the matched value.
    n @ 13 ... 19 => println!("n : {}", n),
    _ => println!("No match"),
  }

  // Using @ in declarations
  let n = match p {
    n @ 1 ... 12 => n,
    n @ 13 ... 19 => n,
    _ => 0,
  };

  println!(n)

```


### Enum (enumerations) and Matching

- Another way for custom types other than struct.

- Enum also does not implement Debug/Display trait

```rust
  #[derive(Debug)]
  enum Direction {
    Up,
    Down,
  }

  enum Direction {
    Up(u32),
    Down {x: u32, y: f64}, // Can be a struct
  }

  enum Direction {
    Up(Point),
    Down(Point),
    Left(Point),
    Right(Point),
  }

  enum Key {
    UpKey(String),
    DownKey(String),
    LeftKey(String),
    RightKey(String),
  }

  struct Point {
    x: i32,
    y: i32,
  }

  impl Direction {
    // Pattern matching
    fn match_direction(&self) -> Key {
      match *self {
        Direction::Up(_) => Keys::UpKey(String::from("Pressed w")), // If direction is up return UpKey
        Direction::Down(_) =>  Keys::DownKey(String::from("Pressed s")),
      }
    }
  }

  impl Key {
    fn destruct(&self) -> &String {
      match *self {
        // ref s is reference to a string (&String)
        Keys::UpKey(ref s) => s,
        Keys::DownKey(ref s) => s,
        Keys::LeftKey(ref s) => s,
        Keys::RightKey(ref s) => s,
      }
    }
  }

```

- Ref Keyword:

```rust
  let u = 10;
  let v = &u;
  let ref z = u;

  // z is equal to v
  if z == v {
    println!("Equal");
  }

```

- Note type casting is done by as
```rust
  let width = 1;
  let height = 2.3;

  let area = (width*height) as f64
```

- Rust doesn't have nil. As they say, nil is a billion dollar error. Rust has Options. Option is a basic enum type in std library

```rust

  // In standard library Option is an enum whose structure is as follows
  // enum Option<T> {
  //      Some(T),
  //      None,
  // }


  fn division(x: f64, y: f64) -> Option<f64> {
    if y == 0.0 {
      None
    } else {
      Some(x / y)
    }
  }

  // Usage of Option return type

  let res = division(5.0, 7.0);
  // Do a pattern match (In Rust Pattern matching >> if else)
  match res {
    Some(x) => println!("{:.10}", x), // Prints up to 10th decimal digit
    None: println!("divide by zero error");
  }

```

### Vectors, Hashmaps, Casting, If-Let, While-Let and Result Enum

- Vectors (Resizable arrays)

```rust
  // Vector creation using vec! macro
  let x = vec![1, 2, 3];

  // Size not known at compile time and like slices has 3 components :
  // Pointer to the first element, size and capacity
  
  // Creation using namespace Vec
  let mut v = Vec::new();

  v.push(1);
  v.push(2);

  for i in &v {
    println!("{}", i);
  }

  // Debug
  println!("{:?} {} {}", &v, v.len(), v.capacity()); // [1, 2] 2 2

  // The resizing algorithm is same as the slices in Go (Doubling each time the capacity is reached)

  // pop method pops the last value in the Vector (can be None/Some)
  println!("{:?}", v.pop()); // Some(10)


  // Type annotation
  let mut v: Vec<i32> = Vec::new();
  // v.pop() gives none here

  // We can embed enum inside a Vector
  // This can help us store multiple types in the vector
  // But technically 
  #[derive(Debug)]
  enum Example {
    Float(f64),
    Int(i32),
    Text(String),
  }

  fn main() {
    let r = vec![
      Example::Int(142),
      Example::Float(12.32),
      Example::Text(String::from("string")),
    ];

    println("{:?}", &r) // [Int(142), Float(12.32), Text("string")]
  }
```

- Hashmap

```rust

  use std::collections::HashMap;

  fn main() {
    let mut hm = HashMap::new();

    // Types must be same
    hm.insert(String::from("random"), 12)
    hm.insert(String::from("strings"), 11)

    // Iterate
    for (k, v) in &hm {
      println!("{}: {}", k, v);
    }

    // getting Values using key and matching
    // Return value of get method on hashmap is an Option
    // So match is used to retrieve the value
    // Use match wherever you get an Option as a return value for now
    match hm.get(&String::from("random")) {
      Some(&n) => println("{}", n),
      _ => println!("No Match");
    }

    // Remove removes key and value
    hm.remove(&String::from("strings"));
  }

```


- If-let bindings

```rust

  // Destructuring Options

  fn main() {
    let s = Some('c');
  }

  match s {
    Some(i) => println!("{}", i),
    _ => {}
  }

  // OR the above matching is not very handy

  if let Some(i) = s {
    println!("{}", i);
  } else {   // else block can be removed
    {}
  }

  // One downside of if binding is that it is not exhaustive. Not consider all cased

  // match is exhaustive
  let mut s = Some(0);

  loop {
    match s {
      Some(i) => if i > 19 {
        println!("Quit");
        s = None;
      } else {
        println!("{}", i);
      },
      _ => {
        break;
      }
    }
  }

```

- While-let binding

```rust
  let mut s = Some(0);

  // This is not exhaustive to. Use if you just wanna check if there is Some value present or not. Example : popping elements from queue can be done as while let Some(i) = q.pop() {}
  while let Some(i) = s {
    if i > 19 {
      println!("Quit");
      s = None;
    } else {
      println!("{}", i);
      s = Some(i + 2);
    }
  }
```


### Coersion (Non Implicit Type Conversion)

Rust provides coersion between primitive types and explicit type conversion (casting) using as

```rust

  let f = 24.43
  let i = f as u8;
  let c = i as char;
  

  println("{}", 255 as char);
```

### Result Enum

```rust

// Internal structure of Result Enum
enum Result<T, E> {
  Ok(T),
  Err(E),
}

// Let's us store Errors. Used for error checking.

// Example

use std::fs::File;

fn main() {
  let f = File::open("text.txt");

  // Opening a file can cause an error and it returns a Result enum
  let f = match f{
    Ok(file) => file,
    Err(error) => {
      panic!("There was a problem opening the file {}", error);
    },
  };
}


```

### Traits and Generics

```rust

trait Shape {
  fn area(&self) -> u32;
}

struct Rectangle {
  x: u32,
  y: u32,
}

struct Circle {
  radius: f64,
}

// Implement the trait
impl Shape for Rectangle {
  fn area(&self) -> u32 {
    self.x * self.y
  }
}

// Compiler provides some inbuilt implementation of traits like Debug, Clone etc

// Clone trait:
let a = A(32);
let c = a;
// Cannot use a after this. But if #[derive(Debug)] is used over A. We can do let c = a.clone();

// Copy trait:
// #[debug(Copy)] lets us copy by default.
// We can refer `a` even after let c = a statement.

// Eq, PartialEq, PartialOrd, Ord


```

### Operator Overloading

- Example:

```rust

  // Operators namespace
  use std::ops;

  struct A;
  struct B;

  struct AB;
  struct BA;

  imp ops::Add<B> for A {
    type output = AB;

    fn add(self, _rhs: B) -> AB {
      AB
    }
  }

  impl ops::Add<A> for B {
    type Output = BA;
    fn add(self, _rhs: A) -> AB {
      BA
    }
  }

  // Similarly can also implement A+A or B+B

  fn main() {

  }

```

### Drop trait (Removes data from memory once get's out of scope)

```rust

  struct A {
    a : String,
  }

  impl Drop for A {
    fn drop(&mut self) {
      println!("dropped {}", self.a);
    }
  }

  // Outputs :
  // dropped C
  // dropped B
  // dropped A
  fn main() {
    let a = A{a: String::from("A")};
    {
      let b = A{a: String::from("B")};
      {
        let c = A{a: String::from("C")};
      }
    }

    drop(a)
  }

```

### Iterator trait (Implementing this gives us lots of methods on the data type like take(), skip() etc)

```rust

  struct Fib {
    c: u32,
    n: u32,
  }

  impl Iterator for Fib {
    type Item = u32;

    // next fibonacci number
    fn next(&mut self) -> Option<u32> {
      let n = self.c + self.n;
      self.c = self.n;
      self.n = n;

      Some(self.c)
    }
  }

  fn fib() -> Fib {
    Fib{c: 1, n: 1}
  }

  fn main() {
    // Take ten elements from this iterator
    for j in fib().take(10) {
      println!("{}", j);
    }

    // Skips 14 elements then take next 10
    for j in fib().skip(14).take(10) {
      println!("{}", j);
    }

    let mut f = fib();
    // gives next fibonacci number
    f.next();
  }

```


### Generics

```rust
  struct Square<T> {coco
    x: T,
  }

  // instantiation
  let s = Square{x: 10};
  let s = Square{x: "Hello"};

  // Generics reduce code duplication

  // Generics use inside a function

  // Can print any type as long as it has the inbuilt Debug trait
  // T: fmt::Debug  =>  It is short form for deriving Debug trait for type T
  fn p<T: fmt::Debug>(x: T) {
    println!("{:?}", x);
  }

  // This will throw compiler error saying T doesn't have debug trait
  fn p<T>(x: T) {
    println!("{:?}", x);
  }

  
  // Generics inside interface implementation block

  struct A<T> {
    x: T,
  }

  // Here T is mentioned twice because A<T> is specified because impl for type A. The T 
  // in A<T> has the scope inside the struct only. So we need to specify another T in the
  // impl block which has the scope in impl block
  
  impl <T> A<T> {
    fn item(&self) -> &T {
      &self.x
    }
  }


  // It should still work
  impl <Whatever> A<T> {
    fn item(&self) -> &Whatever {
      &self.x
    }
  }

  let a = A{x: "Hello"};
  a.item(); // Returns the item "Hello"



  // Multiple generic fields
  struct A<U, V> {
    x: U,
    y: V,
  }

  struct B<V> {
    x: V,
    y: V,
  }
  

  // Generic example of a trait
  use std::ops::Mul;

  trait Shape<T> {
    fn area(&self) -> T;
  }

  // T should be multiplicable
  struct Rectangle<T: Mul> {
    x: T,
    y: T,
  }

  // T must implement the Copy trait. Output should be of type T
  impl <T: Copy> Shape<T> for Rectangle<T>
    where T: Mul<Output = T>, {
      fn area(&self) -> T [
        self.x * self.y
      ]
  }


  // Another way of doing it
  // Note that we use + to write multiple guards on T
  impl <T:Mul<Output = T> + Copy> Shape<T> for Rectangle<T> {
    fn area(&self) -> T {
      self.x & self.y
    }
  } 


  // The problem here is that if we have a circle the area function would return
  // 3.14*radius*radius which would result in a compile error as the compiler
  // doesn't know how to multiply 3.14 with type T. We can use casting and macros
  // to fix this.
```


### Box Pointer, Iterators, Closures and Higher Order Functions

- Smart Pointers are pointers with some addditional meta data

```rust

// Every allocation is done on stack in Rust unless we wrap it in a box

fn main() {
  let b = Box::new(10);
  println!("b = {}", b);
}

// Example of Box namespace (A List data structure from Lisp) and an interesting usage of Enum
enum List {
  // will throw compile error that List is of infinite size (because of stack
  // allocation
  // Con(i32, List),
  // i32 for index of element and other element for pointing to next element
  Con(i32, Box<List>),
  End,
}

// To use Cons directly rather than List::Cons;
use List::Cons;
use List::End;

fn main() {
  // This will create a list
  let l = Cons(1, Box::new(Cons(2, Box::new(Cons(3, Box::new(End))))))
}

// Box sort of works like reference doews. Example:
fn main() {
  let y = 4;
  let x = &y;
  let x = Box::new(y);

  if *x == *z {
    println!("True"); // Prints true
  }
}

```

- fn is a macro in Rust

### Closures and Anonymous Functions

``` rust

fn main() {
  // Anonymous function. No type annotation needed with parameter i but can put it nonetheless.
  let f = |i| i+1;
  // With type annotation
  let g = |i: i32| -> i32 i+1;

  // To call
  let x = 10;
  f(x);

  // Create closure which take no value;
  let p = || println!("This is a closure");
  p();

}

// Example of closure

fn main() {
  let mut c = 0;
  let mut inc = || {
    // inc closure is borrowing c from outer scope
    c += 1;
    println!("incremented by 1 : {}", c);
  };

  inc();
  inc();
  inc();
}


// Example 2:

// Generic function run with type F and F must be a fn (Fn trait is used) with the where clause
fn run<F>(f: F)
where F: Fn() {
  f();
}

// Generic function where f must be of type fn(i32) -> i32
fn add3<F>(f: F) -> i32
where F: Fn(i32) -> i32 {
  f(3)
}

// Struct that stores a function
struct A<F: Fn(i32) -> i32> {
  f: F
}

fn main() {
  let p = || println!("hello");
  run(p);

  let x = |i| i*10;
  add3(x);

  let a = A{
    f: x,
  };
}
```





### Rust Usage

- Taking Input
```rust

  use std::io

  let mut inp = String::new();
  io::stdin().read_line(&mut inp)
    .expect("Failed to read line");

```