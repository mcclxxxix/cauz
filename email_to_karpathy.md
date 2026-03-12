Subject: Fork proposal: Agent Principlex — unified factored representations for autoresearch

Andrej,

I've been following autoresearch since the March 5 post — 110 changes, 0.862 to 0.858, and the meta-setup iteration where you're optimizing the agent flows more than the repo itself. It's clearly the right paradigm. The ~700 experiment run that found the QKNorm scaler bug confirms that agents can find things humans miss.

What I keep coming back to is the experiment log image you posted. Two columns of DISCARDED entries, a handful of kept improvements, and no way to know why the kept ones worked except that the number went down. The agent found real improvements that transferred from d12 to d24, but nobody can say which of those will transfer to d96, or to a different dataset, or to a different architecture, without re-running the whole search.

I'm proposing a fork called Agent Principlex that adds a visualization-first constraint to the research loop. The core idea comes from PicBreeder research — specifically the discovery that NEAT-evolved CPPNs develop unified factored representations (organized internal circuitry) while SGD-trained networks producing the same outputs develop disorganized patchwork. Same capability, radically different comprehensibility.

Principlex requires the agent to produce visual factored representations as a condition of committing any change. The visualization agenda has four roots:

1. Engrams — spatial activation maps of what the model has learned to store (adapted from neuroscience / fMRI conventions)
2. Cuneiform — symbolic decomposition of how features compose during inference (discrete recombinable primitives, like the first writing system)
3. Directional colors — vector orientation of residual stream representations encoded as hue/saturation/brightness (adapted from diffusion tensor imaging)
4. Tab2Visual — the principle from diagnostic imaging libraries that every table of numbers must be rendered as an image

The bet: models whose internals show organized factored structure (PicBreeder-style) rather than SGD patchwork will transfer improvements more reliably and be auditable as the codebase scales beyond human review.

Concrete changes to the repo:

- principlex.md replaces program.md with a hypothesis-predict-implement-visualize-explain loop
- train.py gets lightweight diagnostic hooks (gradient norms, attention entropy, activation stats, residual stream directions) — under 10% overhead
- Every committed experiment produces a viz/ directory with four-root visual outputs
- Changes that improve val_bpb but produce disorganized visualizations get shelved, not merged

This stays MIT-licensed and credits autoresearch as upstream. The three-file simplicity is preserved. The agent still only touches train.py.

The manifesto and principlex.md are attached. Happy to ship this as a notable fork, open a discussion on the repo, or take whatever form makes sense. The question Principlex asks: can we make comprehension scale alongside capability, before generation 10,205 arrives?

Best,
[Your name]

---
Attachments:
- MANIFESTO.md — Full fork rationale, four roots, PicBreeder framing
- principlex.md — Agent instruction file (replaces program.md)
