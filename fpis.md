# Functional Programming in Scala (by Paul Chiusano and Rúnar Bjarnason)

## Chapter 1

### Referential transparency

An expression _e_ is referentially transparent if, for all programs _p_, all occurrences of _e_ in _p_ can be replaced by the result of evaluating _e_ without affecting the meaning of _p_.

### Function purity

A function _f_ is pure if the expression _f(x)_ is referentially transparent for all referentially transparent _x_.

### Substitution model

#### Referential transparent example

```scala
val x = "Hello, World"
val r1 = x.reverse
val r2 = x.reverse
```

#### Not Referential transparent example

```scala
val x = new StringBuilder("Hello")
val y = x.append(", World")
val r1 = y.toString
val r2 = y.toString
```

## Chapter 2

### _Object_ keyword

The object keyword creates a new singleton type, which is like a class that only has a sin- gle named instance. If you’re familiar with Java, declaring an object in Scala is a lot like cre- ating a new instance of an anonymous class. Scala has no equivalent to Java’s static keyword, and an object is often used in Scala where you might use a class with static members in Java.

### _Main_ method

```scala
def main(args: Array[String]): Unit = println("Hello world")
```

### _Unit_ type

_Unit_ serves a similar purpose to _void_ in programming languages like C and Java. In Scala, every method has to return some value as long as it doesn’t crash or hang. There’s only one value of this type and the literal syntax for it is `()`.

### Tail calls

A call is said to be in tail position if the caller does nothing other than return the value of the recursive call. If all recursive calls made by a function are in tail position, Scala automatically compiles the recursion to iterative loops that don’t consume call stack frames for each iteration. By default, Scala doesn’t tell us if tail call elimination was successful, but if we’re expecting this to occur for a recursive function we write, we can tell the Scala compiler about this assumption using the `@tailrec` annotation.

### Higher-order function

Function that accepts other functions as parameter.

#### Example

```scala
def formatResult(name: String, n: Int, f: Int => Int) = { 
  val msg = "The %s of %d is %d."
  msg.format(name, n, f(n))
}
```

### Monomorphic and polymorphic functions

* _monomorphic_ - function that works only on one type of data
* _polymorphic_ - function that works on any type it is given

### Anonymous functions

When we define a function literal, what is actually being defined in Scala is an object with a method called apply. Scala has a special rule for this method name, so that objects that have an apply method can be called as if they were themselves methods. When we define a function literal like `(a, b) => a < b`, this is really syntactic sugar for object creation:

```scala
val lessThan = new Function2[Int, Int, Boolean] { 
  def apply(a: Int, b: Int) = a < b 
}
```

_lessThan_ has type `Function2[Int,Int,Boolean]`, which is usually written `(Int,Int) => Boolean`. Note that the _Function2_ interface (known in Scala as a trait) has an _apply_ method. And when we call the _lessThan_ function with `lessThan(10, 20)`, it’s really syntactic sugar for calling its apply method `lessThan.apply(10, 20)`.

_Function2_ is just an ordinary trait (an interface) provided by the standard Scala library to represent function objects that take two arguments. Also provided are _Function1_, _Function3_, and others, taking a number of arguments indicated by the name. Because functions are just ordinary Scala objects, we say that they’re first-class values.

### Currying

Translating the evaluation of a function that takes multiple arguments (or a tuple of arguments) into evaluating a sequence of functions, each with a single argument. Oposit operation is called uncurrying.

```scala
def curry[A,B,C](f: (A, B) => C): A => (B => C)
def uncurry[A,B,C](f: A => B => C): (A, B) => C
```

## Chapter 3

Functional data structures are by definition immutable !

### Trait

An abstract interface that may optionally contains implementations of some methods.

### Sealed trait

All implementations of the trait must be declared in this file.

### Case classes \ objects

Regular classes which export their constructor parameters and which provide a recursive decomposition mechanism via pattern matching. Example:

```scala
sealed trait List[+A]
case object Nil extends List[Nothing]
case class Cons[+A](head: A, tail: List[A]) extends List[A]
```

### Variance

In the declaration trait `List[+A]`, the _+_ in front of the type parameter _A_ is a variance annotation that signals that _A_ is a covariant or “positive” parameter of List. This means that, for instance, `List[Dog]` is considered a subtype of `List[Animal]`, assuming `Dog` is a subtype of `Animal`. More generally, for all types _X_ and _Y_, if _X_ is a subtype of _Y_, then _List[X]_ is a subtype of _List[Y]_.

### _Nothing_ type

Subtype of all types.

### Pattern matching

Pattern matching works a bit like a fancy switch statement that may descend into the structure of the expression it examines and extract subexpressions of that structure. It’s introduced with an expression (the target or scrutinee) like _ds_, followed by the keyword _match_, and a {}-wrapped sequence of cases. Each _case_ in the match consists of a pattern (like `Cons(x,xs)`) to the left of the _=>_ and a result (like `x * product(xs)`) to the right of the _=>_. If the target matches the pattern in a case, the result of that case becomes the result of the entire match expression. If multiple pat- terns match the target, Scala chooses the first matching case. Example:

```scala
def product(ds: List[Double]): Double = ds match { 
  case Nil => 1.0
  case Cons(0.0, _) => 0.0
  case Cons(x,xs) => x * product(xs)
}
```

### Companion objects

We’ll often declare a companion object in addition to our data type and its data constructors. This is just an object with the same name as the data type where we put various convenience functions for creating or working with values of the data type.

