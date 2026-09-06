# toy-caddisfly-larva
A simple validation library for values that constantly move.

![caddisfly-larva, but with a pineapple](https://raw.githubusercontent.com/Smiduweorc/toy-caddisfly-larva/refs/heads/master/assets/logo.png)

> attribution for the transparent pineapple: https://www.vecteezy.com/free-png/pineapple Pineapple PNGs by Vecteezy

### Why this exists
I have been working with a few typescript validation libraries now, which is fine, but I noticed that they are all really good at the same thing, taking a fully formed value, and they hand you back a valid or invalid status. That said, I started encountering issues when working on problems that involved streaming.
The issue is that the value is "still arriving" and as a result is not yet fully formed/built. I have also never seen a validator that can "oh yeah this is changing on the spot, let me revalidate this without throwing everything away.". Perhaps the existing libraries and ecosystems don't handle this or even try to do so because it is a bad idea. I shall find out myself.
This is not an attempt to replace anything and you should quite frankly avoid using this library. This was made for fun and curiousity.

### Mechanics
If I had to best describe the mechanics of this library it would be that it answers with "Given a value I was already validating and something just changed about it, what's still valid and invalid? And what is still unknown?"

That third state, unknown, but not enough info is the whole reason why this really intrigues me. I have seen this become an issue when working with UIs, file-streaming, LLM outputs or anything involving the need to validate the stream content of sorts.
This also introduces Incremental revalidation, as a side effect, as in that "I have a document that is already validated, but my user has changed just 1 line of a paragraph, I don't want to revalidate the whole document or paragraph, just the line they added.".

While they are not exactly the same, I feel that they are just the same problem wearing 2 different hats.

### Further ideas
- Perhaps this validator could be seen as "built around a session" instead of a function call. You can open a session against a schema and you will feed it events, you then ask it questions about the current state.
- A validator where `pending` is a real outcome
- A validator with an explicit dependency graph between schema and nodes, computed once when the schema is built, so a change in one field can be translated into "here is the exact set of other things that might now need rechecking" instead of "check everything again, we are fast enough so it is fine."

### Lore and art
So caddisfly larvae are aquatic, worm-like insects, and are sometimes also known as the bagworms of the water. This is because the larva of caddisflies share a similar trait where they will construct a case out of materials in their surroundings. However, here are some differences between how the both of them constructs things.

- caddisfly larvae primarily use minerals instead of wood (varies species to species, some almost always trim wood)
- caddisfly larvae cases are open on both ends so that water can flow through
- caddisfly larve measures my touch and fit, it rotates the pebble with its legs and test it against the geometry of the body, while a bagworm cuts the length of the wood

The rock measuring thing is a huge part as to why this library is named after the caddisfly larvae. Because we can't change the shape of the data that is passed to us, but we can choose whether to accept or reject that passed data.
