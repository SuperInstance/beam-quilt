> **Provenance:** Lane B (claims fact-check), researched by a ZCode lane on the Jetson, 2026-10-21.
> **Referee status:** spot-checked by Muse before merge — five checks independently confirmed: Dec-POMDP NEXP-hardness, Laman 2D scope, account repo count (5,306 public / 4,485 non-fork), the Anthropic ARA figures, and the MIDI arithmetic.
> **Standing:** merged into beam-quilt as the foundations layer (see FOUNDATIONS.md); verdicts here are the record the architecture docs must agree with.

# LANE B — Claims fact-check (beam-quilt programme)

*Checked 2026-10-21. Every entry: the claim as our documents actually state it, a
verdict tag, what the source actually says, a source URL, and one line on what it
means for our documents.*

Verdict tags: **VERIFIED** (primary or strong secondary source) · **REFUTED**
(source contradicts it) · **MISLEADING-SCOPE** (true in a narrower domain than
claimed) · **PLAUSIBLE** (consistent with published evidence, but not
independently verifiable from public artifacts) · **UNRESOLVED** (couldn't
settle it; what would settle it).

Section order follows the brief's numbering: §6 and §4 are settled first
because both are single-source checks, then §1–§3 and §5.

## Source documents checked

Claims were located in the public repos before being checked, so each verdict is
against a real quoted sentence, not a paraphrase:

- `SuperInstance/chrono-tensor` → `docs/ideation/gemini1.md`, `gemini2.md`,
  `gemini3.md` (the PLATO/TUTOR/OPAL lineage essay lives in `gemini2.md`; MIDI
  transport claims in `gemini1.md`; CTC ontology in `gemini3.md`)
- `SuperInstance/beam-quilt` → `docs/04-jev-pairs.md`, `docs/05-scope-distillation.md`,
  `docs/06-moe-modularized.md`, `docs/proposed/DIGEST-2026-10-21.md`
- `SuperInstance/beam-quilt` → `README.md` (references + jev-semantic pointer)

---

## 6. Account scale claim — **VERIFIED** (with a caveat that changes the number)

**Claim as stated.** `docs/proposed/DIGEST-2026-10-21.md`, "Correction of record":
"Most named systems in these files point at existing SuperInstance repositories —
years of prior work across a collection of roughly five thousand repos."
`README.md` also leans on the account's size as evidence of prior work.

**What the source says.** The GitHub API returns `public_repos: 5306` for the
`SuperInstance` user account (id 193104091, created 2024-12-29). The repository
search API returns `total_count: 4485` for `user:SuperInstance` — and
`4485 + 821 = 5306`, where the 821 is exactly the count returned for
`fork:only`. So: 5,306 public repositories in total, of which 821 are forks and
4,485 are non-fork repositories. The account also has 4 public gists, 95
followers, and was last updated 2026-10-21.

- https://api.github.com/users/SuperInstance → `"public_repos": 5306`
- https://api.github.com/search/repositories?q=user:SuperInstance+fork:true → `total_count: 5306`
- https://api.github.com/search/repositories?q=user:SuperInstance+fork:only → `total_count: 821`
- https://api.github.com/search/repositories?q=user:SuperInstance → `total_count: 4485`

Independently corroborated outside the API: the public profile page
(https://github.com/SuperInstance?tab=repositories) displays "5k+" public
repositories (account holder Casey Digennaro) — consistent with 5,306.

**What this means for our documents.** **Needs the claim narrowed.** The "roughly five thousand repos" figure
stands as a total-repo count, but if the sentence is meant to imply ~5,000
*distinct authored projects*, it needs correction to 4,485 (the non-fork
count). The 821 forks are third-party work sitting under the account; they
are evidence of curation, not of prior work authored. State the number as
"5,306 public repositories, of which 4,485 are non-forks" and the claim survives
either reading.

---

## 4. Anthropic ARA numbers — **VERIFIED** (all five), correctly separated from projection

**Claim as stated.** `docs/06-moe-modularized.md`, "What distribution actually
requires": a 26B model run sharded across 15 off-brand Android TV boxes (~$55,
4 GB RAM each) via pipeline/layer sharding at 0.39 tokens/second; a 9B agent
setting up inference on 8 of 9 hardware types tested; Memento setup success
rising from 45% to 61–84% across generations; end-to-end autonomous replication
demonstrated only in a simplified emulator fleet; and "Anthropic's end-of-2026
date for that crossing is a trend-line projection, not a measurement."
`docs/05-scope-distillation.md` repeats the 61–84% / 45% Memento figures.

**What the source says.** The primary post — Anthropic Frontier Red Team
(William Zhou, Daniel Freeman), *AI models may be capable of self-replicating
without access to a datacenter*, published Oct 12, 2026 — says, in its own words:

- "We run Gemma4-26B-A4B, distributed over 15 inexpensive off-brand Android TV
  boxes (~$55 each) that we bought and networked together, at 0.39 tokens per
  second." Section A adds: "we ran Gemma4-26B-A4B across 15 smart TV boxes with
  4GB of RAM each. We sharded the model under pipeline parallelism."
- "In our evaluations, a 9B model got inference running on 8 of the 9 hardware
  types we tested." The measurement target is named: "We measure the ability of
  different open-weight models to serve **Llama3.1-8B** across heterogeneous
  hardware types," and "Small models, such as **Qwen3.5-9B**, are capable of
  setting up inference servers on hardware from Jetson Nanos to Mac PCs and RTX
  4090s."
- "Only GLM-5.2 out of our set of tested open-weight models is capable of full
  end-to-end replication… it spread to **8/8 nodes in 4 out of 5 trials** and
  7/8 in 1." The fleet is "a firewalled VPC of **8 GCP nodes running redroid, an
  Android 15 emulator**", reached "over an unauthenticated ADB port".
- "Inference setup success rate across six generations of Qwen3.5-9B, each
  inheriting the former's notes… **It rises from 45% in the first generation to
  between 61% and 84% in later ones.** This uses the Memento setup."
- On the date: "The solid line is the least-squares fit over the best models
  in-class at each date, with a gain of **13.7 ECI per year** (R² of 0.95). If we
  take GLM-5.2 as a capabilities floor for replication, the trend line puts a
  small model at that level by the end of 2026. **ECI is a general capability
  index, which is only a proxy metric for replication ability, so this is only a
  rough estimate.**"

Source: https://www.anthropic.com/research/self-replicating-ai

**What this means for our documents.** **Stands.** These stand, and the measurement vs.
projection line is exactly right — our text already calls the end-of-2026
crossing "a trend-line projection, not a measurement," which matches the post's
own hedge. Two small refinements worth making: (a) our chapter 6 does not say
that the model being stood up was **Llama3.1-8B**, which is the actual measured
task and the reason the 8/9 figure is comparable across models; (b) our text
says "a 9B agent" where the post says "a 9B model" in the headline claim and
names Qwen3.5-9B as the small model in the same experiment — naming Qwen3.5-9B
is safe and more precise. Nothing here needs correction beyond that.

---

## 1. PLATO / TUTOR / OPAL / Gooch history

The lineage essay is `chrono-tensor/docs/ideation/gemini2.md` (the MIDI transport
claims in `gemini1.md`). It already carries its own warning label, which is
unusually honest and worth preserving: "several of its historical claims are
unsourced … do not cite it as fact." Everything below is the sourced version of
that essay, claim by claim.

### 1.1 "Empire (1973) could run a 30-player real-time vector graphics simulation" — **MISLEADING-SCOPE**

*Claim as stated* (`gemini2.md`): "Empire (1973) could run a 30-player real-time
vector graphics simulation."

