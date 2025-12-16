# kubica (WIP)
when functional abstraction meets bare metal
## Introduction
kubica is a programming language that aims expressive high abstraction without eliminates the system low-leveled needs. Highly inspired by Rust, Zig, Scala, and Haskell.
## Overview
### Hello World (WIP)
```kubica
import kubica.IO
@main val main() =
  IO.println("Hello World")
```
### Fields Definition (WIP)
- Immutable
  ```kubica
  # immutable
  val p = 2         # Int
  val q = 3.2       # Float
  val r = 4f        # Float
  val s: Char = 'a'

  p = 10            # error : reassigning immutable
  ```
- Mutable
  ```kubica
  var a = 10
  IO.println(a)     # 10

  a = 3
  IO.println(a)     # 3

  a = 'A'           # error : assigning Int fields with Char
  ```
### Primitives and Operations (WIP)
- Definition
  ```kubica
  val a = 3         # Int (default: Int32)
  val b = 4u        # UInt (default: UInt32)
  val c = 9f        # Float (default: Float32)
  val d = '_'       # Char (defaul: UTF8)
  val e = true      # Bool
  ```
- Arithmetic Operation
  ```kubica
  var x = a + 4     # 7
  x -= 10           # -3
  var y = c/5       # 1.8
  y *= x            # -5.4 (x promoted)
  ```
- Logical Operation
  ```kubica
  val p = x < y             # true
  val q = !(x > y)          # true
  var r = p && !q           # false
  r = !p || r               # false
  val s = r ^^ q            # true
  ```
- Bitwise Operation
  ```kubica
  x = x << 2
  x = x >> 3
  x = a >>> x
  x = a ^ d
  x = d & a
  x = d | x
  ```

### Pseudotypes (WIP)
+ Functions

  Function is treated same as field.
  ```kubica
  val f = (x: Int) -> x+4   # Int -> Int
  # shorthand
  val g(x: Int) = x+5       # Int -> Int
  ```

  As long its treated like field, it can defined mutable.
  ```kubica
  var u(a: Float) = a/10      # Float -> Float
  u = (b: Float) -> b*5       # Float -> Float (same function pattern)
  u = f
  # error : type mismatch, Float -> Float with Int -> Int
  ```

  Calling function isn't same with getting function
  ```kubica
  val returnThree() = 3
  val x = returnThree       # x : () -> Int
  val y = returnThree()     # y : Int
  ```
+ Tuple

  Tuple is just a set of fields with type strictness.
  ```kubica
  # (String,Int,Char,Float)
  val person = ("Kubuni",67,'A',3.14)
  IO.println(person(0))     # Kubuni
  IO.println(person(1))     # 67
  IO.println(person(5))     # error : there is just 4 value
  ```
  Tuple with function :
  ```kubica
  val tupF = (90,30.5,(a:Int,b:Int)->(a+b)*2)
  # > tupF : (Int,Float,(Int,Int)->Int)

  # function passing
  val printer(name:String,sixseven:Int,grade:Char,gpa:Float) =
    IO.println(s"My name is $name")
    IO.println(s"My favorite number is $sixseven")
    IO.println(s"But my GPA is $gpa with $grade grade")
  printer(person)
  ```
+ Address

  Like C pointers, address refer to a memory location of a field.
  ```kubica
  var x = 10

  # get the x address
  val pX = &x             # pX : &~Int (refer to mutable)
  *pX = 50                # deref and assign

  IO.println(x)           # 50

  val y = 10
  val pY = &y             # pY : &Int (refer to immutable)

  *pY = 20                # error : reassigning immutable field
  ```

  Mutable address
  ```kubica
  var z = -5

  var mutableP = &z
  *mutableP = 40          # z = 40

  mutableP = &x
  *mutableP = 35          # x = 35

  mutableP = &y           # error : type mismatch (&~Int with &Int)
  ```
+ Array
+ Slice
