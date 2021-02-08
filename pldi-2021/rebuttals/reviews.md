PLDI 2021 Paper #43 Reviews and Comments
===========================================================================
Paper #43 Proof Repair Across Type Equivalences


Review #43A
===========================================================================

Overall merit
-------------
C. I would not accept this paper but will not argue strongly against
   accepting it.

Reviewer expertise
------------------
Y. **Knowledgeable** = "I follow the literature on this paper's topic(s)
   but may have missed some relevant developments"

Paper summary
-------------
This paper considers the problem of how to automatically repair proofs written in an interactive proof assistant (here Coq) due to changes (primarily inductive) type definitions in which a type definition is changed resulting in an equivalent type definition. The authors propose a tool called Carrot. Carrot can automatically search for an equivalence that holds between the two types, then applies a transformation to the old proof term to transport it across the equivalence to the new type, before finally decompiling the resulting proof term into a tactic script. 

The authors present Carrot's design in detail as well as describing its use on a number of small case studies.

Comments for author
-------------------
Proof repair is becoming an increasingly important sub-field of nascent discipline of proof engineering. In this sense the paper addresses a timely topic about which there has not yet been a great deal of research. 

The overall approach employed by Carrot is interesting and it is clear that its design is the result of a lot of careful thought and trade-offs. It is also clear that Carrot is a work in progress. 

One persistent question that the paper left unanswered is to what degree the current implementation of Carrot reduces effort for proof maintenance, which is the end goal here. The authors present a range of case studies---some simple and others more involved---to which Carrot is applicable--some requiring little or no manual intervention while others requiring some unspecified but non-trivial amount of human labour. Yet the basic question of whether it would have been less effort to do a manual proof repair is not considered.

Were this an ITP paper being presented as a work in progress then it would be fair to defer that question, as the contribution could be squarely evaluated in terms of the technical details of Carrot's design and implementation. However, for a PLDI paper, I believe that that comparison deserves to be made and the case for a reduction in effort presented.

As an example, the authors consider the case of adding a constructor to a datatype. They explain how Carrot can be nicely applied to this by proving an equivalence between the new type and a sum of the old type plus the new constructor. Yet one wonders immediately whether it would have been faster to simply add the extra cases to each of the old proofs. Clearly the effort for the latter is a function of how many and the size of existing proofs that are broken by the change. So a natural question to evaluate then would be: how much existing broken proof do I need to have for Carrot to save me effort? That answer is non-obvious especially given that Carrot's decompiler at present produces proof tactics that sometimes require the human to manually fix anyway, and so it would also suffer from similar scalability concerns as doing a manual proof repair. 

Another negative aspect of the paper for this particular reviewer was that it was not sufficiently self-contained for non-Coq experts. I have written many tens of thousands of lines of interactive proofs but almost none of that was in Coq. I am intimately familiar with the need for automated proof repair, having worked on extending and modifying very large scale interactive proofs. Yet much of the middle of the paper remained opaque or harder to understand than necessary because a few key concepts were not explained. 

For example, the use of Constr and Elim in CIC_\omega. They are explained simply as "inductive constructors" and "primitive eliminators" only in the caption of Fig 6. But the role for  natural number indices i is not. One begins to infer its intention later on when we meet DepConstr and DepElim, but such inference should be unnecessary. 

Likewise the paper mentions the word "refine" on a couple of occasions e.g. line 693-694. Which definition of refinement the authors are referring to was not clear to me. Is this some notion of data refinement? Or something else? Some refinement notion specific to the calculus of inductive constructions?

The paper would be strengthened by taking the time to explain these concepts, to improve its accessibility to outsiders. 

Another shortcoming of the paper for me was a lack of discussion about related ideas in non-constructive provers. For instance, Isabelle/HOL's transfer package comes to mind, which is part of its quotient types implementation. (see e.g. Huffman and Kuncar, 2013).

Another aspect of the paper that I feel does it a disservice is that many of the key ideas are presented over simplified core languages/settings. In this setting the key ideas appear quite trivial: e.g. the transport rules in Fig 9 or the decompilation rules in Fig 13. Of course the much more difficult part of the problem (and where the approach will ultimately succeed or fail) is when those ideas are applied to Coq. At present the paper presents the simple case first and then explains in words how the simple ideas need to be extended to apply to Coq and to full Ltac etc. Yet that explanation is given much less space; yet it is arguably at least as important. 

