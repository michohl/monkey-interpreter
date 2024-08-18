# Writing an Interpreter in Go

This repo is my journey going through the book [Writing an Intrpreter in Go](https://interpreterbook.com/) by Thorsten Ball.

## What is Monkey

Monkey is a simple programming language that does not have an official implementation.
It is purppose made for Thorsten Ball's books. More details can be found about it here: https://monkeylang.org/

Below is what is implemented in _this_ implementation of the language.

## What is From the Book and What are Your Ideas

I've followed the book diligently from start to finish and did not stray at any point. Once
completed I have created a `1.0` release to denote that everything up to that point was
"as the author intended".

Anything beyond version `1.0` can be recognized as my own ideas and implementations.

## Did You Like the Book

I'm assumming if you're even looking at this repository you're researching whether or not
the book in question is worth buying. I would say it was a worthwhile read from my perspective.

The author has a very easy to read style of writing that I appreciated and I enjoy the learning
style of having your hand held while going on a very verbose tour of a code base. The twist
being that instead of just looking at an existing code base you're creating it from scratch.

I would note that part of why I found the verboseness tolerable was because I downloaded a copy
of the repo that the author instructs you to download for reference. This allowed me to copy
and paste the _lengthy_ test suite in as needed and spend more focus on the actual code that
mattered.

I personally hand typed the first few tests to get an idea of the methodology then
once I felt I was bored by it I started copying and pasting.

## How To Run the Code

The interpreter is just a "REPL" (Read, Evaluate, Print, Loop) and is meant to be used
interactively like so:

```console
$ go run main.go
Hello michohl! This is the Monkey programming language!
Feel free to type in commands
>> let x = {"foo": "bar", "zed": "cool"}
>> x["zed"]
cool
>>
```

## What is Implemented

### Literals

These are the building blocks of the language. Everything evaluates down to literals in the end.

#### Strings

Strings are combinations of characters surrounded with double quotes (`"`).

```console
>> let x = "foo"
>> x
foo
```

#### Integers

Nothing noteworthy. Just simple integers. They can be positive or negative

```console
>> let x = 2
>> x
2
```

#### Booleans

Booleans are "truthy" in Monkey. This means if the value is not explicitly `false` or `NULL`
then we will evaluate it as a `true` value.

```console
>> let x = true
>> x
true
>> let y = 1 > 0
>> y
true
```

#### Identifiers (variables)

We can store the result of expressions into variables for reference later.
We accomplish this by using the `let` keyword followed by a variable name `=` and an expression.

```console
>> let x = 1
>> x
1
```

#### Arrays

Arrays (also called lists) are a simple sequence of preserved order elements.

```console
>> let x = [ 2, 3, 1, 6, 9 ]
>> x
[2, 3, 1, 6, 9]
```

We have some "built in" functions for interacting with arrays that are mentioned later on.

#### Hashes

Hashes are often referred to as "maps" or "dictionaries" in other languages. In Monkey we allow
for mapping any "hashable" type to any value. Hashable types are Strings, Integers, and Booleans.

```console
>> let x = {true: "foo", "one": 1, 2: "two"}
>> x[true]
foo
>> x["one"]
1
>> x[2]
two
```

Indexes are also evaluated expressions so we can do more complex indexing like so:

```console
>> x[1 > 0] # 1 > 0 == true
foo
```

#### Functions

Functions in Monkey are treated the same as all other values which means Monkey supports higher
order functions!

```console
>> let square = fn(x) { return x * x }
>> square(8)
64
```

Supporting higher order functions means we can pass functions as arguments to other functions.

```console
>> let square = fn(x) { return x * x }
>> let wrapper = fn(x, y) { return x(y) }
>> wrapper(square, 6)
36
```

#### Errors

"Errors" in Monkey are simply wrappers around strings. We assign them a type so we can "handle"
them when we encounter them but they are just strings that we can use to present errors to the
caller.

```console
>> x = 5
            __,__
   .--.  .-"     "-.  .--.
  / .. \/  .-. .-.  \/ .. \
 | |  '|  /   Y   \  |'  | |
 | \   \  \ 0 | 0 /  /   / |
  \ '- ,\.-"""""""-./, -' /
   ''-' /_   ^ ^   _\ '-''
       |  \._   _./  |
       \   \ '~' /   /
        '._ '-=-' _.'
           '-----'
Woops! We ran into some monkey business here!
 parser errors:
        no prefix parse function for = found
```

#### NULL

Null is not fully leveragable in our implementation of monkey. We don't allow it for regular
use in program space but it is leveraged under the hood. So it is not possible to set
a variable to null directly like so:

```console
>> let x = null
ERROR: identifier not found: NULL
```

But if you have an expression that doesn't return anything then it _could_ return null like so:

```console
>> let x = if (1 > 2) { 10 }
>> x
null
```

### Expressions

Expressions refer to chunks of code that need to be further evaluated to result in literals.

#### If/Else

If else statements are run of the mill compared to other languages.

```console
>> let x = if (1 > 0) { "hit" } else { "miss" }
>> let y = if (1 < 0) { "hit" } else { "miss" }
>> x
hit
>> y
miss
```

#### Infix Expressions

Infix epxressions are operators that come between two other types and takes action with
the two of them to produce a new result.

##### Sum

```console
>> let x = 2
>> let y = 3
>> x + y
5
```

##### Subtraction

```console
>> let x = 3
>> let y = 2
>> x - y
1
```

##### Multiplication

```console
>> let x = 2
>> let y = 3
>> x * y
6
```

##### Division

```console
>> let x = 6
>> let y = 3
>> x / y
2
```

##### Concatenation

Combine two strings

```console
>> let x = "hello, "
>> let y = "world!"
x + y
hello, world!
```

##### Comparisons

Monkey implements `>` and `<` for comparing integers and `==` and `!=` for all others types.

> [!CAUTION]
> The current implementation does not handle comparisons well when you
> start to try and compare complex literals like arrays. It will compare the pointer
> value of the objects instead of their values.

###### Greater Than

```console
>> let x = 1
>> let y = 2
>> x > y
false
>> y > x
true
```

###### Less Than

```console
>> let x = 1
>> let y = 2
>> x < y
true
>> y < x
false
```

###### Equality

```console
>> let x = 3
>> let y = 3
>> let z = 6
>> x == y
true
>> x == z
false
```

###### Inequality

```console
>> let x = 3
>> let y = 3
>> let z = 6
>> x != y
false
>> x != z
true
```

#### Prefix Expressions

Prefix expressions are operators that can be put in front of an expression to change
it's final meaning.

> [!TIP]
> Prefix expressions can be "stacked" to do things like "double negatives"

##### Negation (integers)

You can negate integers by prefixing them with a `-`

```console
>> let x = 5
>> -x
-5
```

##### Negations (booleans)

You can reverse the meaning of a boolean by prefixing it with a `!`

```console
>> let x = true
>> !x
false
```

### Built In Functions

Built in functions are the functions we have implemented in the language Monkey is written in
(go) and exposed to the user. Some are for convenience and some are necessary for the language
to actually be usable.

> [!NOTE]
> Any built in functions that seem to "take action" on an array are **not mutative**.
> They will simply return a new list rather than implicitly modify the original.

#### len

`len` is a simple "length" function which takes an array or string and returns the length of it.

```console
>> let x = [1,2,3,4,5]
>> len(x)
5
```

#### puts

`puts` is our `print` equivalent. This is how we allow the user to write to the screen.

```console
>> let x = [1,2,3,4,5]
>> puts("one", x, "two")
one
[1, 2, 3, 4, 5]
two
null
```

#### first

`first` is a helper function that takes an array as input and returns the first element.

```console
>> let x = [2,3,4,5]
>> first(x)
2
```

#### last

`last` is the inverse of `first` and takes an array as input and returns the last element.

```console
>> let x = [2,3,4,5]
>> first(x)
5
```

#### rest

`rest` is a helper function that is more useful for functional programming which takes an
array as input and returns everything except for the last element in the array.

```console
>> let x = [1,2,3,4,5]
>> rest(x)
[2, 3, 4, 5]
```

#### push

`push` is a helper function that takes an array and a new value as input and returns a copy of
the original array with the new element appended to the end.

```console
>> let x = [1,2,3,4,5]
>> push(x, 8)
[1, 2, 3, 4, 5, 8]
```
