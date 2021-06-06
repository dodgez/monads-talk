What is a Functor?
==================

Rudimentary Definition
----------------------
A functor is a (nice) mapping between categories that preserves arrows and compositions.
Specifically, if we have two categories :math:`C,D`, a functor :math:`F` is a mapping between each category such that for :math:`f:a\to b`, there exists :math:`Ff:Fa\to Fb`.
Another way to think of a functor is an "image" of the category :math:`C` *inside* the category :math:`D`.

Constant Functor
----------------
Nothing in the previous section prohibits one from defining a functor :math:`F:C\to D` to map every object to the exact same object :math:`d\in D`.
This is called a constant functor and despite appearing not useful, comes up in quite a few places.
There is a functor that does this for *every object* in :math:`D`.

Example
--------
Let's say we want to store user information so we use a record type (think object in javascript or dictionary in python)

.. code:: haskell

   data UserInfo = UserInfo {
      name :: String
      , age :: Int
      }
      deriving Show
   main = putStrLn $ show $ UserInfo { name = "Zachary Dodge", age = 99 }
   -- -> UserInfo {name = "Zachary Dodge", age = 99}

Suppose we use this record in our code but later find out the user's age may not always be supplied.
Let's create a new data type to encode this possibility:

.. code:: haskell

   data Nullable a = Present a
                   | Null
   data UserInfo = UserInfo {
      name :: String
      , age :: Nullable Int
      }
   main = putStrLn $ show $ UserInfo { name = "Zachary Dodge", age = Null }
   -- -> UserInfo {name = "Zachary Dodge", age = Null}

Great, but now anytime we want to use this age (or in general, this Nullable data type), we need to check if it is null before proceeding.
Here is slightly contrived example of this difficulty:

.. code:: haskell

   -- putStrLn :: String -> IO ()
   main = do
      let info = UserInfo { name = "Zachary Dodge", age = Present 99 }
      let can_drink = case age info of
            Present age -> Present (age >= 21)
            _ -> Null
      case can_drink of
         Present True -> putStrLn "Here is your free beer"
         Present False -> putStrLn "You are not old enough to drink"
         _ -> putStrLn "I don't know how old you are"

Looking at this code, we notice: ``can_drink`` has type ``Nullable Boolean`` and the first case expression is essentially just ``age >= 21`` but changed to work with the ``Nullable`` type.
Is there a way we could remove the "boiler-plate" code to keep this code clearer?

The answer to this question is **yes**, we can make ``Nullable`` a *Functor* which will allow us to skip this case expression and therefore write cleaner code.
Here is a (simplified) definition of a functor in Haskell.

.. code:: haskell

   class Functor f where
      fmap :: (a -> b) -> f a -> f b

In the case of ``Nullable``, we want to say how an arbitrary function :math:`f:a\to b` can be *lifted* (transformed) to work with a Nullable input and output.
This amounts to the following:

.. code:: haskell

   instance Functor (Nullable a) where
      -- fmap :: (a -> b) -> f a -> f b
      fmap _ Null = Null
      fmap f (Present a) = Present $ f a
   
   main = do
      let info = UserInfo { name = "Zachary Dodge", age = Present 21 }
      let can_drink = fmap (\age -> age >= 21) (age info)
      case can_drink of
         Present True -> putStrLn "Here is your free beer"
         Present False -> putStrLn "You are not old enough to drink"
         _ -> putStrLn "I don't know how old you are"

It turns out there are other data types that are functors:

- ``List a`` (or ``[a]``) where ``fmap :: (a -> b) -> List a -> List b`` is defined by ``fmap _ [] = []`` and ``fmap f (x:xs) = f x : (fmap f xs)``
- ``Identity a`` where ``fmap :: (a -> b) -> Identity a -> Identity b`` is defined by ``fmap f (Identity a) = Identity $ f a``
- ``IO a`` the data type that represents input output (real-world programming)