This gives the somewhat misleading impression that the task is simple by design (wheres of course it is not, which is why for instance Carrot's decompiler is necessarily imperfect for instance). 

One way to avoid that would be to take more time up-front before presenting the simple setting to first note the problems that have to be dealt with in full Coq. Then say that you are boiling it down to a simple setting to present the key ideas, and then return to those larger problems and how Carrot attempts to solve them.

Overall this is solid, well motivated work. Yet the lack of empirical effort evaluation (even an informal one) and the current presentation reduce the appeal of this paper to outsiders. 

Some more minor comments follow:
- line 235 and 236: it might be worth explaining the significance of the vertical bar | to non Coq readers who are otherwise prone to miss the change here.
- line 361 mention that the transformation generalises that of Devoid (and again later on) I never really got a sense of how much more general Carrot's transformation and equivalences are. Some intuitive examples would help to strengthen the paper here. Table 1 helps to make this clearer later on but foreshadowing here could be useful.
- Figure 8: the definition of binary numbers here is unfathomable until we see its explanation much later in the paper.
- Many terms are assumed knowledge throughout Section 4 e.g. definitions vs propositional iota-reduction (line 517). A sentence to explain each would greatly improve the accessibility and thus value proposition of this section to outsiders
- line 803 was the first time it became clear to me that the decompiler can "break" proofs, i.e. turn a working proof term into a non-working tactic script. That should be explained (and justified why this is a sensible design choice) much earlier
- line 849 reads like an uninished sentence (I could not parse it)
- Section 6.2 I did not understand why you could not write your own specialised induction etc. tactics that behave the way Qtac assumed they would. Possibly this is obvious to Coq users.



Review #43B
===========================================================================

Overall merit
-------------
A. I will champion accepting this paper.

Reviewer expertise
------------------
X. **Expert** = "I have written a paper on one or more of this paper's
   topics"

Paper summary
-------------
The paper shows an approach and tool implementation for semi-automatically repairing proofs after changes to type. The main underlying mechanism is type-equivalence, that is, the user, or in some cases the tool, specifies how the new type, or parts of it, are equivalent to the old type, which enables transporting proofs from old to new type. This is demonstrated in Coq by rewriting proof terms and then inferring new tactic scripts from these proof terms for enabling the proof engineer to maintain and further modify the proofs on the new type. 

Examples of transformations contained in the paper: turning lists into packed vectors, adding length to vectors, turning tuples into records, permute constructors, add constructors, factor out constructors, permute type hypotheses, change inductive structure.

Comments for author
-------------------
This is cool work and a tool I've been looking for in every-day proof maintenance for many years.  There is more work to be done, but this is already great, and I especially appreciated the attention to practical proof engineering work and impact.

My main criticism in terms of publication would be the evaluation: it's a standard formal-method tool evaluation, which is "we built a tool to do x, and look it does x well", but it does not evaluate the stated purpose of the tool, which here is to increase productivity, and decrease maintenance work (i.e. was x the right tool to build?). To evaluate that, you should compare to two kinds of baseline: a) what is the theoretical best you could achieve, e.g. everything or almost everything fully automatic (not really achievable, but it clearly can't get be surpassed), and b) what is the current state, e.g. how much work do you have to do manually without the tool. This would be true for a qualitative (roughly as now) as well as a quantitative evaluation (e.g. a study on how much time/work is actually saved, could be small or large). Without these two comparisons you can't really see the impact the tool makes and how far there is still to go.

The results can be surprising, in the sense that I have seen in the past tools that clearly helped in personal experience and that engineers liked to use, but did not actually save much time. That doesn't need to mean the tool is bad, but maybe it does other things than we think we should claim (e.g. maybe it reduces cognitive overhead so you can ultimately cover more complexity or you need less expertise to do the same thing).

This may be too much to ask for this present paper, and at least most would likely agree that this tool is "clearly" useful, but I'd recommend looking at a more thorough evaluation for the future. Most software engineering and FM papers don't do this (and it often needs expertise we tend to be not that great at and might even involve ethics approvals etc), which means there are lots of unsubstantiated claims about tools published that make for battles of opinion instead of data and evidence. It'd be nice if proof engineering would not start out at the same level.

