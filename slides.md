Monstrous Names Aren't Scary
============================

Kris Nuttycombe
May 28, 2016

<aside class="notes">

One of the first things that I think that everybody notices, basically as soon
as they get started with functional programming, is that as soon as you start
looking at resources, reading blog posts, maybe trying to understand the
workings of some library related to a problem you're interested in, that 
the words you're reading have absolutely *nothing to do* with anything that
you've previously encountered in programming. This, of course, can be a little 
scary. The purpose of this talk is to attempt to dispel a bit of that fear.

But to do so, I think we're going to first have to talk a bit about *why*
this experience is scary. 

The truth is, your fear is justified. In one way or another, every one of those
unfamiliar words represents a concept that you need to learn in order to be
able to create working software. Now, sure, it's possible to write some simple
programs without, for example, knowing what a monad is, but the truth is that I
don't think any of us really want to just write "getting your feet wet" kinds
of programs. We know deep down that if we wan to really understand a language's
strengths and weaknesses, we'll have to do something nontrivial. And to do
something nontrivial, we'll have to admit that that there's a lot we don't
know, and that learning it will probably take a lot of effort and time. If that's
not scary, it's at least a bit daunting.

The purpose of this talk is to give you a bit of a framework for thinking about
functional programming that will hopefully make it all a bit less daunting.

</aside>

Functional Programming
======================

* Functional programming is programming with values.
* Primitive arrangements of bits are values.
* Instances of data structures are values.
* Functions are values.
* Programs are values.

<aside class="notes">

What is functional programming? A lot of sources describe it in terms of
programming with pure functions that deterministically map inputs to outputs,
using only immutable data structures. A lot of attention is given to
programming with higher-order functions, which are just functions that take
other functions as arguments. However, I think this focus on functions is a
little misplaced. When I speak about functional programming, somewhat
ironically, what I'm talking about is programming with *values*. 

So what are values? First we can think of some really primitive building
blocks, from which we can build everything else - these are boolean values, or
as we frequently think of them when gathered together with some structure,
bits. For convenience, most languages give us some predefined ways in which we
can refer to larger assemblies of these things; 64-bit integers, IEEE floats,
and so forth. 

Now, think for a moment about the value "True." While it's pretty easy to
imagine a *reference* to a boolean value like being mutable, it's pretty clear
that the value "True" itself isn't; by extension, any conceptual assembly of
true and false values is itself an immutable value. In functional programming,
we also inhibit *references* to values from changing.

So, where does this leave us? If we can't change values, and we can't change
references to values, what exactly can we do? It comes down to just a couple of
operations. First, we can, given a value, think about observe some subset of
that value, so that instead of looking at the whole we're just considering a
part.

The other thing that we can do, and the operation that is going to be the focus
of all of the rest of this talk, is that we can combine two values so that we
observe them as a new value.

So, what does this all have to do with how names in functional programming can
be unfamiliar and scary? Well, you know that apocryphal (and probably false)
story about how the Inuit have maybe fifty, or maybe a couple of hundred,
distinct words for snow? In functional programming, we have lots and lots of 
ways of combining values, and when you have so many ways of doing *essentially*
the same thing, you need a lot of names to describe the subtle differences
in these combining operations.

</aside>

Records
=======

~~~{haxe}

typedef Pair<A, B> = {
  fst: A,
  snd: B
};

~~~

<aside class="notes">

This is an example of the most trivial possible way that one can put two values
together to create a new value. This slide is really just in here to give me an
opportunity for a bit of commentary about the programming lanugage I'm using
for examples in this talk. It's called Haxe, and while I use it in my day job
at the moment, I don't really recommend it much. If you want to really learn
typed functional programming, you should pick up a copy of the new Haskell book
by Chris Allen and Julie Moronuski, or failing that you can take the road that
I did and muddle your way through using something like Scala where you can
learn functional style but still fall back on your old bad habits when you're
under deadline and have to crank out some code. Haxe isn't really a functional
programming language in any meaningful sense. However, it has a few qualities
that I think make it useful for an introductory talk.

First off, it's a language that I think just about anybody who has seen code
before can read. Function definitions look kind of like JavaScript, but with
type ascription using this colon syntax that we can see here, and type
parameterization (the real name for what heathens call "generics") that looks
like it does in Java, C# or Swift.

</aside>

Enum
====

~~~{haxe}

enum Maybe<A> {
  Just(a: A);
  Nothing;
}

function orZero(imay: Maybe<Int>): Int {
  return switch imay {
    case Just(i): i;
    case Nothing: 0;
  };
}

orZero(Just(1)) 
// 1

orZero(Nothing)
// 0

~~~

<aside class="notes">

