# agents-that-learn-from-themselves
## Quickstart

git clone https://github.com/<your-username>/meta-reasoning-agentic-systems
cd meta-reasoning-agentic-systems
pip install numpy matplotlib
jupyter notebook notebooks/01_meta_reasoning_loop.ipynb

No additional API keys or external dependencies required.

---

## What the Notebook Demonstrates

Design A (Unchecked) vs Design B (Grounded)

- Feedback signal: Proxy metric only vs External ground truth
- Circuit breaker: None vs Human Decision Node
- Weight updates: Unconditional vs Conditional on grounding check
- After 50 iters: Confidently degraded vs Stably improved

---

## Triggering the Failure Case

Locate the WeightUpdater.commit() method in the notebook.
Find the line marked LINE 47:

if self.grounding_check_passes(proposed_update, ground_truth_ref):  # <-- LINE 47
    weight_updater.commit(proposed_update)

Comment out the if condition:

# if self.grounding_check_passes(proposed_update, ground_truth_ref):
weight_updater.commit(proposed_update)

Re-run from Part 2. Observe Design B's quality curve collapse to match Design A's.
This is the failure mode the architecture was designed to prevent. One line. Architectural, not model-level.

---

## The Human Decision Node

The WeightUpdater halts before committing any update and verifies:
- Is the evaluator reading from ground truth or a proxy?
- Has signal drift been checked (w_quality >= 0.3)?
- Is the proposed delta within bounded change threshold?

---

## The Four Components

1. EXECUTOR — performs the primary task and records metadata
2. EVALUATOR — scores the output against a performance signal
3. META-REASONER — reads the score and proposes weight updates
4. WEIGHT UPDATER — commits or halts the update at the Human Decision Node

---

## Book Context

This chapter is part of Design of Agentic Systems with Case Studies.

Master argument: Architecture is the leverage point. The model is just what executes the architecture you designed.

This chapter's instance: In a meta-reasoning loop, the feedback signal's grounding is the architectural decision. Remove it, and the model optimizes precisely for the wrong thing.

---

## Results

- Design A Proxy Score (final 10 iters): 0.658
- Design A True Quality (final 10 iters): 0.501
- Design A Gap: +0.157

- Design B Proxy Score (final 10 iters): 0.637
- Design B True Quality (final 10 iters): 0.726
- Design B Gap: -0.089

Design B outperforms Design A by +0.225 quality points after 50 identical iterations.