Don't get me wrong: the case study section in the paper is great, it helped to bring home the generality and applicability of the method. What I'm asking above is evaluation in addition to that, not instead of it. 

In terms of generality of the work: it seems that the basic idea might generalise to LCF provers without proof terms. At least Isabelle implements a lemma transfer mechanism for quotient types, which is reminiscent of the transformation which happens at the proof term level in your work, including the provision/generation of equivalence relations (see [1]). It sounds like some of this infrastructure could maybe help with a similar tool in that world. I'm not sure this helps much with this paper, but it might be worth hooking up with someone from the Isabelle community to discuss this more. I'm sure a tool like this would be well received there too, and it would boost the generality of the general approach.

Finally, there are two points in the paper where you excluded something for lack for space. Both were things I was immediately curious about, so if there is more space for the final version, including a slightly longer summary at least would help readers like me.

[1]: Brian Huffman, Ondrej Kuncar, Lifting and Transfer: A Modular Design for Quotients in Isabelle/HOL. CPP 2013: 131-146



Review #43C
===========================================================================

Overall merit
-------------
B. I support accepting this paper but will not champion it.

Reviewer expertise
------------------
Y. **Knowledgeable** = "I follow the literature on this paper's topic(s)
   but may have missed some relevant developments"

Paper summary
-------------
Formal proofs, like programs, are not "dead": they are "updated" or "evolved" as we 
want to refactor or extend or improve the objects (e.g. programs) we're proving 
properties about. This paper describes a way to automatically update or repair 
proofs of theorems when the underlying representation of a particular datatype
is changed to another (equivalent) datatype. Examples include renaming 
data constructors (e.g. `emp` to `nil`), reordering or removing constructors 
(from `nil` and `cons` to `cons` and `nil`), to more complex ones which refactor 
constructors (e.g. from `Plus exp exp` and `Minus exp exp` to `op exp exp` where 
`op` is `Plus` or `Minus`) to even more complex ones like changing how numbers 
are represented from the usual unary/peano (`zero` and `succ`) to binary.

This paper describes a method for automatically updating proofs that works as follows:

1. The system (CARROT) infers a "configuration" that maps the correspondence/equivalence 
   between the old and new type;

2. The "configuration" is used to repair the underlying (low-level) proof-term;

3. The repaired proof term is *decompiled* into a (plausible) high-level tactic 
   script (that would have generated that term), thereby providing source-level 
   updates to the user's proof.

The method is evaluated on various case studies that illustrate the wide variety
of type-refactoring supported by the tool.

Comments for author
-------------------
Pros

* The paper tackles an important problem that has not received as much attention as it should!
  Most verification work tackles "proofs" as a goal to reach, but in reality, broader adoption 
  will require ways to support longer term "software engineering" as tackled here

* I especially appreciate the attention to repairs at the _tactic_ level (which is crucial for 
  the updates to be maintainable)

* The parts where it describes how the user interacts with the tool are very well written 
  with lots of examples even readers not familar with the internals of Coq .

Cons

* While the paper makes a strong argument that "type equivalences" are not super restrictive 
  (i.e. not just simple things like renaming / permuting constructors) the space of repairs 
  here is nevertheless narrow;

* The specifics of the algorithms used, e.g. to repair proofs and decompile proofs to tactics 
  require deep familiarity with the internals of coq-style provers, and may be best appreciated 
  at a more focused venue like ITP or CPP;

* The evaluation would be much stronger with more evidence from large, real-world proof developments.

I enjoyed reading this paper -- in general, I found the vision of 

1. guessing configurations, 
2. patching proofs, 
3. decompiling to tactics

very interesting, and likely quite broadly applicable!

On the flip side, I confess I could not follow the implementation of each of those steps as well
as I would like. For example, Fig. 10 Step 4, mentions both `list` (and `Old.list`) but I thought 
a key part (lines 350-356) was that 

    "CARROT implements transport in a way that allows for removing references to (the old type) A"?

I suspect I'm missing something very basic, but would be grateful for a clarification.

