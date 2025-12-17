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
Pseudotypes is an approach designed for type strictcity purpose on a low-leveled access
+ Functions (`A -> B`, `A ~> B`)

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
+ Tuple (`(A,B,C,D)`)

  Tuple is just a set of fields with type strictness.
  ```kubica
  # (String,Int,Char,Float)
  val person = ("Kubuni",67,'A',3.14)
  IO.println(person(0))     # Kubuni
  IO.println(person(1))     # 67
  IO.println(person(5))     # error : there is just 4 value
  ```
  Function passing :
  ```kubica
  val printer(name:String,sixseven:Int,grade:Char,gpa:Float) =
    IO.println(s"My name is $name")
    IO.println(s"My favorite number is $sixseven")
    IO.println(s"But my GPA is $gpa with $grade grade")
  printer(person)
  ```
+ Address (`&T`)

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
+ Array (`{T}n`)

  Array is a fixed set of homogenous element with a size strictness.
  ```kubica
  val ar = {3,4,5}                # ar : {Int}3
  IO.println(ar(0) + ar(2))       # 8

  ar(1) = 10      # error : mutating immutable array block

  # generate instant array
  val arGen = kubica.Util.array.generate(10, 0)
  # {0,0,0,0,0,0,0,0,0,0}
  ```

  Multiple indexer can also be applied natively.
  ```kubica
  val ar2 = ar(0,2,1)             # ar2 : {Int}3 = {3,5,4}
  IO.println(ar2(1,1,0,2))        # {5,5,3,4}
  ```

  Slicing with multiple indexer :
  ```kubica
  IO.println({3,2,4,1,6,3,5,7}(4..6,1..3))
  # {6,3,5,2,4,1}
  ```

  Array are operative.
  ```kubica
  val ar3 = ar :: ar2             # ar3 : {Int}6 = {3,4,5,3,5,4}
  ```

  There are cool placeholders too. \
  `n_` for single placeholder, `...` for multiple placeholder (only can used once).
  ```kubica
  val ar4 = ar3{1,..,6} :: {10}   # ar4 : {Int}7 = {1,4,5,3,5,6,10}

  val ar5 = ar4{4,..,8,2_}        # ar5 : {Int}7 = {4,4,5,3,8,6,10}

  val x = 4
  val ar6 = ar5{x_, 3}            # ar6 : {Int}5 = {4,4,5,3,3}

  val ar7 = ar5{..,10,..}         # error : ambigous indexer
  ```

  Assigning mutable array with slicing indexer and placeholders:
  ```kubica
  var mutAr = {6,4,2,4,5,6}

  mutAr(0..3) = {4,3,2,1}         # {4,3,2,1,5,6}
  mutAr = mutAr{_,50,...,1}       # {4,50,2,1,5,1}

  mutAr = {1,2,3}     # error : type mismatch ({Int}6 with {Int}3)
  ```
+ Slice (`T..`)

  While array is size-strict, slice doesn't aware about its size. It representated as value of blocks address. So, it has a fixed size.
  ```kubica
  var elems = [1,3,2,4,5,6]
  IO.println(size(elems))         # 6

  elems = [3,2,1,3,4,6,8]         # refer to a new blocks, not replace it
  IO.println(size(elems))         # 7

  elems = elems(3..1)             # [3,1,2]
  ```

  Unefficient way to define a dynamic array with slice :
  ```kubica
  # slice :: array = slice
  elems = elems :: {30}           # [3,1,2,30]

  # slice :: slice = slice
  elems = [40,10] :: elems(2,3)   # [40,10,2,30]
  ```

  However, it has fixed size. Accessing index greater than size can leads runtime error (not compile error).

  Better way to use slice is on function/tuple/custom type passing.
  ```kubica
  # Array is very safe so it can't do:
  val f(s: {Int}10) = s(5)      # {Int}10 -> Int
  val ar = {3,4,2,1}
  f(ar)         # type mismatch : {Int}4 pass {Int}10

  # Meanwhile slice:
  val g(s: Int..) =
    if (5 > size(s)) s(size(s)) # access last element
    else s(4)

  val sl = [4,3,1,7]
  g(sl)                         # 7
  g(sl::[5,4,2])                # 5
  ```
