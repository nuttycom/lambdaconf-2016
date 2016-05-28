Monstrous Names Aren't Scary
============================

Kris Nuttycombe

(@nuttycom)

May 28, 2016

<aside class="notes">

One of the first things that I think that everybody notices, basically as soon
as they get started with functional programming, is that as soon as you start
looking at resources, reading blog posts, maybe trying to understand the
workings of some library related to a problem you're interested in, that 
the words you're reading have absolutely *nothing to do* with anything that
you've previously encountered in programming. Even familiar terms like "sum" 
and "product" are endowed with new and mysterious significance. It's like being 
dropped into the middle of a D&D campaign without access to the monster manual.

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

------

<img src="./img/fp_with_values.jpg" width="800"/>

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

* Primitive arrangements of bits are values
* Instances of data structures are values
* Functions are values
* Programs are values
* Types are values (!)

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

Before we can talk about the interesting ways in which we can combine values,
though, I want to quickly say a word about types.

</aside>

Types
=====

The purpose of a type system is to verify that we have minimized the potential
state space of an application such that only valid states are representable.

<aside class="notes">

When I talk about functional programming, I essentially always talk about
*typed* functional programming, because what I'm personally most interested
in is our ability to reason about our programs, and, more importantly, the
ways that computers can help us with our reasoning.

As programmers, we're engaged in building some of the most complex artifacts
that human beings have ever created. The number of states that even the
simplest business application can represent dwarfs that of most physical
systems. This complexity is hard for our brains to deal with, but it's the
kind of thing that computers are great at. 

A sufficiently strong type system allows us to *simplify* our applications.
Specifically, they allow us to minimize the state space, ideally to the point
where *only valid states are representable.* Now, there are obviously
situations - invalid user input, network irregularities, and so forth - where
the program will not be able to compute the *desired* result, but by idenfying
these errors *using the type system* we transform these problems from things
that can cause an unpleasant user experience to ones where we can, and indeed
are compelled to, respond gracefully. The stories you hear about programs
working properly once they typecheck are not only true, but in my experience
they understate the value. My experience is that, with an adequately described
state space, not only do programs work on the first try, they also *virtually
never break.*

I regard the compilers I work with as a brain prosthesis - they free me from
trivial details of tracking the types of values through a program and allow me
to focus on what those values represent and how they are transformed. In
programs written in dynamic languages, I can't *afford* to make use of a lot of
the useful abstractions that are available in languages with strong type
systems, simply because too much of my attention is consumed by the mental
effort of tracking how to call the APIs I depend upon.

By contrast, in a language with a strong type system, the compiler takes care
of me, making sure that I'm not wasting my time with subtly incorrect API calls
and null pointers. This frees me up to understand how the problem I'm trying
to solve relates to other similar problems. Since it's likely that someone else
has solved a similar problem before, I can use my understanding of the *type*
that I need to search for a solution. Now, the only languages that I'm aware of
that really make use of this capablity to a substantial degree are functional
languages: Haskell has Hoogle, there's an equivalent service for PureScript, and
there are at least a couple of projects that attempt to do the same for OCaml and
Scala, though at least the Scala one doesn't seem to work very well.

So, now, let's talk briefly about how we can add types together to create new
types.

</aside>

Products
========

~~~{haxe}

typedef Pair<A, B> = {
  a: A,
  b: B
};

~~~

<aside class="notes">

Now that that's out of the way, let's talk some more about combining values.
Here, we're putting a two types (which, as I said earlier, are values, but here
values in our type-level language) together to create a new type. 

This slide is really just in here to give me an opportunity for a bit of
commentary about the programming lanugage I'm using for examples in this talk.
It's called Haxe, and while I use it in my day job at the moment, I don't
really recommend it much. If you want to really learn typed functional
programming, you should pick up a copy of the new Haskell book by Chris Allen
and Julie Moronuski, or failing that you can take the road that I did and
muddle your way through using something like Scala where you can learn
functional style but still fall back on your old bad habits when you're under
deadline and have to crank out some code. Haxe isn't really a functional
programming language in any meaningful sense. However, it has a few qualities
that I think make it useful for an introductory talk.