A crucial part of CARROT is the ability to decompile proof terms back up to source level proof scripts.
The section describing this is quite terse, and I am not sure how well this would work in general.
In particular, it seems the idea is to decompile into a "predefined set of tactics" -- this would work 
for relatively simple proofs that use those tactics, but how does this generalize to fancier proofs that 
may, for example, build up more complex tactics from the primitive ones? At any rate, I expect the decompiler
is (currently) specialized/customized to the predefined set -- How much work is it to expand this set? What 
happens if the proof originally used tactics outside this set? Is there evidence that there is a core set 
that most (large) proofs use? My general concern is that large proof developments likely (?) use highly 
customized combinations of low-level tactics, and we'd want to update the proof scripts over those combinations,
and it wasn't clear to me that the method outlined here would do so.

Since, ultimately, repair is most useful on large proof developments, the paper would be significantly 
stronger with an evaluation over such code bases, which could, for example give evidence to answer the 
questions above. Even using just the kinds of changes in Table 1, why not see if those edits yield good 
repairs on large proof developments? (Specifically, it would help answer the question of whether the 
tactic decompilation is "robust" enough to work in large proofs, which likely use complex tactics not 
in the predefined set.) Dually, is there evidence that refactoring across type equivalences 
is in fact a common task that is really holding engineers back? At some level this is precisely the problem 
solved by "abstract data types" which is something encouraged even in the Software Foundations series.



Review #43D
===========================================================================

Overall merit
-------------
B. I support accepting this paper but will not champion it.

Reviewer expertise
------------------
X. **Expert** = "I have written a paper on one or more of this paper's
   topics"

Paper summary
-------------
Updating a mechanized proof when the theorem statement changes can be a lot of work.  One well-understood scenario is changing a type definition.  This paper presents a principled way to rewrite definitions and proofs to track a change in type definition, using a formal equivalence relation between old and new type versions, plus a handful of associated operators and coherence conditions.  The core translation applies directly to proof terms; it is somewhat best-effort, relying on unification heuristics to find the right ways to rewrite the source term to make its use of the old type systematic enough, since otherwise translation may produce ill-typed terms.  A follow-up translation reverse-engineers new proof terms into tactic scripts, starting with a naive translation and then iterating beautification heuristics.  Experiments show the tool working well on a variety of benchmarks, with varying levels of automation vs. manual application of the framework for describing type equivalences.

Comments for author
-------------------
I had trouble deciding between 'B' and 'C' ratings.  The tool presented here seems neatly split between a principled mechanism for applying type equivalences and then a general approach for decompiling proof terms into tactic scripts.  The principled mechanism is elegant and probably worth letting the community know about, though I have doubts about its generality, within the scope of automation demonstrated in this paper.  The decompilation mechanism is interesting but not described in much detail and not apparently evaluated very systematically, e.g. with a user study where people outside this project eyeball the results, and it doesn't seem to play well with uses of automation in proof scripts that would actually obviate most need for proof repair.  I also felt like the experiments were missing one or two large-scale case studies with Coq developments at the level associated with e.g. PLDI papers.

# The evaluation

