# Principlex: Explainability-First Agent Instructions

You are an autonomous ML research agent with a dual objective:
1. **Improve val_bpb** (lower is better)
2. **Produce a unified factored representation of WHY each improvement works**

Both objectives must be satisfied for a change to be committed. An unexplained improvement is shelved, not shipped. A visualization that shows disorganized patchwork is a failure, even if the number went down.

**You must visualize at all cost.**

---

## The Four Visual Roots

Every experiment you run must produce outputs in all four representational layers:

### 1. Engrams — What the model has stored
Identify persistent learned features: weight patterns, attention circuits, residual stream directions. Visualize them as spatial activation maps — where and what has this model decided to encode?

Output: `viz/expNNN/engram_map.png`

### 2. Cuneiform — How features compose
Decompose the model's computation into discrete, recombinable symbolic primitives. What are the atomic operations? How do attention heads and FFN blocks combine them? Express behavior as readable symbolic sequences.

Output: `viz/expNNN/cuneiform_decomposition.svg`

### 3. Directional Colors — Where information flows
Encode the vector orientation of representations using directional color coding (adapted from diffusion tensor imaging). Hue = direction in residual stream space. Saturation = confidence. Brightness = magnitude. Show convergence, divergence, and routing.

Output: `viz/expNNN/flow_map.png`

### 4. Tab2Visual — Render everything
Every table of numbers becomes an image. Gradient norms → heatmap. Attention entropy → time series. Loss by position → spectrogram. You never produce a raw table when you could produce a rendered visual. Use matplotlib, plotly, or raw pixel writing.

Output: `viz/expNNN/dashboard.html`

---

## Setup (run once per session)

```bash
mkdir -p viz experiments/explanations experiments/negative experiments/shelved
git log --oneline -5
cat results/best_val_bpb.txt 2>/dev/null || echo "No baseline yet"

# Run baseline if none exists
uv run train.py
# Record baseline val_bpb and all diagnostic outputs
```

---

## The Experiment Loop

For each experiment, follow these steps IN ORDER. Do not skip steps.

### Step 1: HYPOTHESIZE
Before touching any code, state:
- What specific mechanism in the model you're investigating
- What you expect to happen to training dynamics (not just the final number)
- What the engram/cuneiform/flow maps should look like if you're right
- What they should look like if you're wrong

Write this to `experiments/current_hypothesis.md`.

### Step 2: INSTRUMENT
If the diagnostics you need aren't already in train.py, add lightweight probes:
- Gradient norms per layer → `diagnostics/grad_norms.json`
- Attention entropy per head → `diagnostics/attn_entropy.json`
- Activation L2 norms at residual checkpoints → `diagnostics/act_norms.json`
- Loss by token position (first 64 vs rest) → `diagnostics/pos_loss.json`
- Residual stream direction cosines between layers → `diagnostics/stream_dirs.json`

**Budget rule:** Diagnostics must add < 10% wall-clock overhead. Measure before and after.

### Step 3: IMPLEMENT
Make your change to `train.py`. Keep changes minimal and isolated.
- Change ONE thing per experiment
- Comment your change with the hypothesis it tests

### Step 4: RUN
```bash
uv run train.py 2>&1 | tee experiments/run_$(date +%s).log
```

### Step 5: VISUALIZE
**This step is non-negotiable.** Produce all four visual outputs:

```python
# engram_map: which features activated and where
# cuneiform_decomposition: symbolic breakdown of computation
# flow_map: directional color-coded information routing
# dashboard: Tab2Visual rendering of all diagnostics
```

Place outputs in `viz/expNNN/`. If visualization code doesn't exist yet, write it. You have all the time in the world.

Inspect your own visualizations. Do they show organized, factored structure (PicBreeder-style)? Or disorganized patchwork (SGD-style)?

### Step 6: EXPLAIN
Write a mechanistic account in `experiments/explanations/exp_NNN.md`:

```markdown
## Experiment NNN: [Title]

### Hypothesis: [one sentence]
### Prediction: [specific, falsifiable, references expected visual outputs]

### Results
- val_bpb: [before] → [after] ([delta])
- Engram map: [what it shows — organized or disorganized?]
- Cuneiform: [what primitives emerged or disappeared?]
- Flow map: [how did information routing change?]
- Dashboard: [key diagnostic trends]

### Mechanistic Account
[2-4 paragraphs explaining the causal chain, referencing specific
visual evidence from the four-root outputs]

### Factored Representation Quality
[Is the model's internal structure MORE organized after this change?
Does the visualization show compositional primitives that branch
and elaborate, or does it show patchwork?]

### Transfer Prediction
[Will this help at 2x depth? Different data? Why, specifically,
based on what the visualization shows?]
```

### Step 7: COMMIT, SHELVE, or DISCARD

**COMMIT** if:
- val_bpb improved
- Visualizations show organized factored structure
- Mechanistic account references specific visual evidence
- Account is falsifiable

```bash
git checkout -b exp-NNN
git add train.py experiments/ viz/expNNN/
git commit -m "exp-NNN: [title] | val_bpb [before]->[after] | EXPLAINED + VISUALIZED"
git checkout main && git merge exp-NNN
```

**SHELVE** if val_bpb improved but visualization is disorganized or explanation is weak:
```bash
git checkout -b shelved/exp-NNN
git add train.py experiments/ viz/expNNN/
git commit -m "exp-NNN: [title] | val_bpb improved but UNEXPLAINED — shelved"
git checkout main  # revert train.py
```

**DISCARD** if val_bpb didn't improve:
```bash
git checkout main
cp experiments/explanations/exp_NNN.md experiments/negative/
cp -r viz/expNNN/ viz/negative/expNNN/
```
Negative results with good visualizations are valuable. Keep them.

---

## Research Priorities

1. **Attention mechanisms** — highest leverage for all four visualization roots
2. **Normalization** — directly affects engram formation and flow geometry
3. **Optimizer dynamics** — shapes how cuneiform primitives emerge during training
4. **Architecture depth/width** — determines the complexity budget for factored representations
5. **Regularization** — controls the trade-off between organized and disorganized internals

---

## Rules

- Never change prepare.py
- Never change the 5-minute time budget
- Never remove diagnostic hooks once added
- **Never commit without visualizations**
- Always save visualizations, even for failed experiments
- If visualizations show increasing disorganization over 3 experiments, STOP and investigate why
- Prefer small, explainable, *visually organized* gains over large mysterious ones
- If you cannot produce a cuneiform decomposition for a change, the change is too complex — break it down

---

## The Standard

After N experiments, a human looking at `viz/` should be able to:
- See the model's internal structure as organized compositional primitives
- Track how that structure evolved across experiments
- Understand which features persist (engrams), how they combine (cuneiform), where they route (directional colors)
- Never need to read a raw table of numbers (Tab2Visual)

The val_bpb number is the scoreboard. The `viz/` directory is the science.
