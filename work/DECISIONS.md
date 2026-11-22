# Decision records — Binder Girls

Choices we have committed to that constrain later work. Numbered, append-only,
newest at the bottom. 

**Entries are immutable.** If a decision changes, add a new one and set the old
entry's status to `superseded by D-00N`. Do not rewrite history: the dated
commit is the evidence that the reasoning came before the data.

`Status`: `proposed` · `accepted` · `superseded by D-00N` · `rejected`

Feeds RATIONALE.md sections *Target face*, *Approach*, *The three* and *What
would prove us wrong*.

**Template**

```
## D-00N — <the decision, written as a statement>

- **Date:** YYYY-MM-DD · **Present:** EC, DB, ECi · **Status:** accepted

**Context.** What forced a choice, and what was known at the time.

**Alternatives considered.** Each with why not.

**Decision.** What we are doing.

**Reasoning.** Why. Cite sources.

**What would prove us wrong.** Specific enough to check — a metric and a
threshold, or a result we would expect not to see.

**Consequences.** What this commits us to downstream.
```

---

## D-001 — Target Adda5 and the D-MeAsp3 carboxyl on the conserved face, for broad microcystin detection

- **Date:** 2026-11-02 · **Present:** ECi · **Status:** proposed 

Transcribed from the team process journal written 02.11.2026. **No designs had
been generated at the time of writing**.

**Context.** The brief requires a buried, shape-complementary pocket against a
chosen face of microcystin-LR, and states that an accident of which pocket the
model liked is not a decision. Everything downstream — hotspot atoms, burial
conditioning, hydrogen-bond targeting — depends on this.

**Alternatives considered.**

1. **The two variable positions, Leu2 and Arg4** — would give congener
   resolution. Rejected: Arg4 is the most mobile residue in both of our
   flexibility comparisons, so a congener-specific pocket would be built around
   the least predictable part of the molecule, and the sensor goes blind when
   the bloom switches producer.
2. **D-Glu6** — rejected: PP1 reaches it through two catalytic metals and
   bridging waters. A metal-free miniprotein cannot reproduce that, so designing
   for it would spend sampling on an unavailable interaction.
3. **Mdha7 / covalent capture** — rejected: irreversible, and a reusable
   competitive sensor requires reversible binding.
4. **Water-mediated contacts generally** — rejected: poorly modelled by the
   tools available to us.

**Decision.** Adda5 plus the D-MeAsp3 carboxyl, on the conserved face of the
ring. Specificity intent is **broad**, not congener-resolving.

**Reasoning.** Adda carries 12 of the contacts PP1 makes and roughly 77% of the
toxin is buried in that complex; D-MeAsp adds two clean polar handles (the
Arg96 and Tyr134 equivalents). Both are conserved across the congener family.
The WHO guideline value is written as *total* microcystin-LR, free plus
cell-bound, so broad detection matches the unit the device has to report.
Accepted consequence: likely cross-reactivity with nodularin and no congener
resolution — for a public-health alert device that is the right trade.

**Pocket specification committed to.**

- Aromatic/aliphatic channel (Val/Ile/Leu/Pro/Trp/Tyr character) for the
  extended Adda tail, captured in a **specific orientation** rather than a
  folded one
- One or two Arg/Lys positioned for the D-MeAsp carboxyl
- Zero cysteine, no disulfides, soluble and stable in *E. coli*
- One terminus and an adjacent surface patch kept free for later sensor
  attachment or a split site

**Stated confidence, before data.**

- We expect **most or all three designs to fail** to reach single-digit nM.
  Small-molecule binder design at this scale and polarity is a frontier problem.
- Our actual claim is narrower and testable: **Adda-targeting designs should
  show binding (any measurable shift) more often than a D-Glu- or
  variable-position-targeting design would.** That is the hypothesis we are
  betting the semester on.
- Confidence that at least one design shows a detectable thermal shift with
  ligand: (confidence score still to be decided, still a placeholder). 

**What would prove us wrong.**

- A design targeting the **variable positions** binds measurably better than our
  conserved-face designs → our conservation argument was the wrong axis.
- Our designs bind but with **no congener cross-reactivity** → we did not
  actually engage the conserved face, whatever the model showed.
- Binding tracks **total hydrophobic burial** rather than Adda-specific shape
  complementarity → the pocket is acting as a generic greasy pit, not a
  recognition site, and would fail in lake-water condition.
- nanoDSF shift present but **no displacement of the fluorescent tracer** → we
  built a binder, not a sensor, and the free-terminus / split-site planning was
  insufficient.

**Consequences.** Fixes the hotspot atom set (Adda, picked from PyMOL geometry),
the burial and hydrogen-bond conditioning in the RFD3 input JSON, and
`target_face` in `designs.csv` — e.g. `Adda + D-MeAsp carboxyl (conserved)`.

---

## D-002 — Exclude cysteine at the sequence-design step rather than filtering for it later

- **Date:** 2026-11-__ · **Present:** _ _ · **Status:** proposed

**Context.** Mdha7 is electrophilic and forms a covalent adduct with Cys273 in
PP1. D-001 excludes covalent capture; the brief also prohibits disulfides.

**Alternatives considered.** Generate freely and filter for cysteine proximity
afterwards — wastes sampling and risks a late surprise in a design we have
already committed to.

**Decision.** `--omit_AA C` in every LigandMPNN invocation.

**Reasoning.** Free to enforce during generation, removes a failure mode from
filtering, and satisfies the no-disulfide requirement by construction. Cost is a
marginal loss of sequence diversity.

**What would prove us wrong.** Nothing expected — a hard constraint, recorded
because the reason belongs in the rationale and because the flag has to be on
every command for it to hold.

**Consequences.** Any design produced without `--omit_AA C` is void regardless
of how it scores.

---

## D-003 — Validate complexes with Boltz rather than the AlphaFold server

- **Date:** 2026-11-__ · **Present:** _ _ · **Status:** proposed

**Context.** `af2_run.py` on the cluster runs ColabDesign AF2 in hallucination
protocol from sequence alone, with no ligand — it says whether the protein folds
as designed, nothing about whether the toxin binds.

**Alternatives considered.** `alphafoldserver.com`, which the repo suggests but
which accepts only a fixed list of common ligands, almost certainly excluding
microcystin-LR. AF3 parameters, which need a request to DeepMind with a real
turnaround — a schedule risk against 30.01.

**Decision.** Boltz as the primary complex predictor; AF2 kept as a fold-only
sanity check and reported as such.

**Reasoning.** Open weights, available now, handles an arbitrary ligand. Being
explicit that AF2 validates fold and not binding matters for calibration —
reporting an AF2 number as evidence of binding is exactly the overclaim this
challenge penalises.

**Consequences.** `method` in `designs.csv` reads
`RFdiffusion3 + LigandMPNN + Boltz`. AF2 and Boltz metrics are logged under
distinct `tool` values so the two are never conflated.
