# 8. Lineage

None of the ideas in this study are younger than their newest
implementation. A compressed genealogy, because seeing the age of the
parts changes how one should feel about the whole: this is not a
rupture. It is a convergence.

## 1957: the portable artifact

FORTRAN made a radical promise: write once, in a human notation, and a
compiler renders it onto the machine in front of you. Two of its ideas
run through everything after: the **compiled artifact** (intelligence,
of a sort, frozen into a portable object) and the **subroutine library**
(modules written once, linked into whatever needs them). The linker was
the first coordinator in this lineage — pieces that know their own
edges, stitched by a dumb, reliable mechanism, no piece holding the
whole program.

## The systems interlude

Mainframes, then client-server, then cloud services swung the pendulum
toward intelligence as a *place you petition*: capability lived in a
building, and your device held a terminal. Frameworks grew; installation
became a priesthood. Much of the material in this study is legible as
the pendulum swinging back — the artifact view recovering ground the
service view took.

## The 1980s: the transmission turn

The second leap, and the one this architecture is built on, arrived
with actor models reaching production (Erlang at Ericsson, mid-1980s;
the BEAM virtual machine under it) and with state-machine replication
in distributed systems theory. The shared insight, stated in
chapter 1's terms: **a system is states, transmissions, and the
algorithms transmissions run through — and a state can end a
transmission changed in place or materialized somewhere else.**
Replication, migration, and supervision all fall out of that sentence.
The BEAM added the discipline that makes it safe: processes share
nothing, influence travels only as messages, and failure is a normal
event handled by supervisors rather than a catastrophe handled by hope.

## The 2010s–2020s: the model becomes a file

Transformers (2017) and mixture-of-experts revived an old trade —
specialization plus routing — inside the model (chapter 6). The local-
inference ecosystem then did to models what FORTRAN did to programs:
**llama.cpp** and the GGUF format made weights a portable file you
copy like a document; small open runtimes made them executable
anywhere; the **llamafile** line fused weights and runtime into a
single artifact. A model became a subroutine again — self-describing
enough to ship, cheap enough to copy, dumb enough to file.

## The 2020s: the installer learns

The final piece in the sequence is the one Anthropic measured in 2026:
agents competent enough to perform the *last-mile rendering*
themselves — probing unfamiliar hardware, choosing stacks, debugging
their own misdiagnoses, and, with a notes-inheriting harness,
accumulating runbooks that make each successive installation easier.
The linker learned to improvise. Everything else in this study —
patches, prisms, paired judges, distilled scopes — is what you can
build once artifacts are portable, transmissions are the primitive,
and installation is no longer the bottleneck staffed by humans.

## The pattern

1957 gave the artifact. The 1980s gave the transmission. The 2020s
gave the artifact intelligence and the transmission a judge at each
end. The quilt is the refusal to add a fourth thing.
