What is a Monad?
================

Motivation
----------

Now we may be thinking that applicatives are all we need.
However, there are still cases where we may wish to have further structure.

Take, for example, the case where we have an applicative ``OurApplicative``.
Since all applicatives are functors, we can apply arbitrary functions ``a -> b`` to a value of type ``OurApplicative a`` to get a value of type ``OurApplicative b``.
Since this is an applicative, we can also apply functions ``OurApplicative (a -> b)`` to ``OurApplicative a`` or apply functions with multiple parameters via ``fmap (f :: a -> b -> c) OurApplicative x <*> OurApplicative y``.

The case we now may wish to solve is when we have a function like ``f :: a -> OurApplicative b`` but notice the reverse case is not particularly interesting because you can just evaluate normally, i.e. ``(f :: OurApplicative a -> b) OurApplicative x``.
This is fine if you have a value of type ``a`` but we may only have a value of type ``OurApplicative a``.
Essentially we want to "unwrap" the value from the applicative and apply it to this function.


Definition
----------

A monad is an applicative with one more method:

.. code:: haskell

   class Applicative m => Monad m where
      (>>=) :: m a -> (a -> m b) -> m b

According to this definition, the function ``>>=`` takes a "wrapped" value ``m a`` and a function from the underlying type to another wrapped type ``m b`` and applies the function.
Monads are often used for side effects and sequential application and with this perspective, the ``>>=`` function can be viewed as a way to sequence these.

Examples
--------

.. code:: haskell

   -- Pun not intended
   checkAge = do
      age <- Just 99
      if age < 21 then Nothing else Just "Here's a beer!"

.. code:: haskell

   main = putStrLn "Hello, World!" >>= (\x -> putStrLn "World")
   -- readLn :: Read a => IO a
   main = (readLn :: IO Int) >>= (\x -> putStrLn $ show $ x)

   -- Do notation is very helpful
   main = do
      x <- readLn :: IO Int
      putStrLn $ show $ x

Built-in Instances
------------------

- ``[a]``
  - ``pure x = [x]``
  - ``fs <*> xs = [f x | f <- fs, x <- xs]`` (applies every function to every input)
  - ``xs >>= f = concatMap f xs`` (maps the function over all inputs and concatenates them together)
- ``Maybe a``
  - ``pure x = Just x``
  - ``Just f <*> Just x = Just (f x)`` and ``Nothing`` otherwise
  - ``Just x >>= f = f x`` and ``Nothing`` otherwise
