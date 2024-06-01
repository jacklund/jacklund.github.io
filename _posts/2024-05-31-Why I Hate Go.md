---
layout: single
title:  "Why I Hate Golang"
tags:
  - programming
---

Over the years, I've used many programming languages, starting with Fortran and Algol (yes, I'm _that_ old), up through C, C++, Java, and, these days, Python and Rust. As a general rule, I don't have strong opinions about whether this language is better or worse than another. Mostly, I just use what's right for the project - if it's a Java shop, I use Java, if it's a fairly quick one-off, I'll maybe use Python or Ruby. Languages are tools, and I use what I consider to be the right tool for the job.

Then, I stumbled across Go.

My first jump into Go was when we were using Hashicorp Consul at work - a good product, written, as is all Hashicorp stuff, in Go - and I came across something which seemed like a decent feature to add. I can't remember the exact details, this was a few years ago, but I remember that it involved, from what I could tell, adding two new fields to a struct. "That should be easy and forward-compatible" I thought.

What I didn't realize at the time, was that fields on structs were public by default. What this meant was, rather than having an initializer which hid the field details and which would make my job easier, the struct gets initialized "by hand", i.e., you set all the fields when you need to use it. "Welp", I thought, "No big deal. I'll add the fields where they're needed, shouldn't be too many places".

By the time I got to my twenty-fifth test file, I decided it really wasn't worth the time I was putting into it.

Now, some might say that it was the Consul developer's fault for making those fields public, but, in my experience, if a language gives you a gun that's pointed at your foot, a lot of developers are going to just pull that trigger.

Since then, I've attempted to use Go to do things, always good to learn a new thing, and every single time, I've hated it, not because I couldn't get the project done with Go, but because using Go is such an unpleasant experience, especially for a programming language from this century. So, I've decided to stop complaining and actually put down in words what I hate so much about Go.

## The Name

Look, I understand, maybe better than most, that naming things, especially for programmers, is difficult. But..."go"? Besides being kind of undescriptive (do they mean the verb, the board game?), it makes it almost impossible to search for on the Internet (sure, you can search for "golang", which makes things easier, but...really?). I mean, I guess it's understandable, since one of its [main authors](https://en.wikipedia.org/wiki/Ken_Thompson) wrote programming languages that he named "A", "B", and, finally, "C" (please note that I'm not denegrating Ken Thompson's work, but simply his naming conventions). 

## The Syntax

One of the things that's nice about programming languages is that, barring aberrations like Lisp-like languages (which have a good reason for their syntactic eccentricities), most programming languages tend to use very similar syntax. For instance, a class method in Python looks like:

```
Class Foo:
  def bar(a: string):
    ...
```

Admittedly, you don't know what it's supposed to return, but it's easily understandable.

Java:

```
public class Foo {
  public String bar(String a) {
  	...
  }
}
```

Rust:

```
struct Foo {}

impl Foo {
  fn bar(a: String) -> String {
  	...
  }
}
```

and then, there's Go:

```
type Foo {}

func (f *Foo) Bar(a string)(string, error) {
	...
}
```

To begin with, this is significantly different than typical, [ML](https://en.wikipedia.org/wiki/ML_(programming_language))-type programming languages. It's also not clear from first glance what's going on. I mean, you can figure it out, but why the weird syntax? To make the compiler happy? Just to be different? Honestly, just from a readability and aesthetic viewpoint, this really is not great.

Let's look at how you declare a map of, say, string to int:

```
map[string]int
```

Even more fun, say you see this as the declared type of an argument:

```
map[string]interface{}
```

This is basically a map from string to...well, anything. Or, almost anything. What it really means is "anything that the called function can handle". Which brings me to my next beef.

## Type Safety

Go is not a type-safe language. They don't even claim that it is. It's a "statically typed" language, which simply means that your types that you use have to be explicitly defined (unlike, say, Python). Technically, `map[string]interface{}` _is_ statically typed - the `interface` type is strictly defined as "any type with an interface", but, in practice, what does that mean? If I have a type `Foo` which the called function doesn't know about, but is an interface type, does that count? How do I know, looking at that "map string interface", whether what I pass in will work or not?

## Data Encapsulation

I already talked about this briefly above, but, honestly, data encapsulation is a thing. An important one. Making fields public by default is a surefire way to introduce unintentional coupling into your code, a lesson I thought we had all learned about 30 years ago. 

## Error Handling

This is maybe my biggest problem with Go. You look at any Go code, and you see a lot of code like this:

```
result, err := DoFirstThing()
if err != nil {
	// handle error
}

result2, err := DoSecondThing()
if err != nil {
	// handle error
}
```

and so on. Besides the fact that this looks a LOT like 1980's-era C code (I wonder why), this is problematic for the following reasons:

1. It's very easy to forget to do the `if err != nil` boilerplate, especially when you're doing it over and over and over again
2. There's absolutely no guarantee that the function you're calling is only returning an error, or a result, but not both, or neither (i.e., a nil err value and nil for the result). 

Honestly, this being, again, well into the 21st century, we _shouldn't be returning errors and results as separate things_. I get that the Go developers didn't want to do exception handling - I'm not against it myself, but I get the argument. But, returning errors and results  as separate things is actually much worse and much more dangerous than dealing with exceptions. You're giving your caller absolutely no guarantees that they'll always only get one or the other, other than the vague hope that the called function is following convention. Rust handles this by returning an `enum` type, which means that it contains either a result type, or an error type, but not both, and they have the powerful `match` syntax and `?` operator to deal with error handling, and the type safety (actual type safety, not just static typing) means that you can't possibly accidentally use one instead of the other.

## Code Aesthetics

This is something that obviously is "in the eye of the beholder", but it's also important. Code that is pleasing to read is also usually easier to understand, maintain, and find bugs in. When you look at Go code, you see lots of "call function - if err is nil handle error - if result is nil do something, else do something else - call next function - ...". Honestly, it's just tiring and monotonous to read, and if it's monotonous, then it's very easy to overlook something. Go doesn't have a "match" syntax, because the "if then else" paradigm is really all you can do, but...it's just so awful, and imposes a lot of cognitive load on the reader. 

## Conclusion

Honestly, we can do better. A lot better. I know Go has goroutines, and channels, which are really nifty, and easy to use, but, honestly, most languages nowadays have all the same constructs. I'd rather use a core language that was a joy to use and easy to look at, understand and debug, but which might have less straightforward concurrency constructs. Using a badly-designed language with great concurrency just means you'll get to your bugs faster.
