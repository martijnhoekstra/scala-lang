---
layout: page
title: A Scala Tutorial for C# Programmers

disqus: true
by: Ivan Towlson
---

## Introduction

Scala is a hybrid of functional and object-oriented languages.
Its functional aspects make it very expressive when writing algorithmic
code, and play nicely with the brave new world of concurrency; its
object-oriented aspects keep it familiar and convenient when creating
business objects or other stateful models.

Scala shares many features and concepts with C#, as well as introducing
many that are new.  In fact, some of the capabilities that people talk
about a lot in Scala introductions or Java-to-Scala guides are very
familiar to C# programmers.  For example, as a C# programmer, you don't
need to be gently walked through the idea of function types – you've
been using delegates and lambdas for years.

However, some Scala features and behaviours are quite different from C#,
and even those which are common usually have different syntax or work
in slightly different ways.  So let's start out by covering some Scala
basics from a C# programmer's point of view.

## Key facts

- Classes and their members are public by default, there is no `public` keyword.
- The concept of statics has been replaced by a more object-oriented approach.
- The type is always written after the identifier, seperated by a `:`.
- Scala's stylistic guidelines differ from those of C#. The *Scala Style Guide*
  provides an comprehensive overview of the encouraged style. Some examples:
  - Semicola are generally not required and left out.
  - Types always start with an uppercase letter.
  - Methods, values and variables start with a lowercase letter.
  - The opening curly brace is not placed on a new line.

## Literals

Most literals look and work the same way in Scala and C#. There are a few differences though:

- C#'s verbatim string literals `@"one \ two \ three"` are written as `"""one \ two \ three"""` in Scala.

- C# 6 added string interpolation to the language: `$"Hello {name}"`.

  Scala is more flexible: Any method defined on `StringContext` can be used for string interpolation and can
  provide its own semantics.

  The following string interpolators are provided by default:

  - **`s`** – Similar to C#'s interpolation, `s` converts its arguments to strings: `s"Hello $name"`
  - **`f`** – Provides formatting capabilities of `String.format`, but checked at compile-time:
    `f"$PI%.4f"` formats the floating point value `PI`, rounding it to four decimal digits after the decimal dot.

- Scala does not have special syntax for arrays.

  Arrays are created with `Array("Hello", "world!")`.

  Array elements are accessed with `arr(1)` and updated with `arr(1) = "planet!"`.

  All of these constructs are defined in the standard library, not the language itself.

## Classes

The basic concept of classes is the same in Scala as in C#.  A class
can contain state (member fields) and hides it behind an
interface (member methods).  The syntax for declaring classes is just
like C#:

    class Widget { } // The empty curly braces are optional in Scala

## Values and Variables

The syntax for declaring a class with two members, a public value and
a private variable, looks like this:

    class Widget {
      val serialNumber = 123
      private var usageCount = 0
    }

Scala automatically infers the type of the field from its initial
value, just like C# with the `var` keyword.  (Don't be fooled by the appearance
of the second field though -- the Scala `var` keyword doesn't mean the
same thing as the C# `var` keyword.)

Now, why is one of these fields introduced as `val` and the other as
`var`?  In C#, fields are mutable by default.  That is, by default,
any code that can access a field can modify it.  You can specify *readonly*
to make a field immutable, so that it can't be changed once the object
has been constructed.