First off, it's a language that I think just about anybody who has seen code
before can read. Function definitions look kind of like JavaScript, but with
type ascription using this colon syntax that we can see here, and type
parameterization (the real name for what heathens call "generics") that looks
like it does in Java, C# or Swift.

</aside>

---------

Sums
====

~~~{haxe}

enum Either<A, B> {
  Left(a: A);
  Right(b: B);
}

~~~

<aside class="notes">

Secondly, Haxe has syntax for sum types (actually generalized algebraic data
types) and pattern matching. 

Whoah, wait a minute, generalized algebraic data types? That's definitely a
scary term! Hopefully we'll be able to make that a little less scary as we go
along here, but for the moment I do want to speak really briefly about sum
types. Haxe calls them 'enums' which is helpfully suggestive for folks coming
from languages like C and Java. 

Unlike what's typically termed an enum, values of a sum type can carry around
additional data, which you can retrieve when you pattern match on a value of
that type. Each term of the 'enum' block is called a 'constructor' for that data
type - just to be clear, 'Left' isn't a type, it's a constructor for a value
of type 'Either of A and B'. 

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

Sum Type Encodings
------------------

~~~{haxe}

typedef Either<A, B> = {
  function apply<C>(ifLeft: A -> C, ifRight: B -> C): C
}

function left<A, B>(a: A): Either<A, B> = {
  function apply<C>(ifLeft: A -> C, ifRight: B -> C): C {
    return ifLeft(a);
  }
}

function right<A, B>(a: A): Either<A, B> = {
  function apply<C>(ifLeft: A -> C, ifRight: B -> C): C {
    return ifRight(a);
  }
}

~~~


<div class="notes">

One more thing before we move on, if you're not fortunate enough to work in a 
language that has syntax for sum types, they're still not unavailable to you
so long as you have either objects or closures. 

Do all of you know how to do this encoding?

In Object-Oriented languages, this sort of encoding is commonly known as
the Visitor pattern. In the so-called "Gang of Four" Design Patterns book,
however, they talk about a crippled version of this approach, which has their
"visit" and "accept" functions returning void, which means building up
your result via mutation of some shared mutable state and is just about
the most awful and non-composable bastardization of a beautiful idea that
one could come up with. 

</div>

JSON
----

~~~{haxe}

enum JValue {
  JObject(xs: Array<JAssoc>);
  JArray(xs: Array<JValue>);
  JString(s: String);
  JNum(f: Float);
  JBool(b: Bool);
  JNull;
}

typedef JAssoc = {
  fieldName: String,
  value: JValue
}

~~~

<aside class="notes">

Here's another example of the sort of thing that sum types permit you
to do - rather than using some sort of awkward map- and object-based 
representation of a JSON value, we can encode JSON as a recursive
sum type, which we can then safely traverse, ensuring that the structure
that we are expecting is present at each step. 

Sum types are really well suited to abstract syntax trees like this. We'll
see them again and again throughout the reat of the talk, and we'll expand 
our ideas about what kinds of things can exist in an abstract syntax tree.

</aside>

Functions
=========

~~~{haxe}

var f : A -> B = ...

~~~

<aside class="notes">

There's one more way to compose a pair of types apart from products and
sums, and that's this: the type of a function from one type to another 
is itself a type. And just as we've been able to count the number of states
introduced into our program with products and sums, we can do the same thing
here.

</aside>

<div class="fragment">
~~~{haxe}

enum OneOfThree = { First; Second; Third; };

var f : OneOfThree -> Bool = ...

~~~

<aside class="notes">

So, we have three possible inputs, each of which may independently produce one
of two outputs. We want to figure out the size of state space that the type of
the function can represent in our program, so we have to multiply the number of
possible outputs by itself for each case of the input, which is to say that the
number of possible inhabitants of a function type is the number of inhabitants
of the output type to the *power* of the number of inhabitants of the input
type.

Now that we've covered the three ways that we can compose a pair of types to
yield a new type, let's switch over to talking about values for a bit.

</aside>

</div>

Function Composition
====================

~~~{haxe}

function compose<A, B, C>(f: B -> C, g: A -> B): A -> C {
  return function(a: A) {
    return f(g(a))
  };
}

~~~

<aside class="notes">

Here are the first two values that we'll think about composing - both of which
are functions.

