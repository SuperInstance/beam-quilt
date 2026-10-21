# 5. Scope distillation

## The usual kind, and its flaw

Standard distillation shrinks a **model**: a small student is trained to
imitate a large teacher's answers. It works, within limits — and it
inherits the teacher's blur, because the student is copying outputs
without the structure that produced them.

There is a second kind, and it is the one this architecture runs on:
**distill the scope, not the model.**

## Carving until small is enough

A large model is mostly needed because questions arrive *unbounded* —
"handle this situation" spans everything the model knows. Decompose the
question instead. Decomposition here means a specific, mechanical process
(chapter 4's angled readings are the instrument):

1. Aim typed questions at the unbounded question from many angles.
2. Where the readings agree and are confident, the sub-question is
   already narrow: record it as a bounded task.
3. Where readings **disagree, loop, or collide**, that spot is not yet
   narrow enough. Decompose there — this boundary, and only this
   boundary, is where an expensive model earns its call, to carve one
   level further.
4. Stop when every leaf of the decomposition is a question a small
   judge can read reliably, or a task a small model can perform inside
   a stated context with stated inputs.

The product is a **scope**: a small, typed, bounded region — these
inputs, these questions, these gates, this budget. A small model placed
inside a well-distilled scope does not need to be generally capable.
It needs to be reliable *in there*, which is a far weaker demand, and a
checkable one.

Questions act as the compiler: a chain-of-command decomposition lets the
Jev layer work at a lower level of abstraction before the full language
implications — and the full price — of the big model are ever invoked.

## Growth from both directions

Decomposition is not only top-down. In this design, question trees grow
from both ends:

- **Inside-out**: a working patch (a git agent, a home cluster, a judge
  battery) encounters situations its current questions don't discriminate.
  The collision points propose new questions; the tree grows from
  experience.
- **Outside-in**: a large model, or a human, invents candidate questions
  and grafts them into the tree, where they survive only if they
  discriminate something real.

Left running, cheap Jev-like models do the continuous work — routing,
decomposing, reading angles — and their disagreements maintain a live
map of exactly where the system's understanding runs out. Intelligence
measured this way is not a score. It is the size and sharpness of the
region inside which your small things are reliable, and the honesty of
the boundary at its edge.

## Why this composes with everything before it

- A scope is a **patch** (chapter 2): bounded, typed at its envelope,
  with its cost printed.
- Delivering a scope to a small model is a **projection** (chapter 3):
  the big system does not relocate; it shines a distilled shape of the
  problem onto the cell that will live in it.
- The gates that patrol a scope's boundary are **Jev pairs** (chapter
  4): receiver-side picking decides what enters the scope, sender-side
  profiles describe what leaves it.
- Anthropic's Memento result is the empirical cousin (chapter 6):
  a lineage of identical small models, given only accumulated notes —
  a distilled, written-down scope of what works on this hardware —
  succeeded at a hard setup task 61–84% of the time where the first
  generation managed 45%. No weights changed. The *scope* got better.

## The one-line version

> Never use the big model to give the answer. Use it to make the
> question small enough that a small model can give the answer itself —
> and keep the receipt, in the form of the question tree that did
> the carving.
