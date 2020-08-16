+++
title = "Calculus of Cooking"
[taxonomies]
categories = ["logic", "cooking"]
+++
Aka the algebra of ingredients.


I had a funny thought I wanted to write down- recipes (like, cooking) are a bit like
logic. This was inspired by alegbraic data types and interpreting logic
through computation. Obviously the real world is messy, especially cooking,
but there is a certain pattern here to be observed.


There is an empty recipe, which does nothing, so we have a 0/False.


There is a recipe which contains every ingredient, although it is not
particularly useful. We would not use this often, but there is at least
a concept of 1/True.


There are an infinite number of ingredients possible that can be listed,
pairing them up together and getting us a notion of "\*".


There can be selection between ingredients, giving a notion of "+", such
as gluten free replacements for flour.


Mapping from ingredient to ingredient is a bit like a function, which can
be composed, have multiple inputs (or tupled inputs), and multiple outputs
(or tupled outputs). We can bind intermediate results to names for use later,
although there is a bit of a linear, or affine, logic thing going on
where we can decide not to use something, but can't duplicate it.


We can get more into logic and quantify over ingredients, such as P(a),
where 'a' is an ingredient and P restricts us to "sugars", for example.
We might have a function P(a) -> b, creating a 'c' out of any ingredient
that satisfies the property "P". We might say "for all ingredients,
given that the ingredient is a sugar, pour in 1 cup". Or we might
not say this.


Universal and existential quantification may have some meaning here,
although I can't think of what it would be. Perhaps there is a
constructive interpretation where we can talk of ingredients without
knowing exactly what they are (thinking of universal quantification
like polymorphism in programming), or existential quanitification
as an ingredient (which we may not know what it is), although with
instructions that verify that it has a property.


This mix a bunch of concepts- logic, abstract algebra, and type theory -
all bundled up into a confusing, but perhaps tasty, pile.


Anyway, just a thought.
