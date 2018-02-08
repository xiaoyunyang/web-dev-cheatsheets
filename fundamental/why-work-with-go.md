# Writing a server with Go
![Go Gopher](https://raw.githubusercontent.com/ashleymcnamara/gophers/master/GOPHER_LEARN.png)

## What is Go
[Go](https://github.com/golang/go) is a programming language created by Google. It started as an experiment by some engineers, to develop to address the pain points of  C++ and Java without compromising performance and capability that those languages provides. Other than a well-thought design, it has some specific features for concurrency like a type of light-weight processes called goroutines.

In [Tomassett's blog article](https://tomassetti.me/best-programming-languages/) on best articles for each situation, he writes:

> All its [Go's] authors expressed a dislike for C++ complexity. So, in some way, it is a language designed to persuade C people to enter the new century. 

## Why You Should Go with Go

1. It's [easy to learn](https://movio.co/en/blog/migrate-Scala-to-Go/).
2. It let's you do concurrency without the overhead.
3. It's type safe.
4. It's fast.


## Features of Go

* Easy to learn - [See this article](https://movio.co/en/blog/migrate-Scala-to-Go/)

**What is concurrency and why we care**



## Go Offers Best Balance

Tradeoffs Programmers Need To Make and How Go 

[Tomassett's blog article](https://tomassetti.me/best-programming-languages/) on best articles for each situation discusses when to use different languages. I'm going to discuss design patterns for each situation.

**Code reusability vs. code scalability** 

Object oriented programming (OOP) make sense for enterprise software because they don't change that much. In OOP behavior is hard coded in abstract classes but is configurable to some extent during construction. This promotes better code reuse, which saves developers a lot of time upfront.  However, if you expect your code to extend more capability later and have to revise your design many times in the future, then OOP will end up hurting developer productivity and make the code highly coupled with the environment and untestable.

are often conflicting design goals but they don't have to be.

**Performance/Correctness vs rapid development**
Languages like C, C++, and Java offer great performance but takes long to develop and compile time ramps up when your code grows. These languages often provide type safety features such as static type checking at compile time, which contributes to compile time.

Type checking at compile time is a useful feature to prevent type errors (inconsistencies). Languages that support dynamic typing doesn't help you catch type errors and let these type errors manifest in runtime errors, or worse, incorrect or inconsistent behavior during runtime, which would result in many hours of debugging.

Having to compile before you can run your code can really hurt rapid development. That's why languages like Ruby and Python are popular with developers concerned with making something quick. 
![xkcd compiling](https://imgs.xkcd.com/comics/compiling.png)

But Ruby and python are notorious for being slow.

