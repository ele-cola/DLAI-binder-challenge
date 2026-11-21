# Journal — Binder Girls

Research, reasoning, papers, what we ran and what happened. 

**How to use this file**

- One entry per working session, newest at the bottom.
- Date and initial every entry: `## YYYY-MM-DD — ECo` (ECo / DB / ECi).
- **Never edit a past entry.** If it turned out wrong, write a new entry saying
  so. 
- Cite sources inline.
- When something here hardens into a choice that constrains later work, it
  graduates to `DECISIONS.md`. It stays here too; the journal is the working-out,
  the decision file is the commitment.
- Numbers about individual designs go in `designs.csv`, not here. Test: would
  you ever want to ask "how many" or "which ones"?

**`designs.csv` columns and reason codes**

`design_id, date, who, source_run, status, stage_cut, reason_code, metric, value, threshold, notes`

`status` is either `kept` or `cut`. Log the row **when you cut the design**, not when
you write the rationale. Record `value` and `threshold` even for designs that
looked bad — `metrics.csv` in the submission must report every metric actually
used to filter.

`reason_code`, agreed in advance so the log is countable: `clash` ·
`low_sc` (shape complementarity) · `low_bsa` (buried surface area) ·
`no_contact` (pocket misses the chosen face) · `wrong_orientation` (Adda folded,
not extended — see D-001) · `no_carboxyl_contact` (nothing positioned for
D-MeAsp) · `length` (outside 60–140) · `af2_fail` (doesn't fold as designed) ·
`boltz_low_conf` · `disagreement` (AF2 and Boltz disagree) ·
`duplicate_backbone` · `no_output_face` (no free terminus or surface patch) ·
`expression_risk`. Add a code only by agreement, and add it to this list when
you do.

**Entry template**

```
## YYYY-MM-DD — XX

**Did.** What I ran or read.

**Result.** What happened, including the boring and the failed. Paste the
command and the error if there was one.

**Thinking.** What I think it means. Flag where I'm guessing.

**Open.** Questions left, and what I need to discuss with the team.
```

---

<!-- Backfilled entries from the old "progress" doc go here, each under its
     original date and marked [backfilled 2026-11-21]. -->

## 2026-11-02 — ECi

**Did.** Wrote the process-journal document setting out the target face,
specificity intent, exclusions, pocket specification, stated confidence and
falsification criteria. No designs generated at the time of writing.

**Result.** We decided to commmit to Adda5 + the D-MeAsp3 carboxyl on the conserved face of
the ring. Broad specificity, accepting likely nodularin cross-reactivity.
Explicitly excluded D-Glu6, Mdha7 covalent capture, and water-mediated contacts.

**Thinking.** Recorded in full as **D-001** in `DECISIONS.md`.

**Open.** Need to discuss with DB and ECo as we have a few open questions to answer 
about the geometry of Adda and the water-mediated contacts of PP1. We also have to 
decide on a confidence figure which we commit to before any data exists, because the 
challenge grades calibration. 

---

## 2026-11-20 — ECo

**Did.** First RFD3 run against microcystin-LR. Target file prepared from 1LCM
by forcing all records to `HETATM`
(`sed -e 's/^ATOM  /HETATM/' 1LCM.pdb | grep -v "^SEQRES" > 1LCM_het.pdb`).

**Result.** Runs in 24 s on an A100-80GB at `diffusion_batch_size=1`. Output
contains both the designed chain and the toxin. The polymer/non-polymer parser
failure (`Residue A1 not found in atom array`) is fixed by the HETATM rewrite.
Hotspot syntax verified separately.

**Thinking.** Pipeline proof only — this backbone is **unconditioned**,
centre-of-mass orientation with no face, no burial requirement and no hotspots.
Not a candidate, and should not be logged in `designs.csv` as one. Sampling
volume is clearly not going to be the constraint at 24 s/design :) .

**Open.** Real hotspot atoms on Adda, picked from PyMOL geometry rather than
guessed from atom names (DB). LigandMPNN in ligand-aware mode on an RFD3 output
(ECo).

---

## 2026-11-21 — ECo

**Did.** Replaced the single shared "progress" doc with `JOURNAL.md`,
`DECISIONS.md` and `designs.csv` in the fork.

**Result.** Files created; old doc still to be migrated across.

**Thinking.** One doc mixed append-only history, current state and numbers, so
none of the three was trustworthy — no way to separate what we believed in
November from what we believe now, and nothing countable for the filtering
section of the rationale. ECi suggested we create a more formal document to 
track our progress, resulting in the creation of `JOURNAL.md`.

**Open.** Migrate the old doc. Agree at the next team meeting that all three of
us actually write here.
