---
layout: fable-blog-page
title: Introducing Fable 2.0 beta
author: Alfonso García-Caro
date: 2018-08-03
author_link: https://twitter.com/alfonsogcnunez
author_image: https://github.com/alfonsogarciacaro.png
# external_link:
abstract: |
  Fable 2? Really? I want to try it out! NOW!
---

## TL;DR

- **Fable 2? Really? I want to try it out! NOW!** The easiest way is to check [the new and shiny repl](http://fable.io/repl) for quickly running and editing samples online. To test the new tooling in your machine, we have prepared two samples: [one with minimal configuration](https://github.com/fable-compiler/fable2-samples/tree/master/minimal) if you prefer to start with the basics; and [a full-blown app](https://github.com/MangelMaxime/fulma-demo) that includes most extensive configuration and code.

> Note [the old repl](http://fable.io/repl-legacy) is still alive in case you want to compare the different outputs.

- **Is Fable 2 any better?** Two is bigger than one, [so it has to be!](https://www.youtube.com/watch?v=uMSV4OteqBE&feature=youtu.be&t=80) Jokes aside, there's still room for improvement and we haven't done extensive benchmarking yet, but early findings show you can get [between 30-40% bundle size reduction](https://github.com/MangelMaxime/fulma-demo/pull/5#issue-190709436) as well as [up to 2x speed boost](https://github.com/fable-compiler/Fable/issues/1457#issuecomment-406901930) in complex computing-intensive apps (like Fable REPL itself).

- **Are there breaking changes?** We've tried to reduce them as much as possible but there are a few, particularly in JSON serialization. See below.

- **Is this ready for production?** No! Yes! Maybe. I don't know. It's basically passing all the tests from Fable 1 and a few more. Some of us are already using it in production, but obscure regressions may remain to be found. Please help us to iron them out during the beta phase and release a great stable version.

- **So will you be fixing immediately all the bugs? In fact, I'm reporting one at this very moment.** Well... I'm going on vacation! So I may be a bit slow to respond in the next couple of weeks, sorry for that!

## A bit of history

On January, I had an online interview with [the Community for F#](https://www.youtube.com/channel/UCCQPh0mSMaVpRcKUeWPotSA). There I said that Fable was entering its third year which would become the consolidation year, where we wouldn't touch so much the compiler and focus instead in ecosystem, tutorials, etc. One week later [we started some discussion](https://github.com/fable-compiler/Fable/issues/1318) to change the representation of types to reduce the size of the generated JS. Two weeks later I started work on Fable 2 with this purpose. Three weeks later I thought maybe this was a good opportunity to clean the code and started rewriting the whole code base... Yeah, I know, programmers.

## New design choices

Moving six months forward (as usual longer than initially planned) we're finally very proud to announce the launch of Fable 2 beta! As mentioned above, the original intention was to compile records as plain JS objects and unions as arrays (similar to what [Bucklescript](https://bucklescript.github.io/) does). However, at the end this hasn't been possible because we couldn't keep the full F# semantics this way (equality, `obj` method overrides, etc). Still we've managed to make types lighter, mainly because they don't include reflection info any more.

One of the original design decisions in Fable was to output readable and standard Javascript. This involved using ES2015 classes to represent types, avoid name mangling, reproducing module hierarchy, etc. However, we've realized this is not always desirable because the way of programming F# is not the same as programming JS, and we were creating some anti-patterns that didn't work well with JS tooling like bundlers or minifiers.

Fable 2 now will convert most of the methods (be it class or nested module members) into functions of the JS file module. This works much better with tree shaking and minification, though it involves more name mangling and somewhat diverges from the original goal of readable output. However Fable 2 performance results indicate it's worth it.

> Interfaces (and in some cases abstract classes) are still the main way to interact with JS code so abstract methods won't be mangled. Please avoid overloads in interfaces.

We will go deeper about the differences in code output between Fable 1 and Fable 2 with examples in another post.

## Structural changes in Fable's code base

I was going to write here about changes in Fable's code base, but I'm running out of time so I'll leave that for the next post too ;)

## Breaking changes

- **Reflection**: Not actually a breaking change as it still works basically the same, only reflection info is not attached to every type by default any more, instead it's generated by the compiler where needed (which should be transparent to most users). One side effect of this is `PassGenericsAttribute` is not needed any more and it's been marked as obsolete.

- **PojoAttribute**: This attribute was mainly intended to define `props` in React components, but same as `PassGenerics` it was kind of forking the F# language and changed the semantics of the type. So we've decided to turn it off in the beta. In our tests, the last version of React works just fine with Fable 2 records. Please give it a try and let us know if you've problems.

- **JSON serialization**: This was the most controversial thing in the discussions about Fable 2. JSON serialization was included in Fable.Core to make it easier for users to share types with the server, but now there are good alternatives and it was time to remove it. DON'T BE SCARED! Thanks to Maxime and Zaid, you still have auto-serializers to make the transition much easier, check [Thoth.Json](https://mangelmaxime.github.io/Thoth/json/v2/decode.html#auto-decoder) and [Fable.SimpleJson](https://github.com/Zaid-Ajaj/Fable.SimpleJson).

## Acknowledgments

We had many great contributors during Fable 2 development, so I apologize in advance if I'm forgetting someone. If you like the improvements, please thank ncave and Maxime Mangel who have put a lot of work in this new release. But also Valery Vitko, Ilja Nosik, Zack Young, Zaid Ajaj and Aleksander Heintz.

And of course not only the compiler, the ecosystem arount it is very important to keep Fable alive, so big kudos from her to Eugene Tolmachev, Diego Esmerio, humhei, Kunjan Dalal, Stachu Korick, and the [SAFE-stack](https://safe-stack.github.io/) team!

I also want to thank my employers, who trust Fable (and me!) and are really supportive to keep its development active: [Ben Taylor](https://twitter.com/bentayloruk), [Robert Kuzelj](https://twitter.com/kuzrob) and [Demetrix](https://demetrixbio.com/).

<br />

That's all for today. Please give Fable 2 a shot - we've put a lot of love in it and hope you like it. Really excited to see what you will build with it!

Have a nice weekend and lovely Summer!