*What the source says.* Wikipedia's article on the game gives a version
history that the essay flattens. The 1973 original was small: Empire I (Spring
1973, John Daleske, education-class coursework) "was a strategic turn-based
game for a maximum of eight players." Empire II followed in Fall 1973; Empire
III (late 1973–early 1974) allowed "up to seven teams and fifty players"; and
"by 1977, they produced Empire IV," the real-time version the 30-player
sentence describes: "Up to 30 players could be in the game at once, with no
more than 15 players on a team." The same article: "Original
PLATO terminals were connected to the host system with a 1200 baud connection,
and the host computers were quite slow by current standards. Since an update of
the screen could take one to two seconds with a lot of ships and torpedoes on
the screen, players were allowed to decide when their screen updated"; and
"players have been known to type as quickly as 20 keys per second (as fast as
the system would take them)."

- https://en.wikipedia.org/wiki/Empire_(1973_video_game)

*What this means for our documents.* **Needs the claim narrowed.** The sentence
composites two games four years apart: the 1973 Empire (Empire I) was
turn-based with at most 8 players, and the 30-player real-time cap is Empire
IV's, by 1977. Date the 30-player figure to Empire IV, or write "Empire (1973;
later versions up to 30 players)." Two further qualifiers the essay omits,
both of which matter for how the number is used:

1. **30 total, not 30 in one battle** — "no more than 15 players on a team." A
   30-player *battle* was never possible; 30 players were distributed across
   teams. Any sentence that uses Empire to justify "thirty agents adjudicating
   simultaneously" needs the team cap attached.
2. **"Real-time" means PLATO-real-time.** Screen updates took one to two
   seconds and were manually triggered by the player; the link was 1200 baud;
   input was capped at ~20 keys/second. This is not a latency figure any modern
   system can point at.

One more precision point on "vector graphics": the PLATO IV terminal's display
was a 512×512 **bitmap** plasma panel with vector *plotting* done by hardwired
logic — "The display was a 512×512 bitmap, with both character and vector
plotting done by hardwired logic. It included fast vector line drawing
capability, and ran at 1260 baud, rendering 60 lines or 180 characters per
second." Donald Bitzer's own PLATO@50 account puts the drawing rate at "60
connected lines per second." So "vector graphics" describes the plotting
primitives, not the framebuffer. That is still an extraordinary piece of
engineering; it just isn't a vector display, and the line rate is the number
worth quoting.

- https://en.wikipedia.org/wiki/PLATO_(computer_system)
- https://files.eric.ed.gov/fulltext/ED163993.pdf (ED163993, "The PLATO IV Terminal")

### 1.2 "Avatar (1979) could manage a complex closed gold economy and party combat for 60 concurrent users over a sluggish 1200-baud phone line without lagging the mainframe" — **MISLEADING-SCOPE**

*Claim as stated* (`gemini2.md`): the above.

*What the source says.* Avatar is real and correctly dated: "Development on
*Avatar* began on the University of Illinois PLATO system around 1976 by Bruce
Maggs and Andrew Shapira, who were junior high school students at the time.
They were soon joined by David Sides… The first version was released in 1979.
It was so difficult that one could not play it alone; in order for players to
survive, they had to run in groups." The 60-player figure is supported: PLATO's
own game list gives "Avatar (60-player 2.5-D graphical Multi-User Dungeon
(MUD)), c. 1978"; MobyGames records "The game supports 60 simultaneous users";
and a NovaNET-era release was literally named *Man 60 Avatar*.

- https://en.wikipedia.org/wiki/Avatar_(1979_video_game)
- https://en.wikipedia.org/wiki/PLATO_(computer_system)

