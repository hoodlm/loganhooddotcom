---
layout: post
title: "Four months of Rust"
excerpt: "I started learning Rust in October 2023, a few hours a week. This post shares some of the things I've done that have worked well for me."
---

> Authentic Rust learning experience involves writing code, having the compiler scream at you, and
trying to figure out what the heck that means.
>
> -- [Gankra](https://faultlore.com)

I started learning Rust in October 2023 - mostly on my own time, a few hours a week.
I've done a mix of tutorials and self-guided work to advance from a beginner to an
early-intermediate Rust programmer. This post shares some of the things I've done
that have worked well for me.

### Why Rust?

Most of my programming experience has been in high-level interpreted languages, like Java and Ruby.
I wrote some C/C++ in the past, but nothing that I'd consider serious software engineering with those languages.
The ability to write fast, native software has been a gap in my programming toolbox for a long time.

Rust seems like a good choice to fill that gap. I don't have the expertise to form this opinion on my own yet,
but as far as I can tell, Rust is in a class of its own for writing fast programs without requiring the programmer
to manage memory.

There's a lot of industry momentum behind Rust right now. Adoption in high-profile projects like the Linux Kernel makes
it look like it's here to stay.
At my own employer, Rust is popping up more and more in greenfield projects. Even
if my job responsibilities don't involve writing many lines of Rust, at the very least I need to be able
to read partner teams' Rust code and understand what it's doing.

# Official resources

[rust-lang.org/learn](https://rust-lang.org/learn) provides three 'Getting Started' resources for beginners:
The Book, Rustlings, and Rust By Example. I've got value out of all three.

## The Book

The Book, or [The Rust Programming Language](https://doc.rust-lang.org/stable/book/index.html), was where I spent
most of my time for the first few weeks learning Rust. Each chapter is a loop of reading about a language feature,
then typing some code samples to explore that feature.

It's an excellent resource and was the core of my learning curriculum for my first month or so of Rust.
As a programming language book - a how-to book - it is a strictly *guided* learning resource. I had to be disciplined
about actively engaging with the material, rather than falling into copy-and-paste zombie mode.

After chapter 12 or so, I started spending less time with the Book and more time on *unguided* learning, writing code
on my own. I visit the Book for the later chapters as I encounter a need to read them.
When I started needing to use data structures beyond Vec and HashMap, I read the Ch. 15 on Smart Pointers.
When I started getting tripped up with Traits, I worked through Ch. 17, Objected Oriented Programming Features of Rust.

## Rustlings

[Rustlings](https://github.com/rust-lang/rustlings) is an interactive set of exercises to learn Rust - currently
96 exercises in total! Each exercise is a partially-completed Rust program that needs some fixing up - either it is failing to compile
or is failing some unit tests. In each exercise, my job is to add a small bit of code - sometimes just a single keyword - to fix the program.

I used Rustlings and The Book in parallel - where Rustlings was a review mechanism for reinforcement learning.
The exercises are quick, but I spaced them out, doing just a few exercises a week. Sometimes I'd cover things in Rustlings a
few weeks after I first saw them in The Book.

#### Protip: read Rustling hints, even if you don't need a hint!

Rustlings provides optional hints for most exercises.
Even if I solved a particular exercise without hints, I still found it helpful to read the hint afterwards.
For example, I solved the 'errors2' exercise like this:

```rust
let qty_result = item_quantity.parse::<i32>();

match qty_result {
    Ok(qty) => Ok(qty * cost_per_item + processing_fee),
    Err(_) => qty_result,
}
```

...which *works*, but the hint reminds me that the `?` operator is a more concise, idiomatic way to do the same thing:

```rust
let qty = item_quantity.parse::<i32>()?;

Ok(qty * cost_per_item + processing_fee)
```

## Rust By Example

I didn't use [Rust By Example](https://doc.rust-lang.org/rust-by-example) for guided learning, but instead as a handy reference material.
It is great for situations like "Hey, how does if-let work again?" and I want to see some concrete code samples.

# Other guides

## Too Many Lists

["Learn Rust With Entirely Too Many Linked Lists"](https://rust-unofficial.github.io/too-many-lists) is a fun one. The
author expresses disdain for Linked Lists, then proceeds to walk through increasingly sophisticated ways to
implement Listed Lists in Rust. (The point here is that Linked Lists are rarely the best data structure
to use in practice - for the vast majority of us, the only practical utility of Linked Lists is implementing them for educational purposes!)

I really like the author's philosophy throughout the tutorial. The quote I used to open this post is from
the introduction to this guide:

> It should be noted that the authentic Rust learning experience involves writing code,
> having the compiler scream at you, and trying to figure out what the heck that means.
> I will be carefully ensuring that this occurs as frequently as possible. Learning to
> read and understand Rust's generally excellent compiler errors and documentation is
> incredibly important to being a productive Rust programmer.

I stumbled on this guide early on, but it looked too advanced when I was brand new, so I bookmarked it for later.

I came back to it when I was stuck at a progression plateau. I needed to implement an `.iter()` function on one of my
own structs; and the process of imlementing the Iterator trait was a violent confrontation with intermediate topics like generics,
lifetimes, and ownership/borrowing/moving. I understood some of the basics of these topics from The Book and Rustlings,
but in the unguided setting of writing my own code, I kept digging myself into a deeper hole of compiler errors that I didn't
know how to fix the right way.

As of writing this post, I've completed the first three Linked Lists in the guide: "bad stack", "OK stack", and "persistent stack".
As promised, this guide put me through many of the same compiler errors that I had previously struggled with on my
own. But, now I had someone to walk me through the right way to work through these; a concrete example of how to build an
Iter; along with some other insights that made some of these intermediate-level language topics click for me.

One thing to keep in mind is that this guide is a few years old. I didn't run into any specific problems; just know
that some compiler errors might be worded differently or occur in a different order.
If you want to use the same version of Rust as this guide was written for,
you can change your `Cargo.toml` to specify `edition = "2018"`.

# Doing things on my own

### Easy, unguided problem solving

I have been working through problems in the [Rosalind problem set](https://rosalind.info/problems/list-view/) every now and then.
Project Euler or Leetcode would work just as well. The goal here is just to have some easy problems to work on in an unguided fashion.

### Some Rust at my day-job

In December, I volunteered to take on a sprint task to write some Rust code in a package owned by another team,
It wasn't a particularly technically-challenging or ambiguous task. But it gave me experience with my company's
Rust build toolchain, got me working in a codebase that someone else wrote, and gave me an opportunity to write
Rust code for someone else to review.

### Toy compiler

In January, I started working on a interpreter/compiler, which I'm writing in Rust. Compilers are
notoriously hard software to write - so this is a great exercise in *learning by doing things wrong
the first time*.

This is where I have run into most of my organic compiler-screaming-at-you scenarios, the type that the Too Many Lists
guide helped me learn to solve.

The important thing is that I have a persistent codebase that I'm working on week-to-week,
not a short-lived throwaway codebase to work on for a single day to solve a single problem.
When I make poor choices, I have to deal with refactoring and fixing those structural issues later.

# What's next?

* My main mission is to continue implementing my toy compiler, to complete a v0.1 release milestone.
* I have a few specific Rust topics I haven't touched yet, in particular Concurrency.
* Besides my toy compiler project, I want to also branch out "beyond the terminal"
and try out some Rust web application and graphics frameworks.