Also, Haxe has syntax for sum types (actually generalized algebraic data types)
and pattern matching. 

Whoah, wait a minute, generalized algebraic data types? That's definitely a
scary term! Hopefully we'll be able to make that a little less scary as we go
along here, but for the moment I do want to speak really briefly about sum
types. Haxe calls them 'enums' which is helpfully suggestive for folks coming
from languages like C and Java. 

Unlike what's typically termed an enum, values of a sum type can carry around
additional data, which you can retrieve when you pattern match on a value of
that type. Each term of the 'enum' block is called a 'constructor' for that data
type - just to be clear, 'Just' isn't a type, it's a constructor for a value
of type 'Maybe of A'. 

Types like this, where you can have multiple different constructors for values
of the same type, are really, really important, so important that I kind of
think that languages that lack first-class support for them are really
crippled.  The reason that they're so important is that, when building an
application, it's really useful to think about the state space of the
application, and more specifically how to minimize the number of states
there are within that space. Does everybody know what I'm talking about here?

(If not, proceed down)

Finally, it *does not* have higher kinded types, which means that a whole lot
of abstractions that folks from the Haskell and Scala worlds find invaluable
are not actually representable in the language. While this is in my opinion a
terrible deficiency, for the purposes of this talk I'm going to spin it as an
advantage, because it forces me to use examples that are reproducible in
*other* languages that lack higher-kinded types, like Java and C# and Swift,
which are probably what many of you are stuck using. More importantly though, 
it means that the examples that you see will be very concrete. In functional
proramming, we love to use a lot of high-level abstractions because they let
us avoid writing a lot of boilerplate code and help us to avoid coupling,
but in my experience for learning it's necessary to progress from concrete
examples to the abstract. Here, all that you're going to see are the concrete
bits, because the language doesn't actually let me represent the abstracted
versions.

</aside>

How Many States?
----------------

~~~{haxe}
var b: Bool = /* censored */;
~~~

<div class="fragment">
~~~{haxe}
var i: Int = /* censored */;
~~~
</div>

<div class="fragment">
~~~{haxe}
var x: { b: Bool, i: Int } = /* censored */;
~~~
</div>

<div class="fragment">
~~~{haxe}
enum Either<A, B> {
  Left(a: A);
  Right(b: B);
}

var x: Either<Bool, Int> = /* censored */;
~~~
</div>

How Many States?
----------------

~~~{haxe}

var b: Bool = /* censored */;
// 2 states

var i: Int = /* censored */;
// 2^32 states

var x: { b: Bool, i: Int } = /* censored */;
// 2 * 2^32 states

enum Either<A, B> {
  Left(a: A);
  Right(b: B);
}

var x: Either<Bool, Int> = /* censored */;
// 2 + 2^32 states

~~~

Monsters!
=========

### Abstract Algebra
Semigroup
Monoid

### Category Theory
(Endo)Functor
Applicative
Monad

### Lens
Lens
Prism

### Free
Coyoneda
Free Applicative
Free Monad
Tagless Final Interpreters

Semigroup
=========

* An operation that combines two values of the same type...

~~~{haxe}

typedef Semigroup<A> = {
  append: A -> A -> A
}

~~~

<div class="fragment">

* ... which is associative

~~~{haxe}

s.append(s.append(a0, a1), a2) == s.append(a0, s.append(a1, a2))

~~~

</div>

<aside class="notes">

Why is associativity important? 

* It allows us to reorder operations and achieve the same result,
  so long as the order of the operands is preserved.

* A coworker stated this as "You want associativity when you want to muck 
  about with evaluation order."

</aside>

------

~~~{haxe}

function reduce<A>(xs: Array<A>, s: Semigroup<A>): Maybe<A> { ... }

function 

~~~

<aside class="notes">

So, with our semigroup in hand, we can then go on to write a bunch of 

So, why don't we just call this an "AssocAppender" or something? Well, we could.
However, here's a chart that I grabbed off of Wikipedia:

</aside>

------

<img src="./img/group-like-structures.png" width="400"/>

<aside class="notes">

We don't call this an AssocAppender because to do so would rob us of centuries
worth of literature and learning about how we can use associative binary 
operations that put together two members of a set and return another member
of that same set. Calling it a Semigroup helps us identify that this operation
is part of a larger framework of binary operations on sets, some of which may be 
useful to us in similar situations. When we look here and see that 'Monoid' is just a semigroup with 
an identity element. 

</aside>

Composition
===========

* (.) :: (b -> c) -> (a -> b) -> (a -> c)

Kleisli
-------

* (>=>) :: Monad m => (a -> m b) -> (b -> m c) -> a -> m c

Lens
----