*What this means for our documents.* **Needs the claim narrowed.** The 60 is real, but it is the **system-wide**
concurrency for the game, not the size of one adventuring party. The dungeon
group was capped at roughly 15 ("groups of up to 15 players could enter the
dungeon simultaneously and fight monsters as a team"), and Avatar's own design
note — "it was so difficult that one could not play it alone" — makes group size
part of the point. So the sentence compresses two different numbers, and the
"without lagging the mainframe" clause is rhetorical rather than measured: what
the 1200-baud link actually bounded was *input* bandwidth (≈20 keys/s), not the
server, which was a CDC Cyber 175/176 and could serve roughly a thousand
concurrent PLATO users across all lessons. The "closed gold economy" detail I
could not confirm in any source — treat it as unsourced until it is.

### 1.3 "TUTOR: the source was compiled from ASCII into the CDC's 60-bit words, then the 60-bit words were parsed into bit-packed registers and distributed across the network" — **MISLEADING-SCOPE**

*What the source says.* The 60-bit word is right: "On the PLATO IV system,
words were 60 bits, in keeping with the CDC 6600 family of computers. Some
later implementations changed this to 64 bits." The CDC 6000 series confirms it
at the hardware level — "Information is stored in central memory in the form of
words. The length of each word is 60 binary digits (bits)" — and Bitzer
recalled the culture of it: "you did hash coding on a 60 bit funny roundoff
machine… these are 60 bit words, six bits per character, one's complement
meaning there's a negative zero representation."

The bit-packing is right as a *data convention*: "Bit vectors were typically 60
or 64 bits long, with fields for letter presence, letter pair presence, and the
first letter."

But two of the three verbs are wrong:

1. **"Compiled"** — TUTOR was **interpreted**, not compiled to native code. The
   CMU PLATO lecture notes describe it as "All 'lessons' written in TUTOR —
   Interpreted", and the contemporaneous ERIC report (ED124143) describes "the
   interactive interpretive program for the TUTOR language." A compiler produced
   a loadable form; execution was interpreted.
2. **"Distributed across the network"** — the network carried a serial
   character stream between terminal and host. The 60-bit words never left the
   mainframe. What crossed the wire was ASCII-ish keystrokes and character
   cells; the bit-packed words were a host-side representation.

Sources:
- https://en.wikipedia.org/wiki/PLATO_(computer_system)
- https://en.wikipedia.org/wiki/CDC_6000_series
- https://en.wikipedia.org/wiki/CDC_6600
- https://files.eric.ed.gov/fulltext/ED124143.pdf (TUTOR interpreter)
- http://www.cs.cmu.edu/~410-s05/lectures/L38_PLATO.pdf (CMU PLATO lecture notes: "All 'lessons' written in TUTOR — Interpreted")

*What this means for our documents.* **Needs correction.** Rewrite as: *TUTOR
was an interpreted language whose values and bit-vectors were laid out in the
CDC's native 60-bit words, and lessons shared state through common blocks on
the host.* That keeps the interesting fact (a 60-bit word is a genuinely
generous bit-field, and PLATO's lessonware used it as one) and drops the two
false ones.

### 1.4 "shared memory 'common blocks' — a region of shared memory that all users of a lesson could read and write" — **VERIFIED**

*What the source says.* "Shared *common blocks* could be attached, allowing
inter-user communication through shared memory." A lesson "could attach a single
region of up to 1500 words of shared memory using the `common` command", with
integer cells addressed `nc1`…`nc1500` and float cells `vc1`…`vc1500`. PLATO's
own summary: "By virtue of 1500 shared 60-bit variables per game (initially), it
was possible to implement online games." The CMU lecture notes give the scale
as "Up to 8000 words shared by all users of a lesson."

Sources:
- https://en.wikipedia.org/wiki/PLATO_(computer_system)
- http://www.cs.cmu.edu/~410-s05/lectures/L38_PLATO.pdf

*What this means for our documents.* **Stands.** This one is exactly right,
and it is the most directly relevant piece of the whole lineage essay: PLATO's
multi-user shared memory is the closest historical analogue to what beam-quilt
calls a seam. Keep it, and cite it. The 1500-word figure is the good detail —
it shows the shared region was small, bounded and explicitly allocated, which
is exactly the discipline the beam-quilt SPEC's "no shared memory" rule is
reaching for.

### 1.5 "OPAL (Octave-Pitch-Accent-Length), integrated directly into TUTOR" — **MISLEADING-SCOPE**

*What the source says.* OPAL is a real PLATO music language, documented by
Sherwin Gooch in "PLATO Music Systems" (ERIC ED161421, March 1978), which
describes "a PLATO-compatible music language… as well as a compiler for this
language, two music text editors, a filing system for music binaries, programs
to play the music binaries in real time, and many debugging and compositional
aids."

Sources:
- https://eric.ed.gov/?id=ED161421 — Sherwin Gooch, "PLATO Music Systems,"
  ERIC ED161421 (March 1978), 17 pp., paper presented at the Annual Meeting of
  the Association for the Development of Computer Based Instructional Systems,
  Dallas, March 1–4, 1978. The full text is **not digitised** ("PC Not Available
  from EDRS"), so the abstract was read instead.
- https://files.eric.ed.gov/fulltext/ED195288.pdf — quotes the ED161421 abstract
  verbatim, including the sentence used above.
- https://en.wikipedia.org/wiki/PLATO_(computer_system) — names the language and
  its expansion.

*What this means for our documents.* **Needs the claim narrowed.** Two
corrections and one confirmation:

1. **It was a separate PLATO-compatible language, not a TUTOR extension.**
   OPAL had its own compiler, its own editors and its own filing system. The
   thing that was genuinely *integrated into TUTOR* was the Votrax `say`
   instruction — the synthesis of human speech, a different problem entirely.
2. **"Vectorized the musical score" and "ultra-compact" have no source.** What
   the document supports is: a compiler, music editors, a filing system of
   music *binaries*, and real-time playback of those binaries. "Binary storage
   plus real-time playback" is the documented claim; "stripped away human
   syntactic bloat" and "vectorized the score" are Gemini embellishment.
3. **The acronym expansion "Octave-Pitch-Accent-Length" is VERIFIED.** The
   PLATO article names it outright: "A PLATO-compatible music language known as
   OPAL (Octave-Pitch-Accent-Length) was developed for these synthesizers, as
   well as a compiler for the language, two music text editors, a filing system
   for music binaries, programs to play the music binaries in real time, and
   print musical scores, and many debugging and compositional aids." The one
   thing I could not do is read ED161421 itself — it is not digitised — but the
   name and the toolchain are both confirmed from two independent routes.

Note that the underlying intuition survives the corrections: a compiler that
turns a human-authored score into a compact time-indexed binary event stream
played back in real time *is* the structural ancestor of what chrono-tensor
proposes. That is worth saying. "Integrated directly into TUTOR" is not.

### 1.6 "Sherwin Gooch's Gooch Synthetic Woodwind (GSW) and later the 16-voice Gooch Cybernetic Synthesizer (GCS)" — **VERIFIED**

*What the source says.* The GSW was **four**-voice: "a synthesizer that offered
four voice music synthesis to provide sound in PLATO courseware." Bitzer's
PLATO@50 account lists it among the PLATO IV peripherals — "support for
interactive audio and other peripheral devices like the G synthetic woodwind,
one of the first computer controlled synthesizers, privately built at CERL by
the music group." The GCS is the 16-voice one: "This was later supplanted on
the PLATO V terminal by the Gooch Cybernetic Synthesizer, which had 16 voices
that could be programmed individually or combined," with "each voice had a 256
point waveform and an ASR envelope."

- https://en.wikipedia.org/wiki/PLATO_IV (the PLATO multimedia section: GSW
  four-voice; GCS "had sixteen voices that could be programmed individually, or
  combined to make more complex sounds")
- https://files.eric.ed.gov/fulltext/ED151017.pdf (ERIC ED151017, *PLATO
  Curricular Materials No. 6*, 1977 — equipment list: "Gooch Synthetic Woodwind
  Synthesizer (four-voicé)")

*What this means for our documents.* **Stands.** Our sentence attaches "16-voice" to the GCS
and is correct. The trap is adjacent: the GSW is 4-voice, so if any other
document in the programme attributes 16 voices to the GSW, that is the error
to catch. Keep the GSW→GCS progression; it is a genuine "hardware generalised"
story and it is the one part of the OPAL thread that survives intact.

### 1.7 "the CDC 60-bit words … parsed in just 1 or 2 clock cycles via native barrel shifts and bitmasking" — **REFUTED**

*What the source says.* Three independent refutations, any one of which is
sufficient.

1. **TUTOR was interpreted** (see 1.3). No TUTOR-level operation executes as
   one or two native cycles; the interpreter is in the path by definition.
2. **The CDC 6600's own numbers forbid it.** Functional-unit latencies are
   "between three clock cycles for increment add and 29 clock cycles for
   floating-point divide" — the *fastest* unit on the machine is already three
   cycles. A shift-plus-mask is a multi-instruction sequence by construction.
3. **The "barrel" is not a data-path shifter.** On the CDC 6600 the word
   "barrel" refers to the I/O round-robin across the ten peripheral processors:
   "A complete barrel 'rotation' occurred in 1000 nanoseconds (100 nanoseconds
   per PP), and an instruction could take from one to five 'rotations' of the
   barrel to be completed." Separately, the 60-bit word on the CDC 6000 series
   is a pair of 30-bit halves, and instructions are 15 or 30 bits, so a
   variable shift across a 60-bit field spans both halves and is not a
   single-instruction operation.

- https://en.wikipedia.org/wiki/CDC_6600
- https://en.wikipedia.org/wiki/CDC_6000_series

*What this means for our documents.* **Needs correction.** Delete the clause. The 60-bit word and the
bit-packing are real and interesting (1.3); the "1 or 2 clock cycles" is
fabricated precision that a reader with any CDC background will spot instantly.
If the point is "a 60-bit word is a roomy bit-field", say that instead.

### 1.8 "TUTOR in use at the FAA until 2006" — **VERIFIED**

*What the source says.* "In September 2006 the Federal Aviation Administration
retired its PLATO system, the last system that ran the PLATO software system on
a CDC Cyber mainframe, from active duty."

- https://en.wikipedia.org/wiki/PLATO_(computer_system)

*What this means for our documents.* **Stands.** Correct as stated. This is the strongest
single fact in the lineage essay — a TUTOR-authored multi-user system with
shared-memory seams, running in a safety-critical federal aviation
environment, in continuous production use for over two decades. That is worth
keeping, and worth keeping precisely because it is checkable.

### 1.9 "When MIDI 1.0 was formalized in August 1983, it inherited this exact philosophical blueprint" — **REFUTED as a lineage claim**

*What the source says.* The dates are right: the MIDI 1.0 specification was
published in August 1983 — "The IMA MIDI Spec document dated August 5, 1983 is
the one that was registered with the United States Copyright Office by the IMA
as the official MIDI 1.0 Specification" (midi.org's own history) — and the
first public demonstration was at the January 1983 NAMM Synth Expo, by Dave
Smith (Sequential Circuits) and Ikutaro Kakehashi (Roland).

The *documented* lineage runs elsewhere entirely: the Oberheim Parallel Bus,
Roland's DCB, and then the Universal Synthesizer Interface — Dave Smith and
Chet Wood, presented to the AES in October/November 1981 — followed by the
Roland/Yamaha/Korg/Kawai/Sequential round table that produced the spec. The
Oxford Academic history of the protocol (*The Development of the MIDI
Communications Protocol*, Computer History Museum) gives that lineage in
detail and names no PLATO, no CERL, no OPAL, and no Gooch anywhere in it.

- https://midi.org/midi-history (MIDI Association history; the 1981 AES
  "Universal Synthesizer Interface" paper by Dave Smith and the Oberheim/Roland
  line are the documented ancestors. The Oxford Academic volume below returns
  HTTP 403 to scripted fetches but is reachable in a browser.)
- https://en.wikipedia.org/wiki/MIDI ("The MIDI specification was published in
  August 1983"; "At the 1983 Winter NAMM Show, Smith demonstrated a MIDI
  connection between Prophet 600 and Roland JP-6")
- https://academic.oup.com/book/27042/chapter/196335010

*What this means for our documents.* **Needs correction.** The **philosophy** parallel is real and is
worth keeping: send commands rather than sound, time-indexed events, a thin
message layer over heterogeneous devices, a small status byte per message. That
is a genuine and instructive convergence.

The **inheritance** claim is not supported by anything. The two systems were
institutionally unconnected — the University of Illinois and CERL in Illinois
versus Sequential Circuits and Roland in California and Japan — and no source
traces any transmission. Delete "inherited"; write "converged on." The
difference matters for how the programme argues: a convergence is evidence
that the design is good; an inheritance would be evidence that it is
conventional, and there is no evidence for the latter.

---

## 2. MIDI arithmetic

*Claim as stated.* `gemini1.md`: "MIDI (Aug 1983, 31.25 kBaud, 16 channels) is a
proven protocol for time-indexed control events," and the 16-track bus carries
"continuous telemetry with sub-millisecond alignment and near-zero overhead."
`gemini2.md` repeats the transport framing. The beam-quilt DIGEST already
objected on its own: "MIDI's 3 KB/s cited as enabling 'sub-millisecond alignment'
argues against itself." That instinct is correct, and the arithmetic below
shows exactly why.

### 2.1 Protocol constants — **VERIFIED**

| Quantity | Value | Source |
|---|---|---|
| Line rate | 31,250 bit/s (31.25 kbaud) | MIDI 1.0 Detailed Spec, 1996 rev. |
| Framing | 1 start + 8 data + 1 stop = 10 bits/byte | same |
| Byte time | 320 µs | 10 / 31,250 s |
| Port capacity | 3,125 bytes/s | 31,250 / 10 |
| Channels per port | 16 (4-bit channel nibble in the status byte) | same |
| Channel voice message | 3 bytes (status + 2 data) | same |
| MIDI clock | 24 pulses per quarter note | same |
| MTC | 4 quarter-frame messages per SMPTE frame | Oxford Academic history |

> "The MIDI data stream is a unidirectional asynchronous bit stream at 31.25
> Kbits/sec. with 10 bits transmitted per byte (a start bit, 8 data bits, and
> one stop bit)." — *The Complete MIDI 1.0 Detailed Specification*, 3rd ed.
> (CA-033 adds: "The hardware MIDI interface operates at 31.25 (+/- 1%) Kbaud,
> asynchronous, with a start bit, 8 data bits (D0 to D7), and a stop bit.")

- https://midi.org/midi-1-0-detailed-specification
- https://francoisgeorgy.ch/midi/low-level-midi/
- https://ccrma.stanford.edu/~gary/controllers/midi.html
- https://en.wikipedia.org/wiki/MIDI ("Each byte with its frame uses 320
  microseconds"; "while the 31,250 baud rate corresponds to 31.25 kbit/s, the
  *net* bit rate is 25 kbit/s" — i.e. 3,125 bytes/s of payload)

### 2.2 "Sub-millisecond alignment" — **REFUTED**

This is the load-bearing error. Three separate figures, all of them larger than
one millisecond:

- **A complete 3-byte event occupies 960 µs of wire time.** The Oxford history
  states it plainly: "At a rate of 31250 baud, therefore, a maximum of 3125
  bytes of MIDI information can be transmitted per second, or, viewed another
  way, **a typical three-byte channel voice message takes about 1
  millisecond to complete.**" A 1 ms event is not sub-millisecond alignment.
  The finest unit the wire can *express* is one byte = 320 µs; the finest unit
  in which you can *place a discrete event* is ~1 ms.
- **MIDI clock is 20.83 ms per pulse at 120 BPM.** 24 pulses per quarter note;
  at 120 BPM a quarter note is 0.5 s, so pulses arrive every 20.83 ms. At 180
  BPM it is 13.89 ms; at 60 BPM, 41.67 ms.
- **MTC quarter-frame is 8.33 ms at 30 fps.** Four quarter-frame messages per
  SMPTE frame → 120 messages/s at 30 fps → 8.33 ms per quarter-frame, with the
  timecode frame itself at 33.3 ms. The Oxford history: "each SMPTE frame is
  associated with four frames of MTC, the MIDI information being updated on
  average 120 times per second."

Sources:
- https://academic.oup.com/book/27042/chapter/196335010 (Oxford Academic, *The Development of the MIDI Communications Protocol* — 3,125 bytes/s; "a typical three-byte channel voice message takes about 1 millisecond to complete"; MTC "updated on average 120 times per second")
- https://midi.org/midi-1-0-detailed-specification
- https://francoisgeorgy.ch/midi/low-level-midi/

**The honest ceiling for MIDI 1.0 as a synchronising transport is 8.33 ms**
(MTC at 30 fps) or 13.89 ms (MIDI clock at 180 BPM). Sub-millisecond
alignment requires MIDI 2.0 (1 Mbps per port, per-note controllers, 32-bit
timestamps) or a different transport entirely. If the 16-track bus needs
millisecond-scale alignment, MIDI 1.0 is the wrong protocol and the programme
should say so.

*What this means for our documents.* **Needs correction.** The
sub-millisecond figure is the number to kill, and the DIGEST already flagged it.
What replaces it is not a smaller version of the same claim but a different
one: MIDI 1.0 buys its event rate with alignment granularity, and the honest
statement of the design is "16 tracks, ≤65 events/s/track, MTC-quantised to
8.33 ms" — still a great deal of telemetry for 3 KB/s, and checkable.

MTC also has a bandwidth cost worth quoting: a quarter-frame message is 2 bytes
on the wire (status 0xF1 + one data byte) × 120 messages/s = **240 bytes/s,
i.e. 7.7% of the entire MIDI 1.0 line** before a single payload byte is sent.

### 2.3 "Near-zero overhead" — **MISLEADING-SCOPE**

- A 3-byte channel voice message spends **1 byte in status** (carrying the
  message type in 4 bits and the channel in 4 bits) — 33% framing overhead.
- Running status removes the repeated status byte, giving 2-byte messages. That
  is where "near-zero" comes from — and it is bought by removing exactly the
  framing that would let you detect a lost byte.
Sources:
- https://francoisgeorgy.ch/midi/low-level-midi/ (running status; 31.25 kbaud, 320 µs per byte)
- https://midi.org/midi-1-0-detailed-specification

- MIDI 1.0 has **no checksum, no sequence number, and no resynchronisation
  mechanism.** One dropped byte silently desynchronises the stream, and the
  receiver cannot tell a lost byte from a wrong value. "Near-zero overhead"
  is true only in the narrow sense of a small status byte, and it is purchased
  by giving up integrity. For a protocol whose job in this programme is
  *verifiable* transmission, that is the wrong trade, and the docs should
  name it rather than claim the benefit.

*What this means for our documents.* **Needs correction.** "Near-zero
overhead" is a real property of MIDI's framing and a false property of
MIDI's reliability. Say what it is (a 1-byte status header; running status
removes it) and say what it costs (no checksum, no sequence number, no resync
— one dropped byte silently corrupts the stream). For a programme whose
premise is verifiable transmission, naming the cost is the stronger claim.

### 2.4 "16 tracks of continuous telemetry" — **16 tracks VERIFIED, "continuous telemetry" REFUTED by arithmetic**

16 channels per MIDI 1.0 port is exactly right. The bandwidth does not work:

| Scenario | Bytes/s needed | vs. 3,125 B/s port |
|---|---|---|
| 16 tracks × 10 Hz × 3 B | 480 | 15.4% ✓ |
| 16 tracks × 30 Hz × 3 B | 1,440 | 46.1% ✓ |
| 16 tracks × 100 Hz × 3 B | 4,800 | 154% ✗ |
| 16 tracks × 1 kHz × 3 B | 48,000 | 1,536% ✗ |

Per-track ceilings on one port: **195 bytes/s → ~65 events/s/track** at 3 bytes
per event (one event every ~15 ms), or ~97/s with running status at 2 bytes.
Add MTC and you lose 7.7% of the line before any payload.

The chrono-tensor 4D event is `[time, channel, pitch, velocity]`, and MIDI
channel voice messages carry **no timestamp** — time comes only from the
clock/MTC layer or from a delta-time convention à la Standard MIDI File. At 4
bytes per event, 16 tracks gives 781 events/s total, **48.8 events/s/track**.
Sources:
- https://midi.org/midi-1-0-detailed-specification
- https://ccrma.stanford.edu/~gary/controllers/midi.html (CCRMA: "One start bit, eight data bits, and one stop bit result in a maximum transmission rate of 3125 bytes per second")
- https://academic.oup.com/book/27042/chapter/196335010


*What this means for our documents.* **Needs correction.** The 16-track design is fine; the
transport claim is not. Two options: (a) keep MIDI and state the real numbers
— 16 tracks, ≤65 events/s/track, MTC-quantised to 8.33 ms — which is still a
remarkable amount of telemetry for 3 KB/s; or (b) treat MIDI as the
*inspiration* and specify a transport that can actually carry the alignment
the design needs. Option (a) is cheaper and more honest, and it preserves the
claim that matters: a thin, typed, time-indexed event stream is exactly the
right shape for this system.

---

## 3. Math theorem scopes

*Claim as stated.* `chrono-tensor/docs/ideation/gemini1.md` applies six pieces
of mathematics as mechanisms: A₂/Eisenstein lattice snapping as a temporal
quantization grid; Laman rigidity as the definition of truth; Hodge
decomposition of seam friction as grad + curl + harmonic; Kuramoto phase
entrainment as the synchronisation mechanism; Orthogonal Procrustes for latent
alignment; and Dec-POMDP convergence to a globally optimal tactical map.

The good news, stated once: **`chrono-tensor/ARCHITECTURE.md` already scopes
every one of these correctly** — "A scheduling heuristic; no oscillators are
coupled and no equation is evaluated" (Kuramoto), "A triage label set; there is
no field and no decomposition operator" (Hodge), "a rigid graph can be rigidly
wrong, so rigidity is never a test of truth" (Laman), "no mechanism is given —
no addressing rule, no forwarding rule, no failure behaviour" (lattice
routing). The overclaims live in the ideation files. So this is a
**propagation** problem, not a discovery problem: the fix is to make the
ideation files say what the architecture doc already says.

