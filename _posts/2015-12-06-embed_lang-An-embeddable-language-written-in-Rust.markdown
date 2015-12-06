---
layout: post
title:  "embed_lang - An embeddable language written in Rust"
---

I have been working on a programming language written in [Rust][] for a while. Though this may be done for fun and learning I figured it would be a good idea to do write something about its goals and features as I feel like things are actually starting to fit together.

## io.print "Hello world"

The language, called [embed_lang][], follows the "tradition" of my parser combinator library of having a very literal working name ([combine][] was called parser-combinators in early versions). As the name implies its primary intent to is to fill a similar role as languages like [Lua][]. That's where the big similarities end however. embed_lang is a quite different language and borrows its syntax and semantics mainly from the ML family of languages, even including a Hindley-Milner based type system!

[embed_lang]:https://github.com/Marwes/embed_lang
[combine]:https://github.com/Marwes/combine

## type Static = { are_useful: Bool } in embed_lang

Embeddable languages usually go for a dynamic type system, and for good reason. Embedding a language in an application provides increased flexibility at run-time and flexibility is the home turf of dynamic languages. Adding type checking (and type inference) also increases the size of the compiler which would ideally be very small to avoid bloating the application it is embedded in.

While the reasons for using dynamic types are good I figured that an embeddable language with a static type system would be interesting to explore. With static type checking you get the usual benefits of some automatic documentation and verification at compile time at the expense of some flexibility. Though losing some flexibility is unfortunate I believe these extra checks will not only be useful when working in the language itself but it can also be used to define safer, clearer and more efficient APIs between host and embedded language.

How well it actually works remains to be seen but I think it is interesting to explore which is half the reason to do it anyway.

## if insert code blog_post then Ok () else Err ()

As mentioned embed_lang is decidedly functional in nature which can also be seen in its syntax which borrows Haskell and Ocaml. That being said the syntax is very likely to change in the future so I don't want to put to much focus on it.

With hello world already having snuck its way into this post the next simplest example would be a factorial function.

    // Explicitly import the operators we need
    let { make_Ord, ord_Int, num_Int } = prelude
    and { (+), (*), (-) } = num_Int
    and { (<) } = make_Ord ord_Int
    in
    // Calculates the factorial of `n`
    let factorial n : Int -> Int =
            if n < 2
            then 1
            else n * factorial (n - 1)
    in factorial 10

And its maybe twice as long as the same function written in just about any other language, I do have an excuse for that though.

To keep the language simple there is currently no form of overloading. This in conjunction with having both integers and floats makes us unable to have the operators for both types in scope at the same time. As I have yet to decide on a solution for this [problem][easy ops] I have opted to not treat any of them specially until the best alternative has been decided on. It will need to be handled in some way however as needing to import basic arithmetic operators is certainly not the friendliest introduction.

[easy ops]:https://github.com/Marwes/embed_lang/issues/10

Skipping past the importing the syntax is noticeably inspired by [Haskell][] with a few elements borrowed from [Ocaml][] to avoid needing an indentation aware parser. I will admit it's not the most consistent choice but at the moment it is more import to have a language which is simple to parse rather than nailing down the final syntax.

A more complete use of the current syntax can be found in the [prelude module][]

[prelude module]:https://github.com/Marwes/embed_lang/blob/master/std/prelude.hs

## case semantics of | Sound -> "Good!" | Unsound -> "Uh oh"

Syntax may be pretty to look at but what makes or breaks a language has more to do with the semantics. As embed_lang is functional at heart it naturally has first-class functions and algebraic data types (or enumeration types in Rust). It also has first-class records which makes for some very flexible and descriptive code as it is possible to create composite types without even needing to declare them first leading to some very dynamic feeling code.

Currently the language is pure and strict though the purity is more or less incidental as I have simply no impure functions in the current implementation. It is unlikely purity will stick as a feature though as most languages are impure and given it wants to work together with its host language, purity may be to restrictive. In any case nothing assumes purity to work correctly so impure functions could be loaded today even without running into any problems.

Complementing the language itself a nice API for interacting with the virtual machine are be needed. What currently exists is a raw interface to interacting with the stack similarily to Lua but built on top of this is an easier to use API which automatically marshals values from and to the host language. It unfortunately relies on a few transmutes which I would like to remove but have so far been unable to. Hopefully this potential unsafety can be fixed in the future.

Past the virtual machine I also want to expose the other parts of the compiler so that each part (parser, type checker etc.) could be used independently. Not only could this be useful for writing tooling (if one were to dream a bit) but it would also make it possible to only include the virtual machine which should give a really small binary size as long as it is enough to only run pre-compiled bytecode.

## you >>= read_post >>= (\feedback -> me.improve_posts feedback)

So thats the short tour of embed_lang. I'd love to get some feedback on what you think may work, whats interesting and on what will simply crash and burn. I have a couple of ideas for other posts about the language as well as more Rust centric posts so if anything in particular in this poist seems interesting please let me know in the [Reddit post][] or by email.

[Lua]: http://www.lua.org
[Haskell]: http://www.haskell.org
[OCaml]: http://www.ocaml.org
[Rust]: http://www.rust-lang.org

[Reddit post]:https://www.reddit.com/r/rust/comments/3vns8b/embed_lang_an_embeddable_language_written_in_rust/
