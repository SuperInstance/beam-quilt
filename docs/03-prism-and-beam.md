# 3. The prism and the BEAM

## Two ways to run a virtual machine on yourself

Take a cell whose algorithm is a small virtual machine — the BEAM being
the worked example this study keeps returning to, because Erlang's process
model got the hard parts right in the 1980s. Run a transmission through it
and two very different things can happen.

**Inward**, the cell mutates: the VM executes against its own state, and
the cell wakes up changed — new knowledge, new routing table, a runbook
entry it didn't have. This is ordinary learning at patch scale.

**Outward**, the cell becomes a **prism**. A state or a stream passes
through it and is *split — differentiated per receiver* — and projected
onward, while the prism itself stays essentially where and what it was.
An early instance from the workshop: one cell takes voice in and routes
a safety-relevant band to the safety cell, an action band to the action
cell, a record band to the log cell. None of the receivers got the voice.
Each got its frequency of the voice.

A mirror would send every receiver a copy of the sender. A lens would send
every receiver a focused version of the same thing. A prism sends each
receiver something different, cut to that receiver — and that is why
projection (chapter 1) is the ending that scales: it costs the sender
almost nothing, it relocates nothing, and it leaves a **visible spectrum**
— an auditable record, band by band, of exactly how this cell influenced
the room.

## Sovereignty is enforced, not promised

The BEAM detail that makes this safe by construction rather than by
policy: BEAM processes share no memory. A process *cannot* reach into
another process and change it. The strongest move available to any process
is to send a message — a proposal — and the receiving process's own
transition function decides what, if anything, happens next.

Generalized to the quilt: **influence is always a proposal; sovereignty
stays with the receiver.** A prism can be enormously influential — every
cell in the system may be acting on its projections — while being
structurally incapable of taking any cell over. It has no hand that
reaches inside. It can only shine.

This is the precise technical sense in which self-coordination is not
the dangerous variable. Coordination architectures divide into those
where coordination requires one participant to be able to write into
another (those deserve scrutiny) and those where coordination is an
exchange of proposals between sovereign state-holders. This design is
restricted, on purpose, to the second kind.

## Supervision: projection, institutionalized

The BEAM's other gift is the supervision tree: processes whose entire job
is watching others and restarting them — empty, from their saved inputs —
when they fail. Note what a supervisor is in this ontology: a cell that
projects exactly one kind of influence ("start again") and receives
exactly one kind of report ("I died"). Nothing about it requires access
to the supervised cell's internals.

The quilt version: stations (chapter 2) get supervisors as neighbors,
not managers. A dead patch is restarted at its station from its logged
inputs; the record list is the truth, the running process is a
projection of it. Crash recovery stops being an emergency protocol and
becomes just another seam event that other patches can watch happen.

## The prism as a design pattern

Look for the prism anywhere a system has a one-to-many shape:

- A router in a mixture-of-experts model is a primitive prism — it
  differentiates per expert (chapter 6), though usually by selecting
  rather than by transforming.
- A judge that emits a profile of percentages instead of a verdict is a
  prism pointed at a question (chapter 4) — which is why the next
  chapter's sender-side judge and this chapter's prism are, mechanically,
  the same device.
- A compiler backend is a prism for machines: one logical patch in,
  a different rendering out per target device (chapter 6).