Scala generally tries to nudge you into prefering immutability wherever mutability
is not required, but is not dogmatic about it.  `val` means
a field is immutable (readonly in C#) and `var` means it's mutable
(a normal field in C#).

So the class above is equivalent to the C#:

    class Widget {
      public readonly int SerialNumber = 123;
      private int UsageCount = 0;
    }

Notice that C# makes you write extra code to make things immutable,
and Scala doesn't.  This may seem like a small thing, but it's
going to be a really important theme.

## Methods

### Declaration

The syntax for declaring methods is like this:

    class Widget {
      def reticulate(winding: Int): String = "some value"
    }

This is a more dramatic departure from C# so let's pick it apart.

The `def` keyword means we're declaring a method rather than a value
(`val`) or a variable (`var`).

Method arguments go in brackets after the method name, just as in C#.
Unlike C#, arguments are immutable in Scala; they cannot be re-assigned
in the method body.
However, the way Scala specifies the argument types is different from C#.
In C# you would write `int winding`; in Scala you write `winding: Int`.

This is a general principle: in Scala, when you want to specify the
type of something, you write the type after the something, separated
by a colon.  (Whereas in C# you write the type before the something,
separated by a space.)

You can see the same principle in action for the return type of the
function. `reticulate` returns a `String`.

Finally, the body of the method has been placed after an equals sign,
rather than inside braces. (Braces are only necessary when the method
body consists of multiple expressions/statements.) What's more,
the body of the method is an expression -- that
is, something with a value -- rather than a set of statements amongst which
is a `return`.

A translation of the above example to C# could look like this:

    class Widget {
      public string Reticulate(int winding) {
        return "some value";
      }
    }

### Implementation

I showed a method above, but only to illustrate the declaration syntax.
Let's take a look at a slightly more substantial method.

    def power4(x: Int): Int = {
      var square = x * x  // usually wouldn't write this - see below
      square * square
    }

This looks pretty similar to C#, except that we don't need a return
statement. The method body consists of an expression, `square * square`,
with some preliminary bits to define the components of the expression.
The return value of the method is the value of the expression.

In order to make this method look like C#, I used the `var` keyword
to declare the local variable `square`.  But as with fields, the
Scala `var` keyword doesn't work quite the same as the C# `var` keyword:

In C#, `var` means "work out the type of the variable from the
right hand side".  But Scala does that automatically -- you don't
need to ask for it.  In Scala, `var` means "allow me to change this
variable (or field) after initialisation".  As with fields, you can
also use `val` to mean 'don't allow me to change this variable
after initialisation.'  And in fact since we don't need to change
`square` after initialisation, we'd be better off using val:

    def power4(x: Int): Int = {
      val square = x * x  // val not var
      square * square
    }

Incidentally, if you do ever want to explicitly indicate the type
of a variable, you can do it the same way as with function arguments:

    def power4(x: Int): Int = {
      val square: Int = x * x
      square * square
    }

Notice that you still need `val` or `var`.  Telling Scala the type
of the variable is independent of deciding whether the variable should
be mutable or immutable.

### Default and Named Parameters

C++, Visual Basic and C# optional parameters have fixed defaults specified
by the called function. For example, if you have a method like this:

    public void Fie(int a, int b = 123) { … }

and you call `Fie(456)`, it’s always going to be equivalent to calling
`Fie(456, 123)`.

Scala provides named parameters and default arguments:

    def fie(a: Int, b: Int = 123) = ...
    fie(456) // or fie(a = 456)

Unlike C#, Scala stores default values at the definition of the method, not its usage.
This means that callers of a method don't need to be recompiled when a default is changed.

### Naming and Calling Methods

C#, like most object-oriented programming languages, is pretty strict about
how you call methods: you use the dot notation, unless the method is a
special ‘operator’ method such as `operator+`, `operator==` or a conversion
operator. The special operator methods are predefined by the compiler: you
can write your own implementation, but you can’t create your own operator
names.  You can teach the `+` operator how to handle your custom type, but
you can’t add an exponentiation operator:

    int a = b ** c;

C# has three problems with this: first, it doesn’t like the method name
`**`; second, it doesn’t like that there’s no `.` before the name; and
third, it doesn’t like that there’s no brackets around the method argument.

To get around the objection to the name, let’s compromise and call it
`ToThe` for now. So what C# insists on seeing is `a.ToThe(b)`.

Scala, like many functional languages, isn’t so strict. Scala allows you
to use any method with a single argument in an infix position. Before we
can see this in the exponentiation example, we will enrich the `Double`
type with the `toThe` method as we learned earlier:

    implicit class RicherDouble(d: Double) {
      def toThe(exp: Double): Double = math.pow(d, exp)
    }

Recall that this is just the Scala idiom for extension methods – it’s the
equivalent of writing
`public static ToThe(this double first, double second) { … }` in C#.
(If we were wanting to use infix notation with our own class, we wouldn’t
need all this malarkey.) So now we can write:

    val raised = 2.0.toThe(7.0)

Okay, so what do we need to do to get this to work in infix position?
Nothing, it turns out.

    val raised = 2.0 toThe 8.0 // It just works

This still doesn’t look much like a built-in operator, but it turns out
Scala is less fussy than C# about method names too.

    implicit class RicherDouble(d: Double) {
      def toThe(exp: Double): Double = math.pow(d, exp)
      def **(exp: Double): Double    = toThe(exp)
    }

    val raised = 2.0 ** 9.0 // It still just works

Much nicer.

This sorta-kinda works for postfix operators too:

    class RicherString(s: String) {
      def twice: String = s + s
    }

    val drivel = "bibble" twice

Calling methods in infix and postfix nodadion is obviously fairly simple
syntactic sugar over normal dot notation. But this seemingly minor feature
is very important in constructing DSLs, allowing Scala to do in internal
DSLs what many languages can do only using external tools. For example,
where most languages do parsing via an external file format and a tool to
translate that file format into native code (a la `lex` and `yacc`),
Scala’s parser library makes extensive use of infix and postfix methods to
provide a “traditional” syntax for describing a parser, but manages it
entirely within the Scala language.

## Structs

In C#, when you define a type, you can decide whether to make it a
reference type (a class) or a value type (a struct). Unlike .NET,
the `Java Virtual Machine` on which Scala runs doesn't really have the
concept of value types.  Scala tries to disguise this as best it can
and offers value classes which have some, but not all the benefits of
structs.

Value classes are classes which extend the trait `AnyVal` and have
exactly one public `val` parameter:

    class Money(val value: BigDecimal) extends AnyVal {
      ...
    }

The compiler will try to avoid any unnecessary allocation of `Money`
instances and will generate code to work directly on the unwrapped
`BigDecimal` value.

## Base Types

Scala's base types are pretty much the same as C#'s, except that they
are named with initial capitals, e.g. `Int` instead of `int`. (In fact
every type in Scala starts with an uppercase letter.) There are
no unsigned variants, and booleans are called `Boolean` instead of `bool`.

Scala's name for `void` is `Unit`, but unlike `void`, `Unit` is a real type.
We'll see why this is important in a moment.

## Function Types

In C#, you can have variables that refer to functions instead of data.
These variables have delegate types, such as `Predicate<T>` or
`Func<T, TResult>` or `KeyEventHandler` or `Action<T1, T2>`.

Scala has the same concept and ships with classes like `Function0[R]`,
`Function1[T1, R]`, ... in its library.
Unlike C#, the language natively supports them, so that function types
can be spelled as `(T1, T2) => R` instead of `Function2[T1, T2, R]`.
For example, a predicate of integers would be of type `(Int) => Boolean`.
If there is only one input type, the parens can be left out like this:
`Int => Boolean`.

Effectively, Scala gets rid of all those weird custom delegate types
like `Predicate<T>` and `Comparer<T>` and has a single family of
function types like the C# `Func<...>` family.

Refering to methods that don't have a return value in C# is not
possible with `Func<T, void>`, because void isn't a valid type.
It is necessary to write `Action<T>` instead. Scala doesn't have
different families of delegate types, it just has the built-in
function types.  Fortunately, in Scala, `Unit` is a real type, so you
can write `(Int) => Unit` for a function which takes an integer
and doesn't return a useful value.  This means you can pass `void` methods
interchangeably with non-`void` methods, which is a Good Thing.

## Tuples

Everybody hates out parameters.  We all know that code like this just
isn't nice:

    Widget widget;
    if (widgetDictionary.TryGetValue(widgetKey, out widget))
    {
      widget.ReticulateSplines();
    }

And once you start composing higher-level functions into the mix, it gets
positively nasty. Suppose I want to make a HTTP request.  Well, that's
going to produce two outputs in itself, the success code and the response
data. But now suppose I want to time it. Writing the timing code isn't a
problem, but now I have *three* outputs.

You can get around this in specific situations by creating custom types
like `DictionaryLookupResult` or `TimedHttpRequestResult`, but eventually
the terrible allure wears off, and you just want it to work.

Enter tuples. A tuple is just a small number of values -- a single value,
a pair of values, a triplet of values, that sort of thing.
Using tuples, our timed HTTP request might look like this:

    public Tuple<bool, Stream, TimeSpan> Time(Func<Tuple<bool, Stream>> action)
    {
      StartTimer();
      var result = action();
      TimeSpan howLong = StopTimer();
      return Tuple.Create(result.First, result.Second, howLong);
    }

    var result = Time(() => MakeRequest(uri));
    var succeeded = result.First;
    var response = result.Second;
    var howLong = result.Third.
    Console.WriteLine("it took " + howLong);

The reason this keeps getting verbose on us is that C# doesn’t provide any
syntatical support for tuples. To C#, a `Tuple<>` is just another generic
type.

Really, what we're really trying to articulate by returning a `Tuple<>` is,
"this method has several outputs." So what do we want to do with those
outputs?  We want to access them, for example to stash them in variables,
without having to construct and pick apart the tuple one value at a time.
That means the language has to provide some kind of syntax-level support
for tuples, instead of treating them like every other class the compiler
doesn’t know about.

Many functional languages have exactly this kind of syntactical support,
and Scala is no exception.  Here’s how the above pseudo-C# looks in Scala:

    def time(action: => (Boolean, Stream)): (Boolean, Stream, TimeSpan) = {
      startTimer()
      val (succeeded, response) = action
      (succeeded, response, stopTimer())
    }

    val (succeeded, response, timeTaken) = time(makeRequest)
    Console.WriteLine("it took " + timeTaken)

Notice the multiple variables on the left hand side of the time call?
Notice the `(Boolean, Stream, TimeSpan)` return type of the time method?
That return value is effectively a tuple type, but instead of having to
capture the returned tuple in a `Tuple<>` variable and extract its various
bits by hand, we are getting the Scala compiler to (in the time function)
compose the tuple implicitly for us, without us having to write the
constructor call, and (in the calling code) unpick the tuple into
meaningful, named variables for us without us having to explicitly copy
the values one by one and by name.

(A proper implementation of the `time()` method wouldn’t be restricted to
`(Boolean, Stream)` results, of course)

How would this play with the dictionary example?

    val (found, widget) = widgetDictionary.getValue(key)
    if (found)
      widget.reticulateSplines()

We don’t actually save any lines of code, but the code has become a bit more
obvious: All the outputs are on the left of the equals sign, instead
of being spread between the assignment result and the parameter list, and
we don’t have that odd Widget declaration at the top.

Although tuples are easy to define and straight-forward to use,
it is generally recommended to prefer classes to tuples as soon as a tuple
would appear in mutiple places of the code.

*Scala's primary platform is the Java virtual machine, and some of the
interest in Scala comes from Java programmers' interest in features such
as type inference, comprehensions and lambdas, with which C# programmers
are already familiar. So what's left that might be of interest
specifically to C# programmers?*

## Mixins and Traits

### Motivation

Interfaces in C# and Java play a dual role.
First, they are a set of capabilities that an implementer has to, well,
implement. Second, they are a feature set that a client can use.

These two roles can be conflicting: The first means that interfaces want
to be minimal, so that implementers don't have to implement a whole lot
of superfluous and redundant guff. The second means that interfaces want
to be maximal, so that clients don't have to clog themselves up with
boilerplate utility methods.

Consider, for example, `IEnumerable` (and its sister interface `IEnumerator`).
This is a very minimal interface: implementers just need to be able to
produce values in sequence. But this minimalism means that clients of
`IEnumerable` need to write the same old boilerplate again and again and
again: foreach loops to filter, foreach loops to call a method on each
element of the sequence, foreach loops to aggregate, foreach loops to
check whether all elements meet a criterion, or to find the first member
that meets a criterion, or...

This is frustrating because the implementations of "filter," "apply",
"aggregate," and so on are always the same. Of course, we could put
these methods into concrete types (`List<T>` includes several), but then
those concrete types will contain duplicate code, and users who only have
an `IEnumerable` will still miss out.  And yet we can't put these methods
into the interface because then every implementer of `IEnumerable` would
have to implement them -- and they'd end up writing the same boilerplate,
just now in all the zillions of `IEnumerable` classes instead of their clients.

### The C# and Scala Solutions

We could resolve this tension if we had a way for interfaces to contain
implementation: for example, if `IEnumerable` required the implementer
to provide the class-specific iteration functionality, but then provided
the standard implementations of "filter," "apply", "aggregate" and so on
automatically:

    public pseudo_interface IEnumerable
    {
      IEnumerator GetEnumerator();  // must be implemented
      IEnumerable Filter(Predicate predicate)  // comes for free
      {
        foreach (object o in this)
         if (predicate(o))
        yield return o;
      }
    }

C# 3 addresses this using extension methods: the methods mentioned above
are all in fact included as extension methods on `IEnumerable<T>` as
part of LINQ.

This has some advantages over the approach described above: specifically,
the "standard methods" aren't bound up in the interface, so you can add
your own methods instead of being limited to the ones that the interface
author has included.

On the other hand, it means that method implementations have to be packaged
in a different class from the interface, which feels less than modular.

Scala takes a different approach. A Scala trait can contain a mix of
abstract methods without implementation as well as concrete methods with
an implementation. (It can also be a pure interface, with only abstract
members.)

Here's a Scala trait that represents objects that can be compared
and ordered:

    trait Ord {
      def < (that: Any): Boolean
      def <=(that: Any): Boolean = (this < that) || (this == that)
      def > (that: Any): Boolean = !(this <= that)
      def >=(that: Any): Boolean = !(this < that)
    }

Orderable objects can extend `Ord`, but only need to implement the
method `<`. They then get the other operators for free, implemented
automatically by Ord in terms of `<`.

    class Date extends Ord {
      def < (that: Any): Boolean = /* implementation */
    }

    // can now write: myDate >= yourDate

A similar trait, called `Ordered` already exists in Scala, so there is no
need to actually define my trait.

### Scala Traits vs. C# Extension Methods

Scala has a different way of packaging standard implementations
from C#'s extension methods, but why is it interesting?
There are a couple of things that you can do with Scala traits that
don't fall nicely out of the extension methods approach.

First, you can override the default implementations of trait members,
to take advantage of additional information or capabilities available
in the implementing type.

Let's look at another `IEnumerable` example, recast as a Scala trait:

    trait Enumerable {
      def getEnumerator(): Enumerator
      def count: Int = {
        // Shockingly bad style; for illustrative purposes only
        var c = 0
        val e = getEnumerator()
        while (e.moveNext()) c = c + 1
        c
      }
    }

This (ignoring style issues for now) is the only fully general
implementation we can provide for count: it will work with any `Enumerable`.
But for collections that know their sizes, such as `arrays` or `List<T>`,
it's gruesomely inefficient. It iterates over the entire collection,
counting elements one by one, when it could just consult the `size` member
and return that.

Let's fix that:

    class MyList extends Enumerable {
      private var _size;
      def getEnumerator(): Enumerator = /* ... */
      override def size: Int = _size
    }

The `size` member of the `Enumerable` trait works like a virtual method:
it can be overridden in classes which implement/derive from `Enumerable`.

Compare this to the `Count()` extension method on `IEnumerable<T>` in LINQ.
This achieves the same effect by trying to cast to `ICollection`, which is
fine as far as it goes but isn't extensible.

Suppose you create an enumerable class that can count itself quickly but
isn't a collection -- for example a natural numbers range object.
With a Scala trait, the `NumberRange` type could provide an efficient
override of `size`, just like any other virtual method; with C# extension
methods, `Enumerable.Count()` would have to somehow know about the
`NumberRange` type in advance, or fall back on counting elements one by one.

Second, with Scala you can choose a trait implementation when you
instantiate an object, rather than having it baked in at the class level
once and for all. This is called mixin-composition.

Suppose you're creating a `MyList` instance, but for some reasons the size
should be inflated.
In C#, you'd need to create a `InflatedSizeMyList` class and override the
`Count` property.
In Scala, you can just mix in a `InflatedSize` version of the trait:

    trait InflatedSize extends Enumerable {
      override def size: Int = super.size + 100
    }

    val normal = new MyList
    Console.WriteLine(normal.count)  // meh
    val puffedUp = new MyList with InflatedSize
    Console.WriteLine(puffedUp.count)  // eek!

As you can imagine this gives us much better granularity and composability
of traits and implementations than we get from the extension methods
approach, or indeed from single implementation inheritance type systems
in general.

So Scala traits have some distinct advantages over extension methods.
The only downside appears to be the inability for clients to add their
own methods to a trait after the fact.

Fortunately, you can work around this in Scala using so-called implicit
conversions. They enable Scala programmers to enrich existing types with new
functionality.

## Singletons

In C#, if you want to create a singleton object, you have to create a class,
then stop evildoers creating their own instances of that class, then create
and provide an instance of that class yourself.

While this is hardly a real-world issue, it does feel like pushing against
the grain of the language. Nor is it great for maintainers, who have to be
able to recognise a singleton by its pattern
(private constructor, public static readonly field, ...), or for clients,
who have to use a slightly clumsy multipart syntax to refer to the
singleton (e.g. `Universe.Instance`).

What would be easier for all concerned would be if you could just declare
objects *as* singletons. That is, instead of writing class `Universe` and a
`public static readonly` instance of it, you could just write `object Universe`.

And that's exactly what Scala allows you to do:

    object Universe {
      def contains(obj: Any): Boolean = true
    }

    val v = Universe.contains(42)

What's going on behind the scenes here?  It pretty much goes without saying
that the Scala compiler is creating a new type for the singleton object.
In fact it creates two types, one for the implementation and one for the
interface. The interface looks like a .NET static class (actually, the
.NET 1.x equivalent, a sealed class with only static members).
Thus, a C# program would call the example above as `Universe.contains(42)`.

Singleton objects are first-class citizens in Scala, so they can for
example derive from classes. This is a nice way of creating special values
with custom behaviour: you don't need to create a whole new type, you just
define an instance and override methods in it:

    abstract class Planet {
      def distanceFromEarth: BigInt
    }

    object Earth extends Planet {
      // We can implement the method with a constant value
      val distanceFromEarth: BigInt = 0
    }

Obviously this is a simple example, but "special singletons" turn out to
be an important part of the functional idiom, for example for bottoming out
recursion. *Scala by Example (PDF)* describes an implementation of a Set class
which is implemented as a tree-like structure ("left subset - member - right
subset"), and methods such as `contains()` work by recursing down to the
child sets. For this to work requires an `EmptySet` whose implementation
(state) and behaviour are quite different from non-empty sets -- e.g.
`contains()` just returns `false` instead of trying to delegate to
non-existent child sets. Since `EmptySet` is logically unique and immutable
it is both simpler and more efficient to represent it as a singleton:
i.e. to declare `object EmptySet` instead of `class EmptySet`.

In fact the whole thing can become alarmingly deep: *Scala by Example*
also includes a description of `Boolean` as an `abstract class`, and
`True` and `False` as singleton objects which extend `Boolean` and provide
appropriate implementations of the `ifThenElse` method.

## Generics

In Scala, Generics are defined within _square brackets_, not _angle brackets_:

    class Box[T]

A generic type can be constrained with

- upper bounds `<:` (the generic type must be a subtype of another type)
- lower bounds `>:` (the generic type must be a supertype of another type)
- context bounds `:` (the generic type must support the operations described by another type)

## Implicits

Scala implicits offer some features which will be familiar to the C#
programmer, but are much more general in nature and go far beyond what can
be done in C#.

### Enriching types in C# and Scala

Scala, like C#, is statically typed: a class’ methods are compiled into the
class definition and are not open for renegotiation. You cannot, as you
might in Ruby or Python, just go ahead and declare additional methods on an
existing class.

This is of course very inconvenient. You end up declaring a load of
`FooHelper` or `FooUtils` classes full of static methods, and having to
write verbose calling code such as `if (EnumerableUtils.IsEmpty(sequence))`
rather than the rather more readable `if (sequence.IsEmpty())`.

C# 3 tries to address this problem by introducing extension methods.
Extension methods are static methods in a `FooHelper` or `FooUtils` kind
of class, except you’re allowed to write them using member syntax.
By defining `IsEmpty` as an extension method on `IEnumerable`, you can
write `if (sequence.IsEmpty())` after all.

Scala disapproves of static classes and global methods and offers
an alternative approach. You’ll still write a `FooHelper` or `FooUtils`
kind of class, but instead of taking the `Foo` to be Helped or Utilised as
a method parameter, your class will wrap `Foo` and enrich it with
additional methods. Let’s see this in action as we try to add a method to
the `Double` type:

    class RicherDouble(d: Double) {
      def toThe(exp: Double): Double = math.pow(d, exp)
    }

(We call the class `RicherDouble` because Scala already has a `RichDouble`
class defined which provides further methods to `Double`.)

Notice that `toThe` is an instance method, and that `RicherDouble` takes a
`Double` as a constructor parameter. This seems pretty grim, because we’d
normally have to access the function like this:

    val result = new RicherDouble(2.0).toThe(7.0)

To make it look nice, Scala supports an `implicit` keyword to allow an
 *implicit conversion* from `Double` to `RicherDouble`:

    implicit class RicherDouble(d: Double) {
      def toThe(exp: Double): Double = math.pow(d, exp)
    }

Now we can write this:

    val twoToTheSeven = 2.0.toThe(7.0)

and all will be well. The `Double` type has apparently been successfully
enriched with the `toThe` method.

This is, of course, just as much an illusion as the C# equivalent.
C# extension methods don’t add methods to a type, and nor do Scala
implicit conversions. What has happened here is that the Scala compiler
has looked around for implicit methods that are applicable to the type of
`2.0` (namely `Double`), and return a type that has a `toThe` method.
Our `RicherDouble` constructor fits the bill, so the Scala compiler
silently inserts a call to that method. At runtime, therefore, Scala calls
`new RicherDouble(2.0)` and calls the `toThe` on the resulting
`RicherDouble`.

C# extension methods are designed to allow the ad-hoc addition of methods
to existing classes. Scala improves upon C#'s approach, because it not
only allows to add methods, but can use these methods to satisfy interfaces
unknown to the orginal author.

### Implicit Parameters

What we saw above was an implicit method – a method which, like a C#
implicit conversion operator, the compiler is allowed to insert a call to
without the programmer writing that call. Scala also has the idea of
implicit parameters – that is, parameters which the compiler is allowed to
insert a value for without the programmer specifying that value.



A Scala implicit parameter, on the other hand, gets its value from the
calling context. That allows programmer calling the method to control the
implicit parameter value, creating an extensibility point that optional
parameters don’t provide.

This probably all sounds a bit weird, so let’s look at an example. Consider
the following `Concatenate` method:

    public T Concatenate<T>(IEnumerable<T> sequence, T seed, Func<T, T, T> concatenator);

We pass this guy a sequence, a start value and a function that combines two
values into one, and it returns the result of calling that function across
the sequence. For example, you could pass a sequence of strings, a start
value of `String.Empty`, and `(s1, s2) => s1 + s2`, and it would return you
all the strings concatenated together:

    IEnumerable<string> sequence = new string[] { “mog”, “bites”, “man” };
    string result = Concatenate(sequence, String.Empty, (s1, s2) => s1 + s2);
    // result is “mogbitesman”

But this is a unpleasantly verbose. We’re having to pass in `String.Empty`
and `(s1, s2) => s1 + s2` every time we want to concatenate a sequence of
strings. Not only is this tedious, it also creates the opportunity for
errors.
We’d like to just tell the Concatenate function, “Look, this is how you
concatenate strings,” once and for all.

Let’s start out by redefining the `Concatenate` method in Scala.
I’m going to factor out the seed and the concatenator method into a trait
because we’ll typically be defining them together.

    trait Concatenator[T] {
      def startValue: T
      def concat(x: T, y: T): T
    }

    object implicitParameters {
      def concatenate[T](xs: List[T])(c: Concatenator[T]): T =
        if (xs.isEmpty) c.startValue
        else c.concat(xs.head, concatenate(xs.tail)(c))
    }

We can call this as follows:

    object stringConcatenator extends Concatenator[String] {
      def startValue: String = ""
      def concat(x: String, y: String) = x.concat(y)
    }

    object implicitParameters {
      def main(args: Array[String]) = {
        val result = concatenate(List("mog", "bites", "man"))(stringConcatenator)
        println(result)
      }
    }

So far, this looks like the C# version except for the factoring out of the
`Concatenator` trait.  We’re still having to pass in the
`stringConcatenator` at the point of the call. Let’s fix that:

    def concatenate[T](xs: List[T])(implicit c: Concatenator[T]): T =
      if (xs.isEmpty) c.startValue
      else c.concat(xs.head, concatenate(xs.tail))

We’ve changed two things here.  First, we’ve declared c to be an *implicit
parameter*, meaning the caller can leave it out.  Second, we’ve left
it out ourselves, in the recursive call to `concatenate(xs.tail)`.

Well, okay, it’s nice that `concatenate` now doesn’t have to pass the
`Concatenator` explicitly to the recursive call, but we’re still having to
pass in the `stringConcatenator` object to get things started. If only
there were some way to make the `stringConcatenator` object itself implicit…

    object Implicits {
      implicit object stringConcatenator extends Concatenator[String] {
        def startValue: String = ""
        def concat(x: String, y: String) = x.concat(y)
      }
    }

Again, we’ve done two things here. First, we’ve declared the
`stringConcatenator` object implicit. Consequently, we’ve had to move it
out of the top level, because Scala doesn’t allow implicits at the top
level (because they’d pollute the global namespace, being in scope even
without an explicit import statement).

Now we can call `concatenate` like this:

    import Implicits._

    object implicitParameters {
      def main(args: Array[String]) = {
        val result = concatenate(List("mog", "bites", "man"))
        println(result)
      }
    }

And we’ll still get “mogbitesman” as the output.

Let’s review what’s going on here.  The implicit parameter of concatenate
has been set to our `stringConcatenator`, a default value that the
`concatenate` method knew nothing about when it was compiled. This is
somewhere north of what classical optional parameters are capable of,
and we’re not finished yet. Let’s build a `listConcatenator`.

    object Implicits {
      class ListConcatenator[T] extends Concatenator[List[T]] {
        def startValue: List[T] = Nil
        def concat(x: List[T], y: List[T]) = x ::: y
      }
      implicit object stringListConcatenator extends ListConcatenator[String] { }
    }

This is a bit vexing. `List` in Scala is a generic type, and has a generic
concatenation method called `:::`. But we can’t create a generic object,
because an object is an instance. And implicit parameters have to be objects.
So the best we can do is build a generic `ListConcatenator` class, and then
create trivial implicit objects for each generic parameter type we might
need.

However, let’s not worry about the implementation, and see how this is used
at the calling end:

    val result = concatenate(List(
      List("mog", "bites", "man"),
      List("on", "beard")
    ))

This displays `List(mog, bites, man, on, beard)`; that is, it concatenates
the two `List[String]`s into one. Once again, we have not had to pass
`stringListConcatenator` explicitly: the Scala compiler has gone and found
it for us. We can use the exact same calling code to concatenate lists and
strings.

### Why Should I Care?

Implicit parameters are about convenience and expressiveness. Implicit
parameters give you a way of describing how a function should handle
different situations, without needing to bake those situations into the
function logic or to specify them every time you call the function.
You don’t want to have to tell the `concatenate` function whether to use
the `List` or `String` concatenator every time you call it: the compiler
knows what you’re concatenating; specifying how to concatenate it just
gives you a chance to get it wrong!

### Conclusion

Scala’s `implicit` keyword goes beyond C#’s equivalent.  As in C#, it is
used for implicit conversions; unlike C#, this is the idiomatic way to add
operations to an existing type, removing the need for the separate
extension method syntax.


Implicit parameters have no equivalent in C#.
They are like being able to add default values to a method: just as a C#
using statement bring implicit methods into scope, a Scala import statement
can bring default values into scope. If implicit conversions are a way of
extending classes, then implicit parameters are a way of extending methods,
creating simple, reliable shorthands for complex generic methods, and
making up another piece of the Scala DSL jigsaw.

<!--
## Compatibility

### Within Scala

**TODO**

### With Java

**TODO**

## Documenting source code with ScalaDoc

**TODO**

## Documentation

**TODO**

## IDE support

**TODO

## Build Tools

**TODO**

## Deployment

**TODO**
-->
