What is Haskell?
================

People often put Haskell down saying "it's only an academic language" or "what is a monad".
They often experience confusion or distaste when they see Haskell code.

Let's briefly look at common scripting tasks we may wish to use and we will see how terse Haskell can be.

.. code:: haskell

   -- Comments are preceded by two hyphens
   -- Say Hello
   main :: IO () -- This type definition says 'main' does IO and returns nothing ('()' is unit)
   main = putStrLn "Hello, World!"

.. code:: haskell

   -- Relevant type definitions:
   -- readFile :: FilePath -> IO String
   -- putStrLn :: String -> IO ()
   -- For now don't worry that the output of readFile doesn't match the input of putStrLn
   main = do
      contents <- readFile "test.txt"
      putStrLn contents

.. code:: haskell

   import Data.List.Utils (replace)

   -- Replace replaces occurrences of the first argument with the second argument in the last argument
   -- replace :: String -> String -> String -> String
   -- writeFile :: FilePath -> String -> IO ()
   main = do
      contents <- readFile "test.txt"
      writeFile "test.txt" $ replace "OldWord" "NewWord" contents
      -- '$' is shorthand syntax to force precedence of the right expression (evaluates first)

.. code:: haskell

   -- Create a new algebraic data type (A list in this case)
   -- Made up of pairing a value of type 'a' with a list of type 'NList a' (imagine single linked list)
   data NList a = NCons a (NList a)
               | NNil
               deriving Show

   -- We "derived" (got for free) the typeclass Show so we can print this new type
   main = putStrLn $ show $ NCons "Hello" (NCons "there" NNil)
   -- -> NCons "Hello" (NCons "there" NNil)

   -- Here is a conversion routine to normal lists:
   convertNListList :: [a] -> NList a
   convertNListList NNil = []
   convertNListList (NCons x xs) = x : convertNListList xs

   -- Here is the counterpart conversion
   convertListNList :: [a] -> NList a
   convertListNList [] = NNil
   convertListNList (x:xs) = NCons x (convertListNList xs)

   -- We can make a "better" instance of Show
   instance Show a => Show (NList a) where
      show list = show.convertNListList list
   main2 = putStrLn $ show $ NCons "Hello" (NCons "there" NNil)
   -- -> ["Hello", "there"]