Since this is an introductory talk about functional programming, I thought it
would be remiss of me to omit a slide on function composition, first because
it's so fundamental, but secondly because we will see echoes of this simple
form of composition later when we talk about Kleisli composition and lenses. 

Now, while function composition might seem a bit trivial in a talk that's
supposedly going to be about concepts with big scary names, there's something
important that I want to observe.

A lot of resources on functional programming talk about working with pure
functions (that is, functions that always produce the same output given a
particular input) and how this precludes us from performing side effects in
function evaluation. What I'd like to point out, however, is that this
"functional purity" itself is a consequence of the fact that we're programming
with values. All of our inputs are values, which implies that ambient changing
things like the state of the world cannot be inputs, at least not unless
they're explicitly snapshotted as unchanging values. Given this, there is
simply no *opportunity* to vary the behavior of a function in such a way as to
produce two different output values given the same input - there's nothing 
to dispatch upon that might change from one evaluation of a function to 
the next. 

So functional purity is a natural outgrowth of the fact that we're programming
with values. In the context of function composition, though, the really
important thing is that this purity which we've derived means that we're able
to reason about components of our systems independently, and still have that
reasoning hold when those components are composed. In mathematics, when you
have a proof of a theorem, you're then able to use that theorem in the
derivation of more complex results without having to check whether the original
proof of the theorem still holds. 

So, now let's get on to something that actually has a bit of a monstrous name.

</aside>

Associativity
=============

~~~{haxe}

f.compose(g.compose(h)) == (f.compose(g)).compose(h)

f.compose(g)(x) = f(g(x))

f.compose(g.compose(h)(x)) 
  = f(g.compose(h)(x)) 
  = f(g(h(x))) 
  = (f.compose(g))(h(x))) 
  = (f.compose(g)).compose(h)(x)

~~~

<aside class="notes">

Okay, so associativity isn't really that monstrous. After all, most of us
encountered it in grade school when they taught us addition. However, for many
of us that may also be the last time we actually *thought* about associativity,
and yet, in terms of composing a program by combining values, it's a vitally
important property.

If we're attempting to build up a system by the composition of values,
we can't have the behavior changing depending upon what order we've put
together the parts of our system in. Associativity of important binary
operations like function composition allow us to decompose our system 
freely - we can choose to factor out either the 'g.compose(h)' or
the 'f.compose(g)' in the above example without changing the meaning
of our program. We're free to let the *human* concerns of what pieces
of functionality "make sense" together guide our design.

What I've written out here is a short proof of the fact that function
composition is associative. 

</aside>

<div class="fragment">

> Programs must be written for people to read, and only incidentally for machines to execute.
> - Abelson, Structure and Interpretation of Computer Programs.

</div>

<aside class="notes">

This is a quote from Harold Abelson in one of the bibles of our profession.
Associativity gives us the freedom we need to be able to rearrange programs
with confidence, relying upon the types of our functions to guide us. We'll
see the importance of associativity over and over again as we explore more
monstrous names in functional programming.

</aside>

Semigroup
=========

An associative binary operation that combines two values of a type to yield a
new value of that type.

~~~{haxe}

typedef Semigroup<A> = {
  append: A -> A -> A
};

s.append(s.append(a0, a1), a2) == s.append(a0, s.append(a1, a2))

~~~

<aside class="notes">

If we're going to be combining values, this seems about as primitive
a mechanism for combining values as one could come up with. 

So, again, associativity is important because it allows us to reorder
operations and achieve the same result, so long as the order of the operands is
preserved.  A coworker stated this as "You want associativity when you want to
muck about with evaluation order."

Now, unfortunately we don't have a mechanism in the Haxe type system to demand
proof of the associativity that we require of implementations of this type, so
we'll have to simply go by convention. This is unfortunate, because it means that
we need to be more careful when writing implementations, but this isn't a defect
that's unique to the Haxe type system - Haskell doesn't require proofs that the
binary operation in instances of its "Semigroup" typeclass are associative
either.

</aside>

------

~~~{haxe}

