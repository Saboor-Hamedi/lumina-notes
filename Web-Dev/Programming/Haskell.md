---
id: a1b2c3d4-1087-4000-8000-000000000087
title: Haskell
language: markdown
tags: [web-dev, programming, haskell, functional]
selection: null
isPinned: false
timestamp: 1781500001087
---
# Haskell

Haskell is a purely functional, statically-typed programming language with lazy evaluation and strong type inference.

## Core Principles

| Principle | Meaning |
|-----------|---------|
| Purity | Functions have no side effects |
| Lazy evaluation | Values computed only when needed |
| Strong static types | Compiler catches most errors |
| Type inference | Types are rarely explicit |
| Immutability | All values are immutable |
| Monads | Structured effect management |

## Basic Syntax

```haskell
-- Function definition (types inferred)
factorial 0 = 1
factorial n = n * factorial (n - 1)

-- Explicit type signature
add :: Int -> Int -> Int
add x y = x + y

-- Higher-order functions
map (*2) [1, 2, 3]       -- [2, 4, 6]
filter even [1..10]       -- [2, 4, 6, 8, 10]
foldl (+) 0 [1..100]      -- 5050

-- Pattern matching
describeList :: [a] -> String
describeList [] = "Empty"
describeList [_] = "One element"
describeList _ = "Many elements"
```

## Algebraic Data Types

```haskell
-- Product type (AND)
data Person = Person String Int  -- name + age

-- Sum type (OR)
data Shape = Circle Float | Rect Float Float | Triangle Float Float Float

-- Parameterized type
data Maybe a = Nothing | Just a
data Either a b = Left a | Right b

-- Recursive type
data Tree a = Leaf a | Node (Tree a) (Tree a)

-- Using pattern matching
area :: Shape -> Float
area (Circle r) = pi * r ^ 2
area (Rect w h) = w * h
```

## Type Classes

```haskell
-- Defining a type class
class Equal a where
    (===) :: a -> a -> Bool

-- Instance for Int
instance Equal Int where
    (===) = (==)

-- Deriving
data Color = Red | Green | Blue deriving (Show, Eq, Ord)

-- Common built-in type classes
-- Functor: fmap :: (a -> b) -> f a -> f b
-- Applicative: <*> :: f (a -> b) -> f a -> f b
-- Monad: >>= :: m a -> (a -> m b) -> m b
```

## Monads in Practice

```haskell
-- IO Monad (side effects)
main :: IO ()
main = do
    putStrLn "Enter your name:"
    name <- getLine
    putStrLn $ "Hello, " ++ name ++ "!"

-- Maybe Monad (nullable computations)
lookupAge :: String -> [(String, Int)] -> Maybe Int
lookupAge name db = do
    entry <- lookup name db
    return (snd entry)

-- Either Monad (error handling)
divide :: Float -> Float -> Either String Float
divide _ 0 = Left "Division by zero"
divide x y = Right (x / y)
```

## Laziness

```haskell
-- Infinite lists
fibs = 0 : 1 : zipWith (+) fibs (tail fibs)
take 10 fibs  -- [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]

primes = sieve [2..]
    where sieve (p:xs) = p : sieve [x | x <- xs, x `mod` p /= 0]

-- Only evaluates what's needed
take 5 $ filter (\x -> x > 100) [1..]  -- evaluates instantly
```

## Build Tool: Stack/Cabal

```haskell
-- project.cabal
name: myproject
version: 0.1.0.0
build-depends: base >= 4.16, text >= 1.2, aeson >= 2.0
exposed-modules: MyProject
```

**Links**: [[Functional Programming]] | [[Scala]] | [[TypeScript]] | [[Algorithm Paradigms]] | [[Concurrency Models]]