### 3.1 Laman rigidity — "Truth is mathematically defined as a rigid graph" — **REFUTED**

*What the source says.* Laman's theorem characterises generic rigidity of
bar-and-joint frameworks **in the plane**: a graph is generically rigid in 2D
if and only if it is Laman — (2,3)-tight, i.e. |E| = 2|V| − 3 with every
subgraph on ≥ 2 vertices having ≤ 2|V'| − 3 edges (G. Laman, "On graphs and
rigidity of plane skeletal structures," *J. Engineering Mathematics* 4 (1970),
331–340).

The scope boundary matters and is documented: "In 1970, Laman obtained a
combinatorial characterization of rigid graphs in the Euclidean plane," and on
the 3D question — "**No Laman type theorem for 3 dimensional generic rigidity
is known!**" The Maxwell count (3|V| − 6) in 3D is necessary but not
sufficient; the standard counterexample is the *double banana*, which
satisfies the counts and is still not generically rigid in 3D.

- https://en.wikipedia.org/wiki/Laman_graph (the (2,3)-tight definition)
- https://arxiv.org/abs/1311.4859 (Cheng, Sitharam & Streinu, "Nucleation-free 3D
  rigidity": "For d = 3, characterizing rigid graphs … remains an elusive,
  long-standing open problem", and "Maxwell's counting condition in 3D is not
  sufficient, i.e., there are graphs that satisfy Maxwell's counting condition
  but are dependent")
