What is an Applicative?
=======================

Motivation
----------

An applicative is a functor with two extra "methods", ``pure`` and ``<*>``.
A functor is great in that it allows us to "decorate" types and apply normal functions across these decorated types, but we may soon run into a problem.

.. code:: haskell

   data MyFunctor a = MyFunctor a
                    deriving Show

   instance Functor MyFunctor where
      -- fmap :: (a -> b) -> MyFunctor a -> MyFunctor b
      fmap f (MyFunctor x) = MyFunctor $ f x

   main = do
      putStrLn $ show $ fmap (+1) (MyFunctor 1) -- Use a "normal" increment function on this functor!!
      -- -> MyFunctor 2
      putStrLn $ show $ fmap (+) (MyFunctor 1) (MyFunctor 2)
      -- ERROR!! Functors can only lift functions a -> b (one parameter)

We are at a standstill because we cannot apply a function that takes multiple parameters, here ``(+)``, two functor values.
Luckily, as we will see, this is where applicatives come in.

Definition
----------------------

.. code:: haskell

   class Functor f => Applicative f where
      pure :: a -> f a
      (<*>) :: f (a -> b) -> f a -> f b

Applicatives require an existing functor ``f`` and two new "methods":

- ``pure`` which "wraps" a expression into the applicative in the "purest" or most minimal way
- ``<*>`` which defines how to apply a function in the applicative to values

In practice, this is generally used when a monad is too much but a functor is not enough for the desired behavior.

Example Continued
-----------------

.. code:: haskell

   data MyFunctor a = MyFunctor a
                    deriving Show

   instance Functor MyFunctor where
      -- fmap :: (a -> b) -> MyFunctor a -> MyFunctor b
      fmap f (MyFunctor x) = MyFunctor $ f x

   instance Applicative MyFunctor where
      -- pure :: a -> MyFunctor a
      pure x = MyFunctor x
      -- (<*>) :: MyFunctor (a -> b) -> MyFunctor a -> MyFunctor b
      (MyFunctor f) <*> (MyFunctor x) = MyFunctor (f x)

   main = do
      putStrLn $ show $ fmap (+1) (MyFunctor 1) -- Use a "normal" increment function on this functor!!
      -- -> MyFunctor 2
      putStrLn $ show $ fmap (+) (MyFunctor 1) (MyFunctor 2)
      -- ERROR!! Functors can only lift functions a -> b (one parameter)
      putStrLn $ show $ fmap (+) (MyFunctor 1) <*> (MyFunctor 2)
      -- -> MyFunctor 3
      putStrLn $ show $ (,,) <$> (MyFunctor 1) <*> (MyFunctor 2) <*> (MyFunctor 3)
      -- -> MyFunctor (1,2,3)