If, for instance, we wanted a function `def fill[A](n: Int, a: A): List[A]` that created a List with n copies of the element _a_, the List companion object would be a good place for it. Companion objects are more of a convention in Scala. We could have called this module _Foo_ if we wanted, but calling it _List_ makes it clear that the module contains functions relevant to working with lists.

### Variadic functions

The function apply in the object List is a variadic function, meaning it accepts zero or more arguments of type _A_:

```scala
def apply[A](as: A*): List[A] =
  if (as.isEmpty) Nil
  else Cons(as.head, apply(as.tail: _*))
```

For data types, it’s a common idiom to have a variadic apply method in the companion object to conveniently construct instances of the data type. By calling this function apply and placing it in the companion object, we can invoke it with syntax like `List(1,2,3,4)` or `List("hi","bye")`, with as many values as we want separated by commas.

### Type inference

When we have the following function:

```scala
def dropWhile[A](l: List[A], f: A => Boolean): List[A]
```

we could call it in the following way:

```scala
dropWhile(xs, (x: Int) => x < 4)
```

If we want to skip typing variable in anonymous function we need to curry the function:

```scala
def dropWhile[A](l: List[A]) (f: A => Boolean): List[A]
```

This is an unfortunate restriction of the Scala compiler; other functional languages like Haskell and OCaml provide complete inference, meaning type annotations are almost never required.

### Underscore notation for anonymous functions

The anonymous function `(x,y) => x + y` can be written as `_ + _` in situations where the types of _x_ and _y_ could be inferred by Scala. This is a useful shorthand in cases where the function parameters are mentioned just once in the body of the function. Each underscore in an anonymous function expression like `_ + _` introduces a new (unnamed) function parameter and references it. Arguments are introduced in left-to-right order. Example:

```scala
_ + _
_ * 2
_.head
```

### Algebraic data type

List is just one example of what’s called an _algebraic data type_ (ADT). An ADT is just a data type defined by one or more data constructors, each of which may contain zero or more arguments. We say that the data type is the sum or union of its data constructors, and each data constructor is the product of its arguments, hence the name algebraic data type.

One might object that algebraic data types violate encapsulation by making public the internal representation of a type. In FP, we approach concerns about encapsulation differently — we don’t typically have delicate mutable state which could lead to bugs or violation of invariants if exposed publicly. Exposing the data constructors of a type is often fine, and the decision to do so is approached much like any other decision about what the public API of a data type should be.

### Tuple

Pairs and tuples of other arities are also algebraic data types. They work just like the ADTs we’ve been writing here, but have special syntax:

```scala
val p = ("Bob", 42)
val x = p._1
val y = p._2
val z = p match { case (a,b) => b }
```

In this example, `("Bob", 42)` is a pair whose type is `(String,Int)`, which is syntactic sugar for `Tuple2[String,Int]`. We can extract the first or second element of this pair (a _Tuple3_ will have a method _3, and so on), and we can pattern match on this pair much like any other case class. Higher arity tuples work similarly.

## Chapter 4

### _Option_ vs _Either_

* `Option` is sufficient when knowing whether a failure occurred is all we care about
* `Either` data type represents, in a very general way, values that can be one of two things. When we use it to indicate success or failure, by convention the Right constructor is reserved for the success case and Left is used for failure.

### Exceptions as a way of handling errors

* exceptions break referential transparency and introduce context dependence

```scala
val y: Int = throw new Exception("fail!")
```

* exceptions are not type-safe
* exceptions allow us to consolidate and centralize error-handling logic

### Returning bogus value as a way of handling errors

* allows errors to silently propagate — the caller can forget to check this condition and won’t be alerted by the compiler, which might result in subsequent code not working properly
* it results in a fair amount of boilerplate code at call sites, with explicit if statements to check whether the caller has received a “real” result (this boilerplate is magnified if you happen to be calling several functions, each of which uses error codes that must be checked and aggregated in some way)
* for some output types, we might not even have a sentinel value of that type even if we wanted to

### Partial functions

Function that is not defined for some inputs. A function is typically partial because it makes some assumptions about its inputs that aren’t implied by the input types.
Providing default value as an additional parameter or changing return type into _Option_ turns function into total one.

### Lifting

```scala
def lift[A,B](f: A => B): Option[A] => Option[B] = _ map f
```

_Scalaz_ provide functions for lifting two and more argument functions.

### For-comprehensions

```scala
def map2[A,B,C](a: Option[A], b: Option[B])(f: (A, B) => C): Option[C] =
  a flatMap (aa =>
  b map (bb =>
  f(aa, bb)))
```

Exactly the same code with for-comprehension:

```scala
def map2[A,B,C](a: Option[A], b: Option[B])(f: (A, B) => C): Option[C] =
  for {
    aa <- a
    bb <- b
  } yield f(aa, bb)
```

A for-comprehension consists of a sequence of bindings, like aa <- a, followed by a yield after the closing brace, where the yield may make use of any of the values on the left side of any previous <- binding. The compiler desugars the bindings to flatMap calls, with the final binding and yield being converted to a call to map.

## Chapter 5

### Lazy evaluation of arguments

```scala
def maybeTwice(b: Boolean, i: => Int) = if (b) i+i else 0
```

Worth noting that `i` will be evaluated two times. If you want to have it being evaluated only once you need to assign it to `lazy` variable.

```scala
def maybeTwice2(b: Boolean, i: => Int) = {
  lazy val j = i
  if (b) j+j else 0
}
```