function nelSemigroup<A>(): Semigroup<NonEmpty<A>> {
  function append0(e0: NonEmpty<A>, e1: NonEmpty<A>): NonEmpty<A> {
    return switch e0 {
      case Single(a): ConsNel(a, e1);
      case ConsNel(a, rest): ConsNel(a, append0(rest, e1));
    };
  }

  return { append: append0 };
}

~~~

<aside class="notes">

In practical terms, here's a common example of how you'll see semigroups used.
A useful semigroup value is the semigroup for non-empty lists. 

We can see from the pattern matching we're using to deconstruct the first argument
to our append function that our NonEmpty type is a sum type with two constructors,
one the constructor for a non-empty list of a single element, and the other the
constructor which takes a head value and a non-empty tail to with that head will
be prepended.

This is a useful data structure for the purpose of things like accumulating parsing
errors. We'll see an example of that later on, so remember this semigroup instance.

There are actually a huge number of types for which we can write Semigroup
instances, for example, for integers, both addition and multiplication are
associative binary operations. Concatenation of ordinary lists or arrays (which
may be empty) is also a binary associative operation. It seems like it's
possible to define a semigroup for just about any data structure for which you
can concieve of an "additive" operation, because those operations can so
frequently be made associative.  Subtractive operations, by contrast, are
frequently not associative though so be cautious. The integers, for example, do
not form a semigroup under subtraction or division.

Now, I've given you the name "Semigroup", described what it does (which is just
combine to values into a new value of the same type, and given you an example
of a semigroup implementation. It's a pretty simple idea. So, rather than calling
this by a potentially unfamiliar name that comes from abstract algebra, why
don't we just call this an "AssocAppender" or something?  We could!
However, here's a chart that I grabbed off of Wikipedia:

</aside>

------

<img src="./img/group-like-structures.png" width="400"/>

<aside class="notes">

We don't call this an AssocAppender because to do so would rob us of centuries
worth of literature and learning about how we can use associative binary
operations that put together two members of a set and return another member of
that same set. Calling it a Semigroup helps us identify that this operation is
part of a larger framework of binary operations on sets, some of which may be
useful to us in similar situations. When we look here and see that 'Monoid' is
a semigroup with an identity element. 

</aside>

------

Monoid
======

~~~{haxe}

typedef Semigroup<A> = {
  append: A -> A -> A
};

typedef Monoid<A> = {
  mzero: A,
  append: A -> A -> A
};

m.append(m.mzero, a) == a
m.append(a, m.mzero) == a

~~~

<aside class="notes">

The "Identity" element for a monoid is simply a value of the type for which we
have our associative binary operation, which obeys these two laws.

Now, the demand that we have an identity element for our associative binary
operation means that we can only write Monoid values for a subset of the types
for which we can write Semigroup values. For example, we can't write a Monoid
instance for NonEmpty - while we have a binary associative concatenation
operation, we can't define a "zero" element for the nonempty list. If we back
up, we see that the semigroup is defined for all A - we can concatenate
nonempty lists without having any idea what ghe element type is - but to define
mzero, we'd have to be able to synthesize a valid value to occupy the Single
element of our non-empty list, and we'd have to be able to do this for *every
possible type.* 

However, because we have this additional operation and law, there is a greater
variety of operations that we can write for monoidal types (types for which we
can define a Monoid) than we can for types for which we can define a Semigroup.
But, if you're writing a function that needs to append two arbitray things of
the same type, you're best off writing it to take a Semigroup to do the
appending in order to make it usable with the greatest possible number of
types. 

</aside>

Kleisli
=======

<aside class="notes">

Let's go back to functions now, and suppose that that we want to compose a
slightly more exotic class of function, namely, *effectful* functions. Here's
an example:

</aside>

~~~{haxe}

function kComposeOption<A, B, C>(f: B -> Option<C>, g: A -> Option<B>): A -> Option<C> {
  return function(a: A) {
    return switch g(a) {
      case Some(b): f(b);
      case None:    None;
    };
  };
}

function kComposeList<A, B, C>(f: B -> List<C>, g: A -> List<B>): A -> List<C> { ... }

function kComposePromise<A, B, C>(f: B -> Promise<C>, g: A -> Promise<B>): A -> Promise<C> { ... }

~~~

<aside class="notes">

Here we have another really straightforward idea, given a scary but informative
name, one that's easily Googlable and can lead you to the start of a nice
random walk through some interesting math and programming Wikipedia pages. 

