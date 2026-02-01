---
date: 2026-02-01T21:00:00+01:00
slug: addendum-on-applicative-monads
categories:
  - math
  - programming
tags:
  - category theory
  - haskell
title: "Addendum to monads and applicative functors"
---

In [my post on monads and applicative functors]({{% ref
"/posts/applicative-functors" %}}), I used the term of
"lifting to the internal hom" as the extra structure
needed to make a monad an applicative functor.

As it turns out, this is [better known as an *enrichment on the
functor* or, even more generally, *tensorial
strength*](https://ncatlab.org/nlab/show/tensorial+strength#RelationToEnrichedFunctors).
An enrichment on a functor may only be defined for monoidal closed
categories, whereas tensorial strength may also be defined for
any odd monoidal category.

This goes to show how much structure we take for granted in the
category of sets.
