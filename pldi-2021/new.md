What has changed since the last arXiv draft? What have we learned?

# New Examples

`vector T n` and `Fin.t n -> T`, useful for expressing matrices (one for efficiency, one for easier proofs).
One direction so far, other in progress.
Got this from Anders.
James helped.
Has iota.
Interestingly, equivalent up to the assumption of functional extensionality.
Manual configuration.

Adding constructors.
Extended the REPLICA case study.
Uses algebraic ornaments.
Split into new and old information.
Kind of weird.
Sometimes more proof obligations than without tool support, but makes the new proof obligations very explicit.
More automation is needed.
Still working on getting back nice proof terms.
Talking to Conor about this.
Automatic and manual configurations combined (so far just automatic).

Swapping hypotheses.
Got this from Anders.
This shows an example where a configuration exists, but it doesn't remove the old type until you simplify.
Manual configuration.

And this is in arXiv already, but the I to J example from a POPL reviewer is new since POPL.

Arbitrary equvialence (reuse only).

# Intuition

A better way to explain the configuration is that it is essentially a deconstructed equivalence that makes the transformation easy.
That is, we want to provide enough information to show that the two types are equivalent.
But this alone isn't enough, given that we sometimes want to repair proofs and not just reuse them.
In particular, for repair, once we get rid of the old type, the functions in the equivalence will not exist anymore.

So what we do is we express a particular equivalence to PUMPKIN Pi by breaking it into pieces.
The idea is to port constructors of A to constructors of B, and eliminators over A to eliminators over B.
But for non-inductive types, this is more of an analogy than anything else.
We further align these constructors and eliminators with each other so that the transformation doesn't have to think about
low level details like swapping or adding arguments.

So the first part of the configuration tells the transformation how to do this.
But this sometimes breaks equality!
Consider `nat` and `Bin.nat`, or consider `vector T n` and `Fin.t n -> T`.
The idea being these have different enumeration orders, so if you just port the eliminator directly,
reduction behavior that used to be definitional is now propositional.

So to deal with ths, we add two more things to the equivalence: `eta` and `iota`.
The idea here is to port definitional equalities to propositional equalities when the inductive structure changes.
First expands any types we don't eliminate over, like `Sigma` types when we eliminate over projections rather than use `sigT_rect`.
Second describes reduction behavior of eliminator.
Together these describe how to port equalities, and this is what lets us substitute in the new eliminator for the old one.

# Scope

In fact, every equivalence does induce a configuration.
We have a proof of this in Coq.
However, we are still missing the proof about the eliminators being equal up to transport.
Furthermore, the default configuration, even though it works, does not remove references to the old type.
So, every equivalence induces a configuration suitable for reuse, but not necessarily for repair.
(Haven't yet found a concrete example not suitable for repair, though.)

To handle repair, we additionally need to be able to essentially _reduce_ the functions in the equivalence _ahead of time_,
so that we can produce things that don't refer to the old type in any way.
This lets us avoid using section and retraction explicitly inside of the eliminator and iota.
I don't know when this is possible in general, but typically we need to know something about how the types are constructed to do this.
If we consider the hypothesis flipping example, we could construct both from an A, or from T3---in one case we get
something suitable for only reuse, and in the other case for reuse and repair.
That example is weird, though, and maybe hard to understand.
For inductive types this is easiest to see: we basically reduce the equivalence function to compute the dependent constructors over B.
For an arbitrary equivalence, though, we don't know any way to construct B other than through `f : A -> B` of the equivalence,
thus there is no way in general to remove references to `A`.

OK, so that's one direction.
In the other direction, we have strong intuition that every configuration induces an equivalence, though we don't yet
prove it in general or in manual cases (we do in automatic cases).
The intuition is similar to what we had before, modulo a few changes (see mistakes below).
This intuition forms an algorithm which we use manually in a bunch of examples, including the general equivalence configuration.
Just needs automation.

Note that even though we can get back and forth, don't know if we have an equivalence between them.
Don't know if round-trip.

# Mistakes

We need a proof that `eta` is extensionally equal to identity.
This shows up in the proof of the induced equivalence.

The proof of the induced equivalence needs iota in both directions, not just one.

# Presentation

We should show a bunch of examples in a table, complete with some useful information like:
* manual vs. automatic
* reuse vs. repair
* other reuse tools that handle this
* other repair tools that handle this
* old type and new type
* description
* source of benchmark

We'll need to trim the paper for PLDI.

# Framing

The introduction doesn't claim anywhere that we subsume a bunch of other repair work, but we do.
Zach had an interesting framing idea for this about it being less ad-hoc or whatever.
I think the best thing to do here might be to include the table in the introduction, then reference it again later.
But may be hard to pull off.

# Tactics

RanDair is working on some more second pass automation, once he finishes the refactor.
This may help with the presentation of tactic proofs in the paper.




