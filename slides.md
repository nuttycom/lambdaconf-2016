Monstrous Names Aren't Scary
============================

Kris Nuttycombe

Preamble
--------

* About me
* Language choice
* Resources


<div class="notes">

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
programs without knowing what a monad is, but the truth is that I don't think
any of us really want to just write "getting your feet wet" kinds of programs.
We know deep down that if 

</div>


Sample of Haxe
--------------

~~~{haxe}

function addOne(i: Int): Int {
  return i + 1;
}

var addOneF: Int -> Int = addOne;

typedef Pair<A, B> = {
  fst: A,
  snd: B
};

enum Maybe<A> {
  Just(a: A);
  Nothing;
}

~~~

Monstrous Names!
----------------

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

Programming with values
-----------------------

* We write programs by _combining_ values
* Functions are values
* Programs are values

a -> a -> a
-----------

Combine two values of the same type.

~~~{haxe}

typedef Semigroup<A> = {
  append: A -> A -> A
}

~~~

Abstraction!
------------

~~~{haxe}

function foldMay<A>(xs: Array<A>, s: Semigroup<A>): Maybe<A> {
  return if (xs.length == 0) {
    Nothing;
  } else {
    var acc = xs[0];
    for (i in 1...xs.length) {
      acc = s.append(acc, xs[i]);
    }
    acc;
  }
}

~~~

Semigroup
---------

* An operation that combines two values of the same type.
* f :: a -> a -> a

* ... which is associative
  * f (f a0 a1) a2 = f a0 (f a1 a2)

* Why is associativity important? 
  * It allows us to reorder operations and achieve the same result,
    so long as the order of the operands is preserved.

Monoid
------

* Semigroup with a zero
* f :: a -> a -> a
* z :: a
  * f (f a0 a1) a2 = f a0 (f a1 a2)
  * f (z a0) == a0
  * f (a0 z) == a0

Composition
-----------

* (.) :: (b -> c) -> (a -> b) -> (a -> c)

Kleisli
-------

* (>=>) :: Monad m => (a -> m b) -> (b -> m c) -> a -> m c