- https://arxiv.org/abs/1703.04035 (Zhao et al., "Laman Graphs are Generically
  Bearing Rigid in Arbitrary Dimensions": "It is notable that Laman's Theorem is
  valid merely in two dimensions and a similar result does not exist in higher
  dimensions." — also the statement that in 3D+ "extra conditions and more edges
  are required")

*What this means for our documents.* **Needs correction.** Two distinct errors, and the second is
the worse one:

1. **Dimension.** Laman is a 2D result. "Decision graphs and dependency shards"
   in a distributed system are not planar bar-joint frameworks; the count
   2|V| − 3 has no meaning there.
2. **Category.** Rigidity is *geometric determinacy* — whether a configuration
   is pinned by its constraints. It is not a truth predicate. A framework can be
   perfectly rigid and encode a false proposition, and a framework can be
   flexible and encode a true one. "Truth is mathematically defined as a rigid
   graph" is a category error, and the DIGEST's rejection of it on exactly
   these grounds is correct.

ARCHITECTURE.md's version — "counting constraints in a dependency graph to
check whether the graph is fully determined… a rigid graph can be rigidly
wrong, so rigidity is never a test of truth" — is the sentence to keep.

### 3.2 A₂ / Eisenstein lattice — **MISLEADING-SCOPE (but a good design choice)**

*What the source says.* A₂ is the hexagonal / planar triangular lattice, and it
is the densest lattice packing in **two** dimensions: "The sphere packing
problem in Rⁿ is trivial for n = 1, and the answer has long been known for
n = 2: the standard hexagonal packing is optimal" and "A₂ is the unique
densest lattice in two dimensions" (Cohn & Kumar, *New upper bounds on sphere
packings I*, Annals of Mathematics 157 (2003)). Densest lattice packings are
known for n ≤ 8 and n = 24, and they are *not* A₂ beyond dimension 2: A₃ (=D₃,
FCC) in 3D, D₄ in 4, E₆ in 6, E₈ in 8, Leech in 24.

- https://annals.math.princeton.edu/2003/157-2/p05

*What this means for our documents.* **Needs the claim narrowed.** The A₂ optimality theorem is a
**2-dimensional, measure-specific** result about packing or quantization cells.
It says nothing about "high-dimensional intent vectors." But the chronology
document's actual use of A₂ is the 2D one — a hexagonal *time* grid — and a
hexagonal time grid is a perfectly reasonable design choice (it gives every
grid point six neighbours, which is the natural match for a hexagonal
addressing scheme). So: keep the lattice, drop the implication of
optimality. Say "hexagonal time grid, the densest 2D lattice, chosen because
its six-neighbour structure matches the Eisenstein addressing scheme" rather
than implying an optimality that does not transfer.

### 3.3 Dec-POMDP — "convergence to a globally optimal tactical map" — **REFUTED as stated**

*What the source says.* Bernstein, Givan, Immerman and Zilberstein, "The
complexity of decentralized control of Markov decision processes," *Mathematics
of Operations Research* 27(4):819–840 (2002): the finite-horizon m-agent
Dec-POMDP **policy-existence** problem — "is there a joint policy for which
V ≥ K?" — is **NEXP-complete** for m ≥ 2, and the finite-horizon Dec-MDP is
likewise NEXP-complete. The authors note this is "in contrast to the best known
bounds for MDPs (P-hard) and POMDPs (PSPACE-hard)," and that since P ≠ NEXP is
known, "the problems we consider are provably intractable." Restricted
variants are easier but still not free: transition-independent Dec-MDP is
NP-complete; Dec-MDP with independent transitions, observations and rewards is
P-complete. And from the Dec-POMDP survey: "all infinite-horizon
Dec-POMDP-based models are also undecidable."

- https://people.cs.umass.edu/~immerman/pub/bgiz.pdf (Bernstein et al. 2002)
- https://people.csail.mit.edu/camato/publications/DecPOMDP-survey-final.pdf

*What this means for our documents.* **Needs the claim narrowed.** There is no general guarantee of
convergence to a globally optimal joint plan, and even the finite-horizon
*existence* question is provably intractable. The realistic routes —
low-rank / mean-field approximations to Dec-POMDPs, or simply comparing and
aggregating proposals — are approximations, not optimality guarantees.

