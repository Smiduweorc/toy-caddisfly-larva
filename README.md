# [WIP] toy-caddisfly-larva
This library is still a WIP and of course is nowhere done, I have just decided to make this repo public just because.

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

### Boundaries
As usual, I will draw the line in the sand and tell what this library is not.
- This is not a throughput contender. If someone benches this against ArkType (I really like the arktype project), on "validate ten thousand flat objects as fast as possible", this will lose, and I am absolutely fine with that. It will never be competitive by design, only be a pure happy accident, because I can forsee that this is unlikely one.
- Async Refinements (or lackof) This isn't going to have async refinements. the moment a validation rule needs a round network trip, `pending` gets a second, incompatible meaning, "pending becase the value isn't here" vs "pending the server is not done, pls wait". Reconciling those is a much bigger design problem than I want to take on before the core idea even proves itself. Explicitly deferred, not quietly ignored.
- No coercion (for now), Whatever comes in is validated as what it claims to be. I might regret this, but who knows?
- No i18n for error-message ecosystem. I care about the shape of an error (which node, which state, why), not about supplying a hundred pre-translated copies of "this field is required." Someone else can build that layer on top if the shape is good enough to bother
- Not importing or exporting JSON Schema. Interesting problem, not this problem.
- Not handling non-JSON types. No `Date`, no `Map`, no `Set`, no `binary`. If a real use case demands one of these later, it gets added deliberately, not by accident.
- Not trying to be a drop-in Zod replacement. No promise that switching is one import away. If the ergonomics end up similar in places, that's because good ideas converge, not because compatibility was a goal.

### Standards
If you have been looking at some of my past libraries, you may have noticed that I occasionally reference specific RFC standards in my work. I don't always lean to those RFC standards, but if they are something I feel is a concern, then of course, I'll add that compliance in. That said, some standards I wanna follow are:
- [StandardSchemaV1](https://standardschema.dev/) I'll implement ~standard.validate() as the one-shot fallback interface. This is table stakes, not a feature. It means the weird session-based core can still slot into tRPC, form libraries, whatever, on day one.
- [JSON Patch (RFC 6902)](https://www.rfc-editor.org/info/rfc6902/) and [JSON Pointer (RFC 6901)](https://www.rfc-editor.org/info/rfc6901/) as the mutation format for incremental revalidation, instead of inventing my own patch grammar. If something already emits JSON Patch, I want it to work here for free.
- The AI SDK's partial-object streaming convention as the shape for streamed input, since that's fast becoming the default way partial JSON shows up in the wild for tool-calling. Adding a third competing "partial JSON" convention to the world felt like a bad use of anyone's time, including mine.

Beyond those three, there's no existing spec for "incremental validation session" that I'm aware of, which either means there's something real here, or it means everyone else already figured out why this is a bad idea and didn't write it down. I'd like to find out which. Once the core stabilizes, I want to write a small `SESSION_SPEC.md` of my own, defining Session, Node, ValidationState, and the exact contract `feed()` and `patch()` have to honor. Which is the same move Standard Schema made, just scoped to this one concern.

### Open questions I don't have answers to yet
Writing these down so future-me can't quietly pretend they were decided:

- Do patches have to arrive in order? What happens to a pending node if a later patch invalidates something an earlier one depended on?
- Which schema node types are allowed to be pending at all? A leaf string probably can't be partially valid. A .refine() across two sibling fields definitely can be, for longer than feels comfortable.
- Should I write my own streaming JSON tokenizer, or wrap an existing partial-JSON parser and put the schema/session logic on top? My instinct is the tokenizer is a whole separate project and I should not let it eat this one.
- Is "streaming" and "incremental patching" actually one mechanism, like I'm assuming above, or does trying to force them into the same abstraction make both of them worse? I won't know until there's real code.

### Lore and art
So caddisfly larvae are aquatic, worm-like insects, and are sometimes also known as the bagworms of the water. This is because the larva of caddisflies share a similar trait where they will construct a case out of materials in their surroundings. However, here are some differences between how the both of them constructs things.

- caddisfly larvae primarily use minerals instead of wood (varies species to species, some almost always trim wood)
- caddisfly larvae cases are open on both ends so that water can flow through
- caddisfly larve measures my touch and fit, it rotates the pebble with its legs and test it against the geometry of the body, while a bagworm cuts the length of the wood

The rock measuring thing is a huge part as to why this library is named after the caddisfly larvae. Because we can't change the shape of the data that is passed to us, but we can choose whether to accept or reject that passed data.