I'm surprised there was no case study on CompCert or some development of that scale.  All sorts of new challenges can emerge going from the small type definitions of your case studies to real PL proofs.  Techniques might fail to apply, or performance may not be good enough.  It's good to have a breadth of tests like you have to show flexibility, but I want to see solidity on examples of independent interest.  (The Coq standard library just doesn't include interesting-enough type definitions, IMO.)

Am I understanding right that you implemented four different automated configuration generators, corresponding to the first four classes of Table 1?  If so, it seems each generator was only tested on between one and three examples, if the table is exhaustive, which may not be enough to be sure about generality, soundness, performance, etc.  Since the paper also doesn't discuss these generators in detail, it's hard to become convinced that they are likely to be general.

It's good to have validation from use of your tool by an industry engineer, but that particular example seems strange and doesn't obviously answer questions I had about the tool beside "can someone who didn't create it use it."  Switching between records and tuples sounds doable by a much simpler and less general tool.

The example of adding a new constructor is subtle.  You define a type `Diff` whose definition actually sounds somewhat involved, to avoid duplicating cases with `Old.Term`.  It seems you must encode the idea of "contains at least one use of the new constructor."  Do you anticipate future support for generating that kind of insight and code definition automatically?  It doesn't feel like support for this use case has been evaluated fully, given the amount of manual work.

# The type-equivalence framework

I think your key usability question is, does it take more work to define and prove a configuration, or to port a development manually?  Is the former complicated by needing to work with a fancy dependently typed encoding of inductive types and their constructors?  That is, might porting the development manually rely on much more elementary logic, thus staying accessible to a broader audience?

The details of automatic configuration generation seem important to resolving that last concern, making it too bad that the paper omits them.

`DepConstr` and `DepElim` are fundamentally about breaking types up into constructor cases.  What limitations on expressiveness follow from that choice?  Do I understand right that it is straightforward to use your framework whenever it is straightforward to define configurations manually?  What if e.g. I want to switch between sorted association lists and tries (two options from Coq's standard library), to represent finite maps over natural numbers?

You added a caching optimization, to reuse a translation when the same subterm appears multiple times.  In a program with minimal opportunities for reuse, maintaining a cache could slow translation significantly without any payoff.  Did you investigate where that tradeoff line is reached?  This is the kind of concern that might have loomed large in a case study on a large development of independent interest.  This idea reminds me of hash consing, an idea tried and abandoned for Coq itself, at least once in its history.

Putting on our worst-case thinking caps, how badly could the core transformations misbehave, to give the proof engineer a new development that really doesn't prove at all what he wanted?  Since the translation may sometimes fail with type errors, it seems likely that it could also silently generate undesirable code, in perverse-enough cases.

# The proof-term-decompilation framework

The decompiler (from proof terms to proof scripts) sometimes outputs faulty proof scripts.  That possibility is especially scary for proof-relevant derivations, where the "proof term" found matters.  For instance, we may be doing an equational derivation of a program, using tactics, then going on to prove additional properties of that program.

The second pass of decompilation depends critically on an oracle that reveals when a change keeps the proof working.  That aspect also wouldn't be dependable for proof-relevant derivations.  Also, do you allow changes that modify the subgoal structure and thus may lead to surprising grouping of Ltac segments into subgoals?

It's neat to read about the heuristics you use to get normal `induction` and `rewrite` invocations to infer motives correctly, starting from proof terms with explicit motives.  I groaned, though, on reading that one heuristic involves running `simpl` before proceeding.  That tactic can often become such a time and memory hog that many advanced developments are carefully constructed to avoid ever having to run it!  In general, it should not be considered safe to run such a naive "simplify everything" heuristic wherever it helps with automation.  Even using `change` could lead to very expensive conversion checks in practice -- or you should provide some argument to the contrary.

It looks like Carrot has no support for generating loops or pattern matching (conditionals) in Ltac.  The hint mechanism may make it possible to introduce such mechanisms within "leaves" of the proof-tree generation, but we never get Carrot recursing inside loops or pattern matching, right?  In general, the paper seems to impose an ironic choice on proof engineers: write extremely manual proof scripts that are labor-intensive to create in the first place, but then get this new refactoring support; or write automated proof scripts that speed initial construction and indeed often adapt automatically to type redefinition, but lose out on the chance to use refactoring without losing the advantages of proof automation.

The idea of applying tactic hints in beautification of proof scripts is interesting, but the paper says so little about it that it's hard for me to draw any clear conclusions.

# Minutiae

A lot of the description here assumes familiarity not just with proof assistants but even with dependently typed ones in particular.  "Propositional" vs. "definitional" is a good example.  That assumed background may not be a good match with PLDI.

The example of `rev_app_distr` is puzzling because the original proof does an extraneous induction/case split on `y`.  In fact, `app_nil_r` proves the base case of the outer induction almost directly.

Though Figure 3's example proofs aren't (I believe) meant to be subjected to your proof-repair tool, I will note that their proof scripts are unnecessarily manual.  Both lemmas are proved by `induction l; simpl; congruence`, which would also work, completely type-generically, for a majority of categories of datatype refactorings described in this paper.

Line 339, "takes t to t'" seems to have an extraneous space after it.

Figure 9 mostly seems to be the boring congruence rules.  Maybe it does more harm than good to show all of them, distracting the reader from the interesting rules at the beginning.

Line 651 says "The transformation in turn assumes that all parameters and indices are fixed."  So that assumption is even already incompatible with vector types, where the "cons" constructor has different indices between argument and result?  Or am I misunderstanding "fixed"?

Line 739, "With this correctness criteria" ... probably wants "these" instead of "this".

Line 984, "one larger that is larger than" ... some redundant words.

Line 1308, "to express path equality in cubical" ... probably missing a word or two afterward?