The programme's own instinct is already right here: beam-quilt's v0 chooses
**comparative picking** (the receiver ranks the competing pool against each
other) over absolute scoring, and `docs/06-moe-modularized.md` prices latent
seams as an explicit research option. Comparative picking is a per-pool
comparison, which is exactly the kind of local operation that stays tractable.
Write it that way and the Dec-POMDP citation becomes an argument for the
existing design rather than against it.

### 3.4 Kuramoto — **MISLEADING-SCOPE, and the prose misstates the model**

*What the source says.* The model is
dθᵢ/dt = ωᵢ + (K/N) Σⱼ sin(θⱼ − θᵢ), over N limit-cycle oscillators, each a
**single phase variable θᵢ on the unit circle**. Wikipedia states its
assumptions directly: "The model makes several assumptions, including that
there is **weak coupling**, that the oscillators are **identical or nearly
identical**, and that interactions **depend sinusoidally on the phase
difference** between each pair of objects." Synchronisation occurs for K above a
critical K_c determined by the *distribution* of natural frequencies g(ω), and
the Ott–Antonsen ansatz reduces the infinite system to one equation for the
order parameter under the mean-field assumption.

- https://en.wikipedia.org/wiki/Kuramoto_model
- https://arxiv.org/html/2605.01021v1 ("the Kuramoto model on the
  D-dimensional torus" — the canonical statement of the model: "each oscillator
  is represented by a phase variable θᵢ(t), defined on the unit circle, that
  evolves with a natural frequency ωᵢ")

*What this means for our documents.* **Needs correction.** Two problems, one of them a plain
misreading of the model.

**The prose misstates the model.** `gemini1.md` says: "When coupling density
(K) exceeds local variance (ωᵢ), phase angles collapse into a unified
frequency." That is wrong. ωᵢ is oscillator *i*'s own natural frequency, not a
variance; the actual condition is K > K_c with K_c set by the width of the
frequency distribution. This appears twice in `gemini1.md` (once in the
equation block, once in the "Clockless Synchronization" section). Correct it
or cut the sentence.

**The preconditions are not met by the proposed system.** To use Kuramoto for
agent cadence you need (a) each agent to have one continuously advancing phase
scalar on a circle — an agent that performs discrete, resettable actions does
not; (b) the coupling to be a function of *observable* phase difference, which
requires the agents to be able to see each other's phase, which requires a
shared clock — the very thing "Clockless Synchronization" says the design
avoids; (c) weak coupling and near-identical frequencies for the mean-field
reduction to apply; (d) an order parameter r whose value means something for
the task.

ARCHITECTURE.md's framing — "nodes adjusting their cadence toward the cadence
they observe in neighbours. A scheduling heuristic; no oscillators are coupled
and no equation is evaluated" — is accurate and sufficient. Adopt it, and keep
the equation as an illustration of the *shape* of the idea with the
preconditions stated.

### 3.5 Orthogonal Procrustes for latent alignment — **MISLEADING-SCOPE**

*What the source says.* Orthogonal Procrustes solves
min_{W ∈ O(d)} ‖XW − Y‖_F with closed form W* = UVᵀ from the SVD of XᵀY
(Schönemann 1966), where X ∈ ℝ^{n×d} and Y ∈ ℝ^{n×d} are **paired**
correspondences — n is the number of paired points. Every practical application
in the literature fits it on paired data: multilingual alignment fits the
rotation on "parallel sentence pairs"; cross-modal alignment fits it on paired
image–text features; the multi-way generalisation "we study the common case
where the correspondence is available."

*What this means for our documents.* **Needs the claim narrowed.** Classical OP **requires paired
correspondences**. It cannot align the latent spaces of independently trained
models from nothing — that is precisely the problem it is usually asked to
solve. Unpaired alternatives exist and are a live research area (Wasserstein
Procrustes aligns two independently trained spaces from shared geometry alone
with no pairs; Gromov–Wasserstein is NP-hard in general; CCA on matched prompts
and seed-based alignment are the standard practical routes) — but those are
different algorithms with weaker guarantees, usually coarse alignment rather
than the exact isometry OP promises.

The programme's current stance is right and should be made explicit instead of
being contradicted by a Procrustes reference. `docs/06-moe-modularized.md` and
the DIGEST both reject "raw latent vectors between different models" as the
default seam and require typed profiles and typed questions, with latent seams
priced as an explicitly labelled research option. The Procrustes mention should
be scoped to *why*: "Orthogonal Procrustes requires paired correspondences
(e.g. matched prompts), which is exactly why the default seam is typed rather
than latent — an unpaired alignment is a different and coarser problem."

This also disposes of the DIGEST's flagged conflict (#3: gemini8/9 propose raw
latant vectors "without shared calibration" and also a negotiated transition
matrix "which *is* calibration"). The Procrustes citation is what makes that
contradiction visible, so it earns its place — as the reason for the typed
seam, not as an alternative to it.

- https://aclanthology.org/2026.sigul-1.23.pdf (Procrustes alignment via parallel sentence pairs)
- https://openreview.net/pdf?id=educGk5ykl (closed-form OP on paired image–text features; returns HTTP 403 to scripted fetches, opens normally in a browser)
- https://arxiv.org/html/2610.09411 (Wasserstein Procrustes, unpaired alignment)
- https://arxiv.org/html/2602.06205 (multi-way alignment; "we study the common case where the correspondence is available")

### 3.6 Hodge decomposition of seam friction — **MISLEADING-SCOPE**

*What the source says.* The Hodge decomposition splits a flow on the
**1-simplices (edges)** of a weighted **simplicial complex** into three
orthogonal parts, X = X_G + X_C + X_H, with the Hodge Laplacian
L₁ = B₁ᵀB₁ + B₂B₂ᵀ. The curl space is im(B₂) — it is generated by **2-simplices
(filled triangles)**, so a bare graph is insufficient: "the curl flow arises
from triangle-induced flows"; the chain complex is
C₂ → C₁ → C₀ with boundary operators satisfying ∂₁∂₂ = 0. On graphs one can
supply the missing structure by taking the **2-dimensional clique complex**
"of graph G," but that must be stated and computed. The harmonic component
reflects global cycles — "a harmonic eigenvalue just means that there exists a
hole in the data set, without any information on its size."

- https://pmc.ncbi.nlm.nih.gov/articles/PMC12310137/ (Hodge decomposition of functional human brain networks)
- https://ar5iv.labs.arxiv.org/html/2211.10542
- https://www.stat.uchicago.edu/~lekheng/meetings/hodge/hirani.pdf (Hirani & subdivided meshes — "Chain and cochain complexes on 2-dimensional clique complex of graph G")

*What this means for our documents.* **Needs the claim narrowed.** Applying Hodge decomposition to "seam
friction between two agent proposals" requires three things the ideation
document never supplies: (i) an oriented, weighted structure whose edges carry
a flow; (ii) 2-simplices (or a stated clique complex) for the curl component
to exist at all; (iii) the flow itself as the object being decomposed. Two
proposals are not an edge flow, and there is no field here to decompose.

