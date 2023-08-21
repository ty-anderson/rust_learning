# Rust Notes

## Table of Contents
1. [Cargo](#1-cargo)
2. [Running a Program - Detailed](#2-running-a-program---detailed)
3. [Rust Basics](#3-rust-language-basics)
    * [Data Types](#data-types)
    * [Variables](#variables)
    * [References](#references)
    * [Shadowing](#shadowing)
    * [Scope](#scope)
    * [Statements & Expressions](#statements--expressions)
    * [Functions](#functions)
    * [Returning from Function](#returning-from-function)
    * [If Expressions](#if-expressions)
    * [Loops](#loops)
    * [Comments](#comments)
4. [Ownership](#4-ownership)

Official Docs: https://doc.rust-lang.org/book/ch01-03-hello-cargo.html

## 1. Cargo

Cargo is Rusts build system and package manager.

A rust project will have the following files:
* src/
  * main.rs
* Cargo.toml
* .gitignore
* .git

Create a project in new directory: `cargo new project_name` \
Create a project in existing directory: `cargo init`

**Cargo.toml** is the config file for your project and looks like this:

```toml
[package]  # SECTION HEADING
name = "rust_learning"
version = "0.1.0"
edition = "2021"

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]  # PACKAGE DEPENDENCIES - KNOWN AS CRATES
```


### File Structure
Cargo expects your code to live in the src folder. 
The top-level project directory should only be used for README files, licenses, config files, 
and other things not directly related to your code.

### Create and Run w/ Cargo:
1. Create a rust project
```
cargo new project_name
cd project_name
```
2. Update **main.rs** files in src folder. This the source code of your project.
3. Compile and run `cargo run`
4. To build non-debug file `cargo build --release`

<hr>

## 2. Running a Program - Detailed

##### Build Process
When your project was created with cargo, compile the project with: `cargo build`. 
The default build made is debug, so cargo puts your file .exe file into the following directory
`./target/debug/<filename>.exe`

But this is not very efficient.  Instead of compiling and running in two steps, we can do both with: `cargo run`

##### Compile Check
You can also check if your code will compile, but not creating an .exe file, by running: `cargo check`

##### Build Profiles, Dev and Release
When your project is finally ready for release, you can use `cargo build --release` to compile it with optimizations. 
This command will create an executable in `target/release` instead of `target/debug`. 
The optimizations make your Rust code run faster, but turning them on lengthens the time it takes for your 
program to compile. This is why there are two different profiles: 
1. Development, when you want to rebuild quickly and often
2. Building the final program you’ll give to a user that won’t be rebuilt repeatedly and that will run as fast as possible. 

##### Benchmarking
If you’re benchmarking your code’s execution time, be sure to run `cargo build --release` and benchmark with the executable in `target/release`.

##### Cargo.lock
Running cargo build for the first time also causes Cargo to create a new file at the top level: Cargo.lock. This file keeps track of the exact versions of dependencies in your project. This project doesn’t have dependencies, so the file is a bit sparse. You won’t ever need to change this file manually; Cargo manages its contents for you.

From <https://doc.rust-lang.org/book/ch01-03-hello-cargo.html?search=> 

### Running with `rustc`

`rustc` is the basic rust compiler.  It is not recommended to use this for building projects, 
but it is useful for testing code.

`rustc filename.rs` will create the .exe file.  
You can then run the `./filename.exe` file, or you can run a command in the terminal. 
On Windows, when the file is compiled, there is a .pdb file created which is debugging information.  

<hr>

[TOC](#table-of-contents) 
## 3. Rust Language Basics

#### Data Types
Rust is a statically typed language, which means that it must know the types of all variables at compile time.


#### Variables
Variables are created by using the let keyword: `let x = 5;`
Variables are immutable by default.  To make a variable mutable, use the mut keyword: `let mut x = 5;`


#### References
Using a reference: `read_line(&mut x)`. The `&` before a variable name indicates that it’s a reference. 
This gives you a way to access one piece of data without needing to copy that data into memory multiple times. 
References are also immutable by default. 

#### Shadowing
Overwriting a variable instead of making a new one.

#### Scope
Scope is the area of code where a variable is valid.  This is usually expressed within curly braces:
```rust
fn main() {
    // Outer scope
    let outer_variable = 5; // x is valid from here until the end of the function
    {
        // Inner scope
        let inner_variable = 6; // y is valid from here until the end of the inner scope
        println!("inner_variable: {}", inner_variable);
        
        // You can access outer variables from inner scopes
        println!("outer_variable: {}", outer_variable);
    }
    
    // inner_variable is not valid here
    // we can still access outer_variable here
    println!("outer_variable: {}", outer_variable);
}
```

#### Statements & Expressions
* **Statements** are instructions that perform some action and do not return a value.
	* Functions
	* Setting variables
	* Loops
	* Note: statements in rust must end with a semicolon. 
* **Expressions** evaluate to a resultant value. Expressions do not end in semicolon.
	* 5
	* 10+6
	* Calling a function that returns
	* Calling a macro
	* If expressions
	* Note: expressions can be part of statements (let x = 6; 6 is an expression)
	* Note: expressions that are followed by a semicolon are treated as statements

#### Functions

```rust
fn main() {
    println!("Hello, world!");
}
```
Functions are a central design of rust. The main function will always be the first code to run.

#### Returning from Function
```rust
fn repeat_value(i: i32) -> i32 {
    i
}
```
To return a value from a function, you need to have the arrow and return type. 
The last [expression](#statements--expressions) will be what a function returns if it has a return type.
You can also return early with the return keyword
```rust
fn repeat_value(i: i32) -> i32 {
    if i > 5 {
        return i+5;
    }
    i
}
```

If a function will not return anything you can exclude the arrow and return type.
```rust
fn just_print(i: i32) {
    println!("i: {}", i);
}
```

#### If Expressions

Standard if expressions, pretty similar to python.  Be sure to put the contents in the curly braces.
```rust
fn if_control(i: i32) -> i32 {
    if i > 5 {
        i+5
    } else if x < 0 {
        i-5
    }
    else {
        i
    }
}
```

If statements can also be used on the right side of a variable. 
This is because if statements evaluate to a value and are actually called **[if expressions](#statements--expressions)**

```rust
fn main() {
    let condition = true;
    let number = if condition {
        5
    } else {
        6
    };
    println!("The value of number is: {}", number);
}
```

#### Loops
There are 3 kinds of loops:
1. Loop
   1. Infinite loop - continuously execute something until a condition is met and then either use the continue or break keyword.
   ```rust
   fn main() {
       loop {
           println!("again!");
       }
   ```
    You can return values from a loop by using break.
    ```rust
    fn main() {
        let mut counter = 0;
        let result = loop {
            counter += 1;
            if counter == 10 {
                break counter * 2;
            }
        };
        println!("The result is {}", result);
    }
    ```

   **Loop labels** help with control flow of multiple loops. The default for break is to break the inner most loop. You can
   use loop labels to break out of higher loops within inner loops
    ```rust
    fn main() {
        let mut count = 0;
        let remaining = 10;
        'outer_loop: loop {
            println!("Count = {}", count);
            loop {
                count += 1;
                println!("Loop {} of {}", count, remaining);
                if count == remaining {
                    break;
                } else if count == 100 {
                    break 'outer_loop;
                }
            }
        }
        println!("End of program");
    }
    ```
   
2. While - Good for looping until a condition is met.
    ```rust
    fn main() {
        let mut number = 3;
        while number != 0 {
            println!("{}!", number);
            number -= 1;
        }
        println!("LIFTOFF!!!");
    }
    ```
    
3. For - Good for looping through a collection
    ```rust
    fn main() {
        let a = [10, 20, 30, 40, 50];
        for element in a.iter() {
            println!("the value is: {}", element);
        }
    }
    ```

### Comments 
`//` just like javascript


<hr>

[TOC](#table-of-contents)
## 4. Ownership