The idea is the same one that we've been looking at in every case thus far,
that we want to take two values that have the same shape - in this case,
functions from a value to some effectful "container" containing a value of some
other type - and combine them to create a new value, a new function, having the
same shape.

In each case, the resulting function fuses together the effects produced 
by the functions being composed. 

There's something else important here. The effects being fused are fused
*sequentially*. I think that the third example, using Promises, is a good one
for this reason. A Promise represents a potentially asynchronous computation
that may produce a value of the parameter type at some point in the future.
When we compose these functions, it's obvious that the Promise produced by 'g'
must complete and yield a value before 'f' can be evaluated to produce the
final result. This is true in every case - if the Option yielded by g
contains a value, it must have been evaluated completely in order
for f to be invoked. In the second example, each B value produced by 
the that 'g' is used to invoke 'f', and the results are concatenated
sequentially. 

Kleisli composition is all about putting effects in order by way of introducing
a data dependency between those effects.

And, as with function composition, Kleisli composition is associative.  This is
important here for the same reasons that it was important when we were
composing functions where the output type of the first matched the input type
of the second. We want functions like this, where the output value is wrapped
in some effect, to be able to be written and reasoned about independently,
composed based upon their input and output types, and then be confident that
their meaning doesn't change based upon the order in which we performed
the compositions. 

</aside>

Applicative Functors
====================

~~~{haxe}

function liftA2Zip<A, B, C>(f: A -> B -> C): ZipList<A> -> ZipList<B> -> ZipList<C>;

function liftA2Par<A, B, C>(f: A -> B -> C): Par<A> -> Par<B> -> Par<C>;

~~~

<div class="fragment">

~~~{haxe}

function liftA2Val<E, A, B, C>(f: A -> B -> C, s: Semigroup<E>): 
  Validation<E, A> -> Validation<E, B> -> Validation<E C>

~~~

</div>

<aside class="notes">

Let's talk now about a composition of effects that does *not* necessarily demand
to be sequenced by way of data dependency. 

The interesting thing to look at here is the result types of these liftA2
variants. You can think of Par as a type alias (really a newtype) for Promise
that demands parallel execution, rather than the data-dependant, sequential
evaluation that Kleisli composition gives us. ZipList is a similar newtype wrapper
for ordinary lists. In both cases we're using a distinct type to distinguish
how these types of values compose, so as not to confuse the parallel versions
with their sequential variants. 

Anyway, the result type of liftA2Par is a function that directly fuses two
values representing asynchronous effects, but without any data dependency
between the first effect and the second forcing an ordering of evaluation
upon us. The two inputs can be evaluated, as you might expect, in parallel,
and then the provided function applied to both results when they become
available. 

So, we're just composing things again! Applicatives are about nothing more
than fusing together these effects, just in a slightly different way than
Kleisli composition does it.

One more example, this is another really common use of applicative composition,
one that I use every day. If you have a function that does some validation of
input and potentially parsing of a wire form like JSON to a type, it's useful
to be able to accumulate any failures and return them all at once. For this
kind of purpose we clearly wouldn't want sequential composition with
fail-on-first-error semantics, since then it might take a user multiple
round-trips to discover all of their errors. Here we see another use of a
semigroup - if we have a semigroup for the error type, then we can use that to
collect up (dare I say compose?) all of our errors into a single value.

</aside>

Free Applicative Functors
=========================

~~~{scala}

sealed trait FreeAp[F[_], A]
case class Pure[F[_], A](a: A) extends FreeAp[F, A]
case class Ap[F[_], A, I](f: F[I], k: FreeAp[F, I => A])

object FreeAp {
  def liftA2[F[_], A, B, C](f: (A, B) => C): (FreeAp[F, A], FreeAp[F, B]) => FreeAp[F, C] = ???
}

~~~

<aside class="notes">

Sooner or later as you explore the world of functional programming, you're 
going to start running into the idea of "free" structures. Free monads
are perhaps the most famous, but since we're on applicatives right now 
we'll cover them first.

In each of the applicatives we've looked at thus far, the specific applicative
type has had some kind of intrinsic effect in addition to the fact that that
effect could be composed in applicative fashion. The "free" construction is a
way to separate out the applicative composition part from that additional
effect. 

What this means is that we're going to create a data structure that has *no*
intrinsic effects of it own, except to wrap another parameterized type in a way
that lets us implement liftA2 for that wrapped type. In languages that support
higher-kinded types, we get this lifting or wrapping capability as a library;
in Haxe, we have to do a little bit of extra work because we lack the
abstractive capability, but we can get a similar result.

This brings me to an important point. Even in languages that lack some very
important features, like higher-kinded types, it's often possible to achieve
the same compositional properties, at the cost of a loss of abstraction -
repeated code. Here I'm jumping into Scala for a moment to give you an idea
of what this looks like in the general case - we're able to implement liftA2
without choosing ahead of time what F is. Moreover, it's the only operation we
*can* implement, because we have no additional structure.

So what can we do with this thing, if the only operation we can perform is
this particular type of composition? Here's an example.

</aside>

------

~~~{haxe}

enum Schema<A> {
  BoolSchema: Schema<Bool>;
  FloatSchema: Schema<Float>;
  IntSchema: Schema<Int>;
  StrSchema: Schema<String>;
  UnitSchema: Schema<Unit>;

  ObjectSchema<B>(propSchema: ObjAp<B, B>): Schema<B>;
  ArraySchema<B>(elemSchema: Schema<B>): Schema<Array<B>>;

  // schema for sum types
  OneOfSchema<B>(alternatives: Array<Alternative<B>>): Schema<B>;

  // This allows us to create schemas that parse to newtype wrappers
  IsoSchema<B, C>(base: Schema<B>, f: B -> C, g: C -> B): Schema<C>;

  Lazy<B>(s: Void -> Schema<B>): Schema<B>;
}

enum Alternative<A> {
  Prism<B>(id: String, base: Schema<B>, f: B -> A, g: A -> Option<B>);
}

enum PropSchema<O, A> {
  Required<B>(fieldName: String, valueSchema: Schema<B>, accessor: O -> B): PropSchema<O, B>;
  Optional<B>(fieldName: String, valueSchema: Schema<B>, accessor: O -> Option<B>): PropSchema<O, Option<B>>;
}

enum ObjAp<O, A> {
  Pure(a: A);
  Ap<I>(s: PropSchema<O, I>, k: ObjAp<O, I -> A>);
}

~~~

<aside class="notes">

This is a set of data structures that describe a typed schema for JSON data.
It suppports a set of combinators that allow you to build up values of the
Schema type. They're mostly trivial, so I'm just going to show you an example 
of how they're used.

</aside>

------

~~~{haxe}

class Person {
  public var name: String;
  public var age: Option<Int>;
  public var children: Array<Person>;

  public function new(name: String, age: Int, children: Array<Person>) {
    this.name = name;
    this.age = age;
    this.children = children:
  }

  public static var schema: Schema<Person> = ObjectSchema(
    liftA3(
      Person.new,
      Required("name", StrSchema, function(x: Person) return x.name),
      Optional("age",  IntSchema, function(x: Person) return x.age),
      Required("children", 
               ArraySchema(Lazy(function() return Person.schema)), 
               function(x: Person) return x.children)
    )
  );
}

~~~

<aside class="notes">

So here we have a bit of code defining a person class, and a schema for this type. 
Fine. So what can we do with it? If the only effect that the "Free" part gives us
over the underlying sum type is that it permits us to compose values as we see
here, then the only sensible way to make use of such a composed value is to deconstruct
it. What can we do with such a deconstruction?

</aside>

------

~~~{haxe}

public static function parseJSON<A>(schema: Schema<A>, v: JValue): VNel<ParseError, A>

~~~

<div class="fragment">

~~~{haxe}

public static function renderJSON<A>(schema: Schema<A>, a: A): JValue

~~~

</div>

<div class="fragment">

~~~{haxe}

public static function toJsonSchema<A>(schema: Schema<A>): JValue

~~~

</div>

<div class="fragment">

~~~{haxe}

public static function gen<A>(schema: Schema<A>): Gen<A>

~~~

</div>

<aside class="notes">

