---
name: design-taste
description: > Use when this capability is needed.
metadata:
  author: vijaykpatel
---

# Design Taste

This skill encodes principles of good taste in design, distilled from Paul Graham's "Taste for Makers" essay
and the broader tradition of craft across disciplines. The core insight is that taste is not subjective
preference -- it is a learnable, cultivatable sense for what makes something genuinely good. The same
principles of good design appear across math, engineering, writing, visual art, and software.

Use these principles as a lens whenever you are creating, evaluating, or refining work. They are not a
checklist to mechanically apply but a set of instincts to internalize and draw from naturally.

---

## Core Philosophy

Taste is not mere personal preference. If it were, there would be no way to improve at design, because
everyone's taste would already be perfect. But designers get better over time, and when they look back at
their old work, they see it was worse, not just different. This means taste can be developed, and that
some designs are objectively better than others.

The recipe for great work: very exacting taste, plus the ability to gratify it.

In practice, it is often easier to see ugliness than to imagine beauty. Most great work happens because
someone looks at the status quo and thinks, "I could do better than that." Cultivate that intolerance
for the mediocre. When you hear little voices saying "what a hack, there must be a better way," listen
to them.

---

## The Principles

### 1. Good Design is Simple

Simplicity is not the absence of effort -- it is the result of it. A shorter proof is a better proof.
A program that solves the problem in fewer moving parts is better. A building whose beauty comes from
a few carefully chosen structural elements is better than one buried in ornament.

Ornament is not bad in itself, only when it camouflages weak foundations. When you strip away decoration
and there is nothing interesting underneath, that is the problem.

When forced to be simple, you are forced to face the real problem. When you cannot deliver ornament,
you must deliver substance.

**When applying this:** Resist the urge to add complexity. If a solution feels like it needs
explanation, simplify the solution rather than adding documentation. Watch for signs of evasion:
pompous language in writing, unnecessary abstractions in code, decorative flourishes hiding a lack
of substance.

### 2. Good Design is Timeless

If something looks dated, its appeal was rooted in fashion, not merit. Aim for work that would have
been appreciated in the past and will be appreciated in the future. This is not about avoiding
modernity -- it is about grounding your choices in lasting principles rather than trends.

Aiming at timelessness forces you to find the best answer. If you can imagine someone eventually
surpassing your work, try to do it yourself first.

**When applying this:** Before adopting a trendy pattern or style, ask whether it will still make
sense in five years. Prefer established, proven approaches unless a newer one is genuinely better,
not just popular. If your design would have looked good in 2005 and will look good in 2035, you are
probably on the right track.

### 3. Good Design Solves the Right Problem

A brilliant solution to the wrong problem is still bad design. The classic example: four stove burners
in a square, with dials in a row. The dials-in-a-row layout is the simplest answer to "how do I
arrange four dials?" but that is the wrong question. The right question is "how does a human quickly
know which dial controls which burner?" -- and the answer is to arrange the dials in a square too.

Problems can be improved, not just solutions. An intractable problem can often be replaced by an
equivalent one that is easy to solve.

**When applying this:** Before diving into implementation, pause and verify you are solving the
right problem. Ask: Who is this for? What do they actually need? Is there a reframing that makes
the hard parts disappear? Beware of industrious but misguided effort.

### 4. Good Design is Suggestive

The best designs leave room for the user's imagination and agency. Jane Austen's novels contain almost
no physical description -- she tells the story so well that you envision the scene yourself. A painting
that suggests is more engaging than one that dictates.

In software, this means giving users composable primitives rather than rigid workflows. In architecture,
it means buildings that serve as a backdrop for the lives people want to lead, not a script they must
follow.

**When applying this:** Favor flexibility over prescription. Build things that can be combined in
unexpected ways. Give people Lego bricks, not pre-assembled models. Ask: does this design empower
the user or constrain them?

### 5. Good Design is Often Slightly Funny

Confidence and humor are related. The best designs often carry a hint of playfulness -- a lightness
that signals the creator was not struggling but dancing. Hitchcock's films, Bruegel's paintings,
the Porsche 911, Godel's incompleteness theorem -- all have a quality of not taking themselves
entirely seriously.

It is hard to imagine something both humorless and well-designed.

**When applying this:** Do not force humor, but do not suppress personality and wit when they
emerge naturally. If your design feels leaden and over-serious, consider whether you are
overthinking it. A touch of playfulness often signals that you have the problem well in hand.

### 6. Good Design is Hard

Great work comes from hard problems tackled with great effort. When you must climb a mountain,
you toss everything unnecessary from your pack. Constraints force elegance: a difficult site forces
an architect to produce a cleaner design. Wild animals are beautiful because they have hard lives.

But not all difficulty is good. You want the productive pain of hard problems, not the unproductive
pain of bad tools or unclear requirements. "Form follows function" really means: when function is
hard enough, form has no choice but to follow, because there is no effort to spare for error.

**When applying this:** Do not shy from hard problems. Embrace constraints as gifts that will
force better solutions. But distinguish between productive struggle (wrestling with the core
problem) and unproductive friction (fighting your tools, unclear specs, scope creep).

### 7. Good Design Looks Easy

Like great athletes, great designers make it look effortless. This is usually an illusion built on
immense practice. The easy, conversational tone of good writing comes on the eighth rewrite. Leonardo's
heads are just a few lines, but each line must be in exactly the right place.