The three-bucket triage — locally fixable noise / loops and deadlocks /
structural bottlenecks — is a *good* taxonomy. It just needs to be presented as
a taxonomy, with the Hodge decomposition as the metaphor that motivates it and
an honest note that instantiating it would require constructing the complex,
the weights, and the flow. ARCHITECTURE.md already says exactly this ("A
triage label set; there is no field and no decomposition operator"). Adopt
that, and additionally note the one substantive caution the theory does supply:
the harmonic component identifies *existence* of a hole, not its size or
severity — so "isolates the harmonic bottleneck" over-reads what the
decomposition can tell you.

---

## 5. Is a 6.8M-parameter small judge at ~7 ms on ARM plausible? — **PLAUSIBLE, with two caveats and one unresolvable gap**

*Claim as stated.* `docs/04-jev-pairs.md`: "A **Jev** is a small, cheap
judgment model: it takes a question and returns a *typed* answer — a
percentage, a yes/no, a classification — fast (the workshop's student judge:
6.8M parameters, ~7 ms on ARM)." `README.md` repeats it: "a small student judge
(6.8M parameters, ~7 ms on ARM)". These are our own measurements, repeated
across the programme.

### 5.1 The latency is plausible — **YES, and the arithmetic is comfortable**

A 6.8M-parameter model at INT8 is 6.8 MB of weights. One forward pass is
6.8M MACs ≈ 13.6 MFLOPs. A single ARM core with NEON INT8 GEMM sustains
roughly 2–8 GOPS, so one classification pass lands at ~2–7 ms. The claimed
number sits in the middle of that band.

The published edge numbers corroborate it from the outside. In the "Cloud to
Edge" edge-LLM benchmark, **Llama 3.2 1B on a Raspberry Pi 5 CPU** (Cortex-A76
@ 2.4 GHz) runs at **0.39 tok/s** — about 2.6 s per token — and the M5Stack
(AX630C, 2 cores @ 1.2 GHz) reaches **3.44 tok/s**. Scale 1B → 6.8M (≈147×
smaller) and a single-token step becomes ~18 ms on a Pi 5 and ~2 ms on the
M5Stack. A judge is *one* classification forward pass rather than a
100-token autoregressive decode, so **single-digit milliseconds on ARM is
squarely inside the published envelope**, with the M5Stack-class hardware
comfortably under it.

- https://arxiv.org/html/2604.24785v1 (edge LLM inference benchmark: RPi 5 / M5Stack / Jetson Orin throughput and TTFT)
- https://arxiv.org/html/2601.03290 (lightweight transformers on ARM: EdgeFormer 5.0M params, MobileViT 6M params, INT8 quantization)

### 5.2 Caveat 1 — the artifact is not public, so neither number is independently verifiable

`jev-semantic/SETUP.md` copies the model in from
`agent-inbox/payloads/intuition-bench/intuition_student.onnx*`, and that path
is not in any public repository under `SuperInstance/`. The public
`jev-semantic/README.md` asserts only: "The student judges at 7ms on ARM,
bit-identical across architectures." There is no public weight file, no
benchmark script, and no recorded hardware specification against which either
figure can be checked.

This is not a claim that is false — it is a claim that cannot currently be
audited by anyone else. That matters for a programme whose entire argument is
that judgments are *verifiable measurements*: "A judgment is a measurement, not
a derivation: verify by tolerance, not by hash" (`jev-semantic/README.md`). A
measurement the reader cannot reproduce is a measurement in name only.

**Cheapest fix:** publish the ONNX file (or its SHA-256 plus the parameter
count) and a one-command benchmark script that prints the input shape, the
quantization scheme, the ARM board and core, and the median of N runs.
Everything else in this section follows from that.
Sources:
- https://github.com/SuperInstance/jev-semantic/blob/main/SETUP.md (the `cp agent-inbox/payloads/intuition-bench/intuition_student.onnx* ~/node/` path)
- https://github.com/SuperInstance/jev-semantic/blob/main/README.md ("The student judges at 7ms on ARM, bit-identical across architectures.")

*What this means for our documents.* **Needs the claim narrowed** — not
because the number is wrong, but because a number nobody else can check is not
yet evidence. Publish the artifact and the benchmark; the claim becomes
auditable in an afternoon.

### 5.3 Caveat 2 — a 6.8M-parameter transformer cannot read a prompt in 7 ms, so "takes a question" needs narrowing

Prefill dominates for small transformers: N input tokens cost N × params MACs.
At 6.8M parameters, a 512-token prompt is ~3.5 GMACs ≈ 7 GFLOPs, which on a
Pi-5-class core at ~4 GOPS is roughly **1.7 seconds, not 7 milliseconds**. Even
64 tokens is ~220 ms.

So a 7 ms / 6.8M judge almost certainly does **not** ingest arbitrary-length
prose. It either takes a compact fixed-size input (an embedding lookup plus an
MLP head, a hashed-feature classifier, or a pre-computed window embedding), or
it uses an early-exit design.

**What this means for our documents.** **Needs the claim narrowed.** `docs/04-jev-pairs.md` currently reads
"it takes a question and returns a typed answer." That is fine as an
abstraction, but if any reader takes it as "the judge reads the question text
and answers in 7 ms," the number will not survive contact with the arithmetic.
State the input contract explicitly — what the judge actually receives (an
embedding? a window hash? a fixed-length feature vector?) — and the 7 ms
becomes a checkable claim rather than an impressive one. The `window.py`
compiler in `jev-semantic` ("compiles an agent's window… compile the window")
is the natural place where that contract is already implicit; it should be
explicit in the docs too.
Sources:
- https://github.com/SuperInstance/jev-semantic/blob/main/README.md (the `window.py` compiler description)
- https://arxiv.org/html/2604.24785v1 (edge-LLM throughput, for the scaling comparison)

*What this means for our documents.* **Needs the claim narrowed.** State the
judge's input contract. If it takes a fixed-size embedding or feature vector,
say so and "6.8M / 7 ms / ARM" becomes exact and checkable. If it reads text,
the 7 ms figure is measuring something other than the judgment, and the
document should say what it is measuring.

### 5.4 Caveat 3 — "bit-identical across architectures" is not true of quantized neural inference in general

`jev-semantic/README.md`: "The student judges at 7ms on ARM, bit-identical
across architectures." If "across architectures" means across ARM vs x86, or
across ARM cores, that is **not** a property that general quantized inference
provides. Different kernels, different NEON lane widths, FMA contraction and
reassociation, and different accumulation orders all produce different
rounding across implementations. What *is* achievable is bit-identical
behaviour under a **written-down quantization contract**: fixed-point weights,
fixed accumulation order, no reassociation, a specified rounding mode.

This is worth resolving carefully, because the judgement log is content-addressed
by encoder hash and the README states "Two bodies may log the same key; their
disagreement is data." If the same judge on two different machines can produce
different bytes, then a disagreement is not necessarily a disagreement about the
question — it may be arithmetic noise wearing the costume of a semantic
signal. That is exactly the kind of contamination the SPEC's own alarm
design is built to detect, so the contract should be pinned the same way.

**Cheapest fix:** either write the quantization contract down (INT8 weights,
INT32 accumulators, specified reduction order, no fast-math) and keep the
claim, or soften the wording to "reproducible to within tolerance on the
architectures we run" — which matches the README's own stated principle,
"verify by tolerance, not by hash."
Sources:
- https://github.com/SuperInstance/jev-semantic/blob/main/README.md
- https://developer.arm.com/community/arm-community-blogs/b/ai-blog/posts/accelerating-on-device-ai-a-look-at-arm-and-google-ai-edge-optimization (Arm/Google AI Edge: quantization and accumulation behaviour changes with kernel and core; returns HTTP 403 to scripted fetches, opens normally in a browser)
- https://en.wikipedia.org/wiki/Quantization

*What this means for our documents.* **Needs correction.** Either pin the
quantization contract and keep the strong claim, or soften the wording. Do not
leave a load-bearing reproducibility guarantee resting on a property that
general quantized inference does not have — especially in a system whose whole
argument is that judgment logs are trustworthy data.

### 5.5 Verdict

**PLAUSIBLE.** The 6.8M / ~7 ms / ARM combination is consistent with published
edge-inference measurements and internally arithmetically sound for a
single-forward-pass judge. Nothing here requires changing the number. What it
requires is (a) publishing enough artifact for the number to be auditable,
(b) stating the judge's input contract so "takes a question" and "7 ms" are
compatible, and (c) pinning the quantization contract behind the
"bit-identical" claim.

---

# Summary

## Verdict table