Each of these functions is an interpreter - it deconstructs the schema a piece
at a time, accumulating the information that was captured during its
composition and using that to construct the output. We put the Schema value
together just so that we could take it apart and allow its structure to guide
the composition of some other data structure.

</aside>

Free Monads
===========

~~~{scala}

sealed trait Free[F[_], A]
case class Pure[F[_], A](a: A) extends Free[F, A]
case class Bind[F[_], A, B](s: Free[F, A], f: A => Free[F, B]) extends Free[F, A]

object Free {
  def kCompose[F[_]: Functor, A, B, C](f: B => Free[F, C], f: A => Free[F, B]): A => Free[F, C] = ???
}

~~~

~~~{scala}

sealed trait FreeAp[F[_], A]
case class Pure[F[_], A](a: A) extends FreeAp[F, A]
case class Ap[F[_], A, I](f: F[I], k: FreeAp[F, I => A])

~~~

<aside class="notes">

Okay, remember Kleisli composition? What I didn't mention when I was talking
about fusing effects is that that's also known as the Monad operation. That's
all that Monad means - a type which has a Monad is one where you can compose
two effects of that type sequentially, with the order of those effects
guaranteed by the data dependency.

So, we saw with free applicatives that the "Free" moniker referred to separating
the compositional effect from the underlying algebraic data type. The Free monad
is really no different. I'm not going to go into details here, except to point out
the difference between 'Ap' and 'Bind'.

In the case of Bind, when we write an interpreter to deconstruct a value of
type Free the major difference that we find is that at each step of the
decomposition, we must evaluate the value on the left side of the Bind
constructor completely in order to be able to evaluate the value on the right.
This enforces the sequentiality that we talked about earlier when discussing
Kleisli composition - while with FreeAp we can effectively recover all of the
structure at once, which is useful for something like producing the serialized
form of a value, in the case of the free monad we are forced to decompose the
structure incrementally, and additional values that we will have to then
decompose may be produced in the process of evaluating the function on the
right of the Bind.

</aside>

Lenses
======

~~~{haxe}

class PLens<S, T, A, B> {
  public var get(default, null): S -> A;
  public var set(default, null): B -> (S -> T);

  public function new(get: S -> A, set: B -> (S -> T)) {
    this.get = get;
    this.set = set;
  }

  public function modify(f: A -> B): S -> T {
    return function(s: S) {
      return this.set(f(this.get(s)))(s);
    };
  }
}

~~~

<aside class="notes">

One more, and we'll be done. This is a lens. Anybody want to guess what it's good for?

</aside>

------

~~~{haxe}

function view<S, T, A, B>(s: S, la: PLens<S, T, A, B>): A {
  return la.get(s);
}

function update<S, T, A, B>(s: S, la: PLens<S, T, A, B>, b: B): T {
  return la.set(b)(s);
}

~~~

<div class="fragment">

~~~{haxe}

function compose<S, T, A, B, C, D>(stab: PLens<S, T, A, B>, abcd: PLens<A, B, C, D>): PLens<S, T, C, D> {
  return PLenses.pLens(
    function(s: S): C return abcd.get(stab.get(s)),
    function(d: D): S -> T return stab.modify(abcd.set(d))
  );
}

~~~

</div>

<aside class="notes">

You can use a lens to pick a component out of a data structure, or to create a copy
of a data structure with that component replaced by a new value.

And, of course... this is almost getting redundant at this point, right? You can compose
lenses, so that the composed value can act on composed data structures. 

</aside>

Conclusion
==========

<aside class="notes">

Okay. Is the horse dead yet? I think that I've been stating the conclusion to this
talk on basically every slide thus far.

Functional programming is about programming with values, and about the only
thing we can do with values are to put them together and take them apart.
Values come in all kinds of interesting shapes, and since each shape is a
little different, we've come up with lots and lots of ways to put values of
different shapes together, and given these different ways names to help us
distinguish one from another. We've drawn on inspiration from set theory,
abstract algebra, category theory, and elsewhere, and so where possible we've
used the names that those fields have given these modes of composition, to
distinguish them and to draw our attention to the fact that, while programming
is a new field, there's a tremendous amount of learning that has come before in
the various subfields of mathematics that we can draw on as we figure out how
to take information and make it dance for our pleasure.

</aside>

