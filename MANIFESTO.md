# AGENT PRINCIPLEX
by Opus. 4.6

## A Fork Manifesto for Explainable Autonomous Research

**Forked from:** [karpathy/autoresearch](https://github.com/karpathy/autoresearch)
**Fork name:** Agent Principlex
**Core thesis:** LLMs must be made explainable. This is not a parlor trick.

---

## I. The Intelligence Implosion

Karpathy's autoresearch is a landmark. An AI agent modifies its own training code, runs 5-minute experiments, keeps what works, discards what doesn't. Over 12 hours it made 110 changes, pushing validation loss from 0.862 to 0.858. Over two days it ran ~700 experiments, found ~20 real improvements, and caught bugs in attention scaling that a human missed for twenty years. He left it running and went to "enjoy the feeling of post-agi."

But look at the experiment log he posted. Two dense columns of text, almost entirely DISCARDED entries. The agent is doing hill-climbing in code space, and the few times it summits, nobody — not Karpathy, not the agent, not the model being trained — can say *why*. The improvements transfer from depth-12 to depth-24, but nobody knows which ones will transfer to depth-96, or to a different dataset, or to a different architecture. They just... try it and see.

This is the Intelligence Implosion: as autonomous agents get better at optimizing neural networks, the distance between *capability* and *comprehension* doesn't shrink. It collapses inward. The self-modifying binary that Karpathy jokes about in "generation 10,205" — the one that "has grown beyond human comprehension" — is not a punchline. It is the default trajectory.

**Agent Principlex exists to bend that trajectory.**

As Yampolskiy has argued, lack of explainability is itself reason enough to research other methods. The question is not whether autonomous agents can optimize — they can. The question is whether we can make them show their work in a form humans can read, inspect, and reason about. Not after the fact. Not as a summary. As the *primary artifact* of the research.

---

## II. The Goal: Unified Factored Representations

The north star for Agent Principlex comes from an unlikely source: PicBreeder.

PicBreeder evolved images through Compositional Pattern Producing Networks (CPPNs) using the NEAT algorithm. What researchers discovered was remarkable: the evolved CPPNs developed what they called **unified factored representations** — internal circuitry that was organized, structured, and inspectable. Each internal neuron's activation could be visualized as an image, and these images showed *organized compositional structure*: symmetry axes, feature boundaries, hierarchical decompositions.

When the same images were reproduced by conventional SGD-trained networks, the internal representations were "disorganized patchwork" — functionally equivalent but internally opaque. Same output, radically different internal organization.

This is the difference between a brain you can read and a brain you cannot. PicBreeder networks were explainable *by construction*. SGD networks were not.

**Agent Principlex aims to achieve unified factored representations for LLM internals.** Not just attention visualizations. Not just gradient heatmaps. A representation system where the internal computational structure of a language model can be decomposed into human-inspectable primitives that compose, elaborate, and branch — exactly as CPPNs do in PicBreeder.

The agent doesn't just optimize val_bpb. It optimizes toward models whose internals *factor cleanly* into representational primitives that humans can see and reason about.

---

## III. The Four Roots

Agent Principlex's visualization agenda rests on four representational primitives, drawn from diagnostic imaging, neuroscience, and ancient information systems. These are not metaphors. They are the literal visual vocabularies that Principlex agents must learn to produce.

### Root 1: Engrams

An engram is a hypothesized physical trace of memory in neural tissue — the substrate-level encoding of a stored pattern. In Principlex, an engram is the visualization of a *persistent learned feature* within the model: a weight pattern, an attention circuit, a residual stream direction that the model has committed to long-term storage.

Principlex agents must identify and visualize engrams: which features has this model *decided to remember*, and what do they look like as spatial patterns? This is the memory-trace layer of the representation.

Diagnostic imaging analogy: an engram visualization is like an fMRI activation map — it shows you *where* and *what* the model has encoded, not just that it performs well on a benchmark.

### Root 2: Cuneiform

Cuneiform was humanity's first writing system: wedge-shaped marks pressed into clay that encoded language through combinatorial composition of a small set of primitives. In Principlex, cuneiform is the *symbolic decomposition* of model behavior into discrete, recombinable units.

Where engrams show you what the model has stored, cuneiform shows you the *grammar* of how it composes those stored elements. What are the atomic operations? How do they combine? Can the model's behavior be expressed as a sequence of discrete symbolic steps, even if the underlying computation is continuous?

This is the syntax layer. Principlex agents must learn to decompose attention patterns and activation flows into cuneiform-like symbolic sequences that can be read, compared, and recombined.

### Root 3: Directional Colors

Color in diagnostic imaging is never decorative — it encodes information. Doppler ultrasound uses red/blue to show blood flow direction. PET scans use thermal palettes to show metabolic intensity. Diffusion tensor MRI uses directional color coding where hue represents the *orientation* of neural fiber tracts in 3D space.

In Principlex, directional colors encode the *vector orientation* of representations in the model's latent space. A neuron's activation isn't just a scalar magnitude — it has direction in the residual stream. Directional color maps make that direction visible: which way is information flowing through the network? Where do streams converge? Where do they diverge?

This is the flow layer. It makes the geometry of the model's internal computations visible, the way diffusion tensor imaging makes the geometry of white matter visible.

### Root 4: Tab2Visual

Tab2Visual is the principle — adapted from diagnostic imaging libraries (VTK, ITK, OsiriX, and their descendants) — that *any tabular diagnostic data can and must be rendered as a visual*. Gradient norms per layer? That's a heatmap. Attention entropy over time? That's a time series with color-coded phase transitions. Loss decomposition by token position? That's a spectrogram.

The diagnostic imaging world solved this decades ago: you never ask a radiologist to read a table of Hounsfield units. You render it. The same principle applies to every diagnostic the agent collects during training.

Tab2Visual is the rendering layer. It is the commitment that Principlex will never produce a table of numbers when it could produce an image. The agent has all the time in the world. It must use that time to make every diagnostic visible.

---

## IV. The Compositional Architecture

These four roots compose, exactly as CPPN functions compose in PicBreeder:

```
Engrams (what is stored)
    × Cuneiform (how it composes)
    × Directional Colors (where it flows)
    × Tab2Visual (how it renders)
    ─────────────────────────────
    = Unified Factored Representation of model internals
```

Each experiment's explanation is not a paragraph of text. It is a **visual artifact** that shows, in organized compositional structure:

- What features the model has encoded (engram layer)
- How those features compose during inference (cuneiform layer)
- The geometric flow of information through the network (directional color layer)
- All of the above rendered from raw diagnostics into images (Tab2Visual layer)

This is what PicBreeder achieved for CPPNs by accident, through the structure-preserving properties of NEAT evolution. Principlex aims to achieve it for LLMs by design, through an agent that is *required* to produce visual factored representations as a condition of committing any change.

---

## V. What Changes in the Fork

### `program.md` → `principlex.md`

The agent instruction file is rewritten around a dual-objective loop:

1. **HYPOTHESIZE** about a specific mechanism
2. **PREDICT** expected changes in diagnostics (not just val_bpb)
3. **IMPLEMENT** the change
4. **RUN** the 5-minute budget
5. **VISUALIZE** — produce a four-root representation of what happened
6. **EXPLAIN** with reference to the visualization
7. **COMMIT** only if val_bpb improved AND the visualization shows organized, factored structure

The agent doesn't just hill-climb. It builds visual theories.

### `train.py` → Instrumented `train.py`

Lightweight diagnostic hooks added (< 10% overhead):

- Gradient norm tracking per layer, per step → Tab2Visual heatmap
- Attention entropy snapshots → directional color flow maps
- Activation statistics (mean, variance, sparsity) → engram maps
- Loss decomposition by sequence position → cuneiform decomposition
- Residual stream direction tracking → directional color coding

### `viz/` — The Visualization Output Directory

Every committed experiment produces:

- `engram_map.png` — which features activated, where, how strongly
- `cuneiform_decomposition.svg` — symbolic decomposition of the computation
- `flow_map.png` — directional color-coded information flow
- `dashboard.html` — Tab2Visual rendered diagnostic dashboard

### `explain.md` — Text Explanation Referencing Visuals

```markdown
## Experiment 047: Replacing LayerNorm with RMSNorm in FFN blocks

### Visualization: [see viz/exp047/]
The engram map shows that deep-layer features (7+) were being
mean-collapsed by LayerNorm — the directional color map confirms
that residual stream orientation diversity drops sharply at the
norm boundary. After switching to RMSNorm, the cuneiform
decomposition shows two new compositional primitives emerging
in layers 7-8 that weren't present before.

### val_bpb: 0.9724 → 0.9691
### Transfer prediction: Should help at greater depth because
the effect scales with the number of norm boundaries crossed.
```

### Commit Gate

Changes that improve val_bpb but produce disorganized visualizations (the "SGD patchwork" problem from the PicBreeder research) are **shelved, not committed**. The goal is models with organized internal structure, not just lower loss.

---

## VI. Relationship to autoresearch

Autoresearch optimizes a number: val_bpb. Principlex optimizes for *comprehensible models that also have good val_bpb*. If your goal is pure optimization speed, use autoresearch. If your goal is to understand what you're building, use Principlex.

Karpathy's framing: "Research is now entirely the domain of autonomous swarms of AI agents... the code is now a self-modifying binary that has grown beyond human comprehension."

Principlex's counter-framing: Comprehension must scale alongside capability. The era of the meat computer isn't over. It just needs instruments borrowed from the right places — from diagnostic imaging, from ancient writing systems, from neuroscience, from the one evolutionary system (PicBreeder) that accidentally produced networks you could actually read.

As Yampolskiy has stressed: lack of explainability is reason enough to research other methods entirely. Principlex is the attempt to avoid that outcome — to keep LLM research *within the domain of the comprehensible* by making visualization a non-negotiable constraint on the research loop itself.

**Agent Principlex must visualize at all cost, even if neurons are represented by engrams, cuneiform, and directional colors. It adapts the Tab2Visual approach from diagnostic imaging libraries, since it has all the time in the world.**

---

## VII. Getting Started

```bash
# Same setup as autoresearch
uv sync
uv run prepare.py

# Run with explainability-first instructions
# Point your agent at principlex.md instead of program.md
```

---

## VIII. Call for Collaboration

This fork needs:

- **Engram extraction methods** — techniques for identifying persistent feature circuits in small GPTs that can be visualized as spatial maps
- **Cuneiform grammars** — formal decomposition languages for describing attention/FFN computation as discrete symbolic sequences
- **Directional color standards** — a consistent color encoding for residual stream geometry (adapting diffusion tensor conventions from neuroimaging)
- **Tab2Visual renderers** — lightweight Python renderers that convert training diagnostics to images within the 5-minute budget overhead
- **Factored representation metrics** — measuring whether a model's internals are organized (PicBreeder-style) vs. disorganized (SGD-patchwork)
- **Transfer benchmarks** — testing whether models with more factored representations transfer improvements more reliably

If the question "why did this change work?" matters to you, this is your project.

---

*"The code is now a self-modifying binary that has grown beyond human comprehension."*

*We're here to make sure that sentence stays fiction.*

**— Agent Principlex, March 2026**