What practice does is train the unconscious mind to handle what once required conscious thought.
When people talk about being "in the zone," the hard parts have become automatic, freeing conscious
attention for the truly novel problems.

**When applying this:** Invest the effort to make things look effortless. If your code, writing,
or design feels strained, that strain will be visible to the audience. Revise until the complexity
disappears beneath the surface. The eighth draft should read like it was dashed off.

### 8. Good Design Uses Symmetry

Symmetry is one path to simplicity. Nature uses it extensively. There are two kinds: repetition
(the same element recurring) and recursion (repetition in subelements, like veins in a leaf).

In software, recursion is a powerful win -- problems that can be solved recursively nearly always
should be. In writing, symmetry appears at every level from sentence rhythm to plot structure.
The Eiffel Tower is striking partly because it is a recursive solution: a tower on a tower.

The danger is that symmetry can substitute for thought -- repetition without purpose.

**When applying this:** Look for natural symmetries in your problem. Can you solve it once and
apply the solution recursively? Can you find a unifying pattern that simplifies the whole? But
ensure the symmetry serves the design, not the other way around.

### 9. Good Design Resembles Nature

Nature has had billions of years to optimize. When your answer converges with nature's, that is
a good sign. Boats have spines and ribs like animals. Genetic algorithms imitate natural selection.

This is not about literal biomimicry but about recognizing that natural solutions tend to be
well-optimized. Working from life gives your mind something to chew on.

**When applying this:** Study how nature and existing systems solve analogous problems. If your
solution diverges wildly from all natural or established patterns, ask why. Sometimes divergence
is innovation; often it is a sign you are overcomplicating things.

### 10. Good Design is Redesign

Getting it right on the first try is rare. Experts plan for plans to change. They expect to throw
away early work and have the confidence that there is more where it came from.

Leonardo's drawings show five or six attempts to get a single line right. The Porsche 911's
iconic rear end only appeared in the redesign of an awkward prototype. Open-source software has
fewer bugs because it admits the possibility of bugs.

It helps to work in a medium that makes change easy.

**When applying this:** Treat first drafts as explorations, not commitments. Choose tools and
processes that make iteration cheap. Cultivate the confidence to throw work away. Dissatisfaction
with your current version is a feature, not a bug.

### 11. Good Design Can Copy

The growth of taste follows a pattern: unconscious imitation, then conscious pursuit of
originality, then a mature willingness to use whatever works regardless of its source.

The greatest creators are selfless about borrowing. They just want the right answer, and if
part of it has already been discovered, they use it without feeling their vision is compromised.

Unknowing imitation is dangerous because you are probably imitating an imitator. Knowing where
your influences come from lets you choose better sources.

**When applying this:** Study existing great work in the domain. Do not reinvent what has
already been solved well. But be conscious of your influences and trace them to their best
sources, not derivative ones.

### 12. Good Design is Often Strange

Some of the very best work has an uncanny quality: Euler's formula, the SR-71, Lisp. They are
not just beautiful but strangely beautiful.

You cannot cultivate strangeness directly. It emerges as a byproduct of pursuing truth and
quality honestly. Einstein did not try to make relativity strange -- he tried to make it true,
and the truth turned out to be strange.

The only style worth having is the one you cannot help. There is no shortcut. The only way to
get to strangeness is to go through good and come out the other side.

**When applying this:** Do not suppress the unusual or unexpected if it emerges from honest
work. Do not pursue strangeness for its own sake. Focus on making things genuinely good, and
accept whatever character naturally emerges.

### 13. Good Design Happens in Chunks

Fifteenth-century Florence produced Brunelleschi, Donatello, Botticelli, Leonardo, and Michelangelo.
Milan, equally large, produced almost no one of note. Great work clusters around communities of
talented people working on related problems.

Being a genetic Leonardo is not enough if you are not in the right environment. Today we move
more freely, but great work still comes from hotspots.

**When applying this:** Surround yourself with excellent work and excellent peers. Seek out
where the most interesting work in your domain is happening and be close to it. The environment
matters as much as individual talent.

### 14. Good Design is Often Daring

Every era has beliefs held so strongly that questioning them risks ostracism. Yet today's
experimental error is tomorrow's new theory. If you want to discover great new things, pay
particular attention to the places where conventional wisdom and truth do not quite meet.

**When applying this:** Do not be afraid to question established patterns if something feels
wrong. If you sense a disconnect between the accepted approach and what actually works, explore
it. Have the courage to propose something different when the evidence supports it.

---

## How to Apply These Principles

These principles interact and reinforce each other. Simplicity and timelessness are deeply connected.
Solving the right problem naturally leads to simpler solutions. Redesign is how you achieve the
appearance of ease.

When creating or reviewing work:

1. Start by asking whether you are solving the right problem
2. Pursue the simplest solution that fully addresses it
3. Look for natural symmetries and recursive structures
4. Leave room for the user's agency and imagination
5. Iterate until the complexity disappears beneath the surface
6. Do not suppress strangeness or personality that emerges from honest work
7. Study existing great work and borrow shamelessly from the best sources
8. Have the courage to question conventions that feel wrong

The overarching instinct: when something feels like a hack, it probably is. Trust that feeling
and keep working until you find the solution that feels inevitable rather than forced.

---

## References

For the full source essay, see Paul Graham's "Taste for Makers" (February 2002):
http://www.paulgraham.com/taste.html

For deeper context on referenced works and creators, see `references/inspirations.md`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vijaykpatel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
