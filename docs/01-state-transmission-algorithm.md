# 1. The minimal ontology

Strip a computing system down until removing anything else would break it,
and what remains is one shape:

```
state ──transmission──▶ [ algorithm ] ──▶ state'
```

A state exists. Something transmits — a message, an activation, a question,
a file. An algorithm runs during or at the end of that transmission. A state
exists afterward. That is all a spreadsheet cell is, all a transformer layer
is, all a state machine is, all an actor is. The claim of this study is that
**it is also all a quilt needs** — and that adding more primitives than this
is where architectures go wrong.

## The four endings

Every transmission terminates in one of four ways. The vocabulary matters,
because most confusion about "self-replicating" or "self-coordinating"
systems is confusion about which ending is being used.

| Ending | What happens | Familiar forms |
|--------|--------------|----------------|
| **Mutate** | The state changes where it was | A cell recalculating; an actor updating its state; fine-tuning in place |
| **Move** | The state appears somewhere else and leaves here | Process migration; handing off a session; a model loaded onto a new device, deleted from the old |
| **Copy** | The state appears somewhere else and stays here | Replication; backup; a weights file sent to a second machine |
| **Project** | The state stays home and sends *differentiated influence* | A prism cell fanning one input out, cut per receiver; a judge emitting percentages other cells act on |

Copy is the ending that alarms people when a system chooses it by itself,
without an owner choosing it. Note that in this ontology copy is not a
special mode a system "slips into." It is one ordinary outcome of a
transmission, no more exotic than mutate. A worm is a system where copy is
the default ending and nobody signed for it. A backup system is the same
ending, chosen on purpose, in the open.

Project is the ending that scales furthest and is easiest to overlook, and
chapter 3 is devoted to it: influence that travels without the state
traveling, tailored per receiver, leaving the sender in place.

## Why no more primitives are needed

Coordination looks like it should need a fifth thing — a scheduler, a
registry, a commander. It doesn't. Coordination is what you call a pattern
of transmissions when each patch decides, at each seam, which ending applies
and whether to accept what arrived. A patch never has to understand the
system it is in. It has to honor one contract (chapter 2) and answer for
its own state transitions.

This is the same economy the BEAM's process model enforces (chapter 3) and
the same economy state-machine replication proved in the 1980s: feed
identical transmissions to identical algorithms, and the state reappears,
faithfully, on machines it never touched. Replication was never a
capability layered on top of state machines. It is a property of them.

## What this rules out

- **No shared state between patches.** If two patches can both reach into a
  third thing and change it, you no longer know which transmission caused
  which state — and you have lost auditability, which this design treats as
  load-bearing.
- **No patch designs for the quilt.** A patch designed as "part of" a
  specific larger system embeds assumptions about its neighbors and stops
  being portable. Patches are built to stand alone; quilts emerge.
- **No central point whose removal kills the system** — not because
  distribution magically prevents it (chapter 7 shows it doesn't), but
  because the ontology gives the system no place to put one.
