# Writing an Interpreter in Go

This repo is my journey going through the book [Writing an Intrpreter in Go](https://interpreterbook.com/) by Thorsten Ball.

## How To Run the Code?

The interpreter is just a "REPL" (Read, Evaluate, Print, Loop) and is meant to be used
interactively like so:

```console
$ go run main.go
Hello michohl! This is the Monkey programming language!
Feel free to type in commands
>> let x = {"foo": "bar", "zed": "asdf"}
>> x["zed"]
asdf
>>
```

## What is From the Book and What are Your Ideas?

I've followed the book diligently from start to finish and did not stray at any point. Once
completed I have created a `1.0` release to denote that everything up to that point was
"as the author intended".

Anything beyond version `1.0` can be recognized as my own ideas and implementations.

## Did I Like the Book?

I'm assumming if you're even looking at this repository you're researching whether or not
the book in question is worth buying. I would say it was a worthwhile read from my perspective.

The author has a very easy to read style of writing that I appreciated and I enjoy the learning
style of having your hand held while going on a very verbose tour of a code base. The twist
being that instead of just looking at ane existing code base you're creating it from scratch.

I would note that part of why I found the verboseness tolerable was because I downloaded a copy
of the repo that the author instructs you to download for reference. This allowed me to copy
and paste the _lengthy_ test suite in as needed and spend more focus on the actual code that
mattered.

I personally hand typed the first few tests to get an idea of the methodology then
once I felt I was bored by it I started copying and pasting.