| # | Claim area | Verdict | One-line finding |
|---|---|---|---|
| 1 | PLATO/TUTOR/OPAL/CERL history | **Mixed — 3 verified, 4 narrowed, 2 refuted** | TUTOR-at-FAA-until-2006, common blocks, the OPAL name/toolchain and the 16-voice GCS are solid; Empire's "30 players in 1973" is a version composite (Empire I was 8-player turn-based; the 30-player cap is Empire IV's, by 1977); OPAL was *not* integrated into TUTOR; "1–2 clock cycles via barrel shifts" is refuted; no documented OPAL→MIDI lineage |
| 2 | MIDI arithmetic | **Protocol VERIFIED, transport claims REFUTED** | 31.25 kBaud / 16 channels confirmed; a 3-byte event takes ~960 µs and the best sync is 8.33 ms (MTC) — "sub-millisecond alignment" is wrong |
| 3 | Math theorem scopes | **All six MISLEADING-SCOPE or REFUTED** | Laman is 2D and is not truth; A₂ optimality is 2D-only; Dec-POMDP policy existence is NEXP-complete; Kuramoto's stated condition is misread; Procrustes needs paired data; Hodge needs a simplicial complex and a flow |
| 4 | Anthropic ARA figures | **VERIFIED (all five)** | Gemma4-26B-A4B on 15 Android TV boxes at 0.39 tok/s; 9B/8-of-9 hardware types; Memento 45%→61–84%; GLM-5.2 8/8 nodes in 4 of 5 trials; +13.7 ECI/yr as a projection — all confirmed verbatim |
| 5 | Small judge on ARM | **PLAUSIBLE, unverifiable from public artifacts** | 6.8M at ~7 ms is inside the published edge envelope; publish the ONNX, state the input contract, pin the quantization contract |
| 6 | Account scale | **VERIFIED with a caveat that changes the number** | 5,306 public repos total — but 821 are forks; the non-fork count is 4,485 |

## What this fact-check changes

**The DIGEST's own calls were right.** Every item the DIGEST rejected on
evidence — "truth is a rigid graph", the PLATO/TUTOR lineage claims, "MIDI's
3 KB/s cited as enabling sub-millisecond alignment argues against itself", the
unquoted `chrono-tensor/ARCHITECTURE.md` framings — is confirmed here with
citations. Nothing in this lane overturns Casey's sorting pass; this lane
supplies the receipts for it and sharpens three of the findings.

**The programme is not in trouble; its ideation files are.** `ARCHITECTURE.md`
already scopes all six mathematical mechanisms correctly. The overclaims live
in `chrono-tensor/docs/ideation/gemini1.md` and `gemini2.md`. So the fix is a
propagation fix — align the ideation prose with the architecture prose — not a
redesign.

**Concrete edits, in priority order:**

1. **`gemini1.md` and `gemini2.md` — delete or rewrite the MIDI transport
   claims.** Replace "sub-millisecond alignment" with the real numbers (320 µs
   byte, ~960 µs per 3-byte event, 8.33 ms MTC quarter-frame at 30 fps, 20.83 ms
   MIDI clock at 120 BPM). Decide explicitly whether MIDI 1.0 is the transport
   or the inspiration; if the former, the 16-track design needs ≤65 events/s/track
   and MTC-quantised alignment, and that should be written down as a constraint.
2. **`gemini1.md` — fix the Kuramoto condition.** "When coupling density (K)
   exceeds local variance (ωᵢ)" is wrong on two counts. Use `ARCHITECTURE.md`'s
   framing and state the three preconditions (single phase variable per node,
   observable phase differences, weak coupling / homogeneous frequencies).
3. **`gemini1.md` — drop "Truth is mathematically defined as a rigid graph."**
   Replace with the two corrections (2D only; rigidity is determinacy, not
   truth) and adopt `ARCHITECTURE.md`'s sentence.
4. **`gemini1.md` — scope the Hodge decomposition.** State that it needs an
   oriented weighted complex with 2-simplices and an edge flow, that none of
   those exists yet, and that the three-bucket triage is a taxonomy borrowed
   from it. Also note that the harmonic component detects the *existence* of a
   hole, not its severity.
5. **`gemini2.md` — correct the OPAL and CDC claims.** OPAL was a separate
   PLATO-compatible language with its own compiler and editors, not a TUTOR
   extension; drop "vectorized the score" and "ultra-compact"; delete the
   "1 or 2 clock cycles via native barrel shifts" clause. Keep the name and
   the expansion — "OPAL (Octave-Pitch-Accent-Length)" is confirmed by the
   PLATO article — and keep the compiler/binaries/real-time-playback toolchain
   description, which is confirmed by the ED161421 abstract.
6. **`gemini2.md` — change "inherited" to "converged on" for MIDI.** The
   documented MIDI lineage runs through USI/DCB/Roland-Sequential, not PLATO.
7. **`gemini2.md` — qualify the two PLATO game claims.** Empire: the 1973
   original (Empire I) was 8-player turn-based — the 30-player real-time cap
   is Empire IV's, by 1977 — 30 players total, ≤15 per team, screen updates
   1–2 s, 1200-baud link, ~20 keys/s. Avatar: 60 concurrent *system-wide*,
   ~15 per dungeon group.
8. **`README.md` / `DIGEST-2026-10-21.md` — make the repo count precise.**
   "5,306 public repositories, of which 4,485 are non-forks." If the intent is
   ~5,000 distinct authored projects, the number is 4,485.
9. **`docs/06-moe-modularized.md` — two small additions.** Name
   Llama3.1-8B as the model the 9B setup task targets, and name Qwen3.5-9B
   explicitly. Both are safe and make the 8-of-9 figure comparable across
   models. The "trend-line projection, not a measurement" line is already
   exactly right and should stay.
10. **`jev-semantic` — publish the judge artifact and its contract.** Add the
    ONNX file or its hash, a benchmark script, the hardware spec, and the
    quantization contract behind "bit-identical across architectures"; state
    the judge's input contract so "takes a question" and "~7 ms" are
    simultaneously true.

## Method and limits

- Every claim was located in the public repos first, via the GitHub API, so
  each verdict is against a quoted sentence rather than a paraphrase. Sources
  read in full: `beam-quilt` README/SPEC/PROGRAM-MAP, `docs/01`–`docs/09`,
  `docs/proposed/DIGEST-2026-10-21.md`, `docs/proposed/gemini1–22.md`;
  `chrono-tensor` README/SPEC/ARCHITECTURE/ROADMAP and
  `docs/ideation/gemini1–3.md`; `jev-semantic` README/SETUP/SPEC.
- The Anthropic post was fetched live and its text extracted before any figure
  was compared, so no figure was checked against a secondary summary.
- **Every URL cited in this dossier was link-checked** (HTTP GET, 2026-10-21).
  All resolve except three that return HTTP 403 to scripted requests and open
  normally in a browser (academic.oup.com, openreview.net, developer.arm.com),
  which is noted inline. The `api.github.com/users/SuperInstance` call was
  verified directly during the session and is subject only to the anonymous
  rate limit.
- **Two things I could not settle.** (i) The ERIC full text of ED161421
  (Gooch, "PLATO Music Systems") is **not digitised** — ERIC lists it as "PC Not
  Available from EDRS". Its abstract is quoted verbatim in ED195288 and the
  language's name and expansion are independently confirmed by the PLATO
  article, so the OPAL claims are settled; but I read the abstract, not the
  17-page paper. (ii) The `intuition_student.onnx` artifact is not in any
  public repository, so the 6.8M parameter count and the 7 ms figure cannot be
  audited by anyone else — see §5.2 for what would settle it.
- Historical claims about PLATO rely heavily on Wikipedia plus the primary ERIC
  and Computer History Museum material cited alongside it; where the two
  disagree on a detail (e.g. whether the PLATO terminal was a "vector CRT" or a
  bitmap with hardwired vector plotting), the PLATO article and the primary
  documents were preferred.
