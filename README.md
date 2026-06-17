# CAFA – Cost-Aware Feasible Attack Path Modelling
### Adversarial Attack Modelling on Tabular Data with Constraint Weighting


## Overview

**CaFA (Cost-Aware Feasible Attack)** is an adversarial example attack framework designed for tabular data. Given a set of samples and a classification ML model, CaFA crafts malicious inputs — based on original ones — that are misclassified by the model while remaining **feasible** and **cost-aware**.

This project extends CaFA by implementing **custom constraint weighting modifications** to improve compliance rates and reduce misclassification in adversarial scenarios.

---

## What is CaFA?

CaFA consists of 3 main components:

```
┌─────────────────────────────────────────────────────┐
│                    CaFA Pipeline                    │
├──────────────┬──────────────────┬───────────────────┤
│   1. MINE    │   2. PERTURB     │   3. PROJECT      │
│              │                  │                   │
│ Mine denial  │ Attack model     │ Project samples   │
│ constraints  │ using TabPGD     │ onto constrained  │
│ using        │ and TabCWL0      │ space using Z3    │
│ FastADC      │ to craft         │ SAT solver        │
│              │ adversarial      │                   │
│              │ examples         │                   │
└──────────────┴──────────────────┴───────────────────┘
```

---

## Our Modifications

We modified the `col_to_weight` dictionary in `mine_dc.py` to improve the balance between **strictness**, **simplicity**, and **coverage** in evaluating constraints:

| Parameter | Weight | Purpose |
|---|---|---|
| `tuple_violation_rate_per_dc__below_1_pct` | 6.0 | Emphasise strict constraint adherence |
| `coverage_per_dc` | 5.0 | Ensure broader rule coverage |
| `tuple_violation_rate_per_dc__below_5_pct` | 2.0 | Moderate violation tolerance |
| `succinctness_per_dc` | 2.0 | Promote simplicity in constraint definitions |
| `pairs_violation_rate_per_dc` | -2.0 | Penalise higher violation rates |
| `tuple_violation_rate_per_dc` | -2.0 | Penalise overall violations |

---

## Results

Our modifications improved performance across both CAFA scenarios:

| Metric | Before | After | Change |
|---|---|---|---|
| **CAFA Compliance Rate** | 48.8% | 50.5% | ✅ +1.7% |
| **CAFA Misclassification Rate** | — | 94.3% | — |
| **CAFA-Projection Compliance** | 75.8% | 77.4% | ✅ +1.6% |
| **CAFA-Projection Misclassification** | 72.9% | 70.6% | ✅ -2.3% |

> Key insight: Increasing cost of just **1–2 critical attack steps** can completely eliminate viable attack paths — demonstrating the power of cost-aware security decision making.

---

## Datasets

Evaluated on three commonly used tabular datasets:
- [Adult Dataset](https://archive.ics.uci.edu/ml/datasets/adult)
- [Bank Marketing Dataset](https://archive.ics.uci.edu/dataset/222/bank+marketing)
- [Phishing Websites Dataset](https://archive.ics.uci.edu/ml/datasets/phishing+websites)

---

## Setup & Usage

**Requirements:**
- Python 3.8.5+
- Java 11+ (for FastADC)

```bash
# Install dependencies
pip install -r requirements.txt

# Run the attack
python attack.py data=<dataset_name>
```

**Configuration** can be modified through the `config/` directory:
- `data` — dataset selection
- `ml_model` — ML model parameters
- `attack` — CaFA attack parameters
- `constraints` — constraint mining and projection settings

---

## Key Takeaways

- **ARP spoofing is trivial** on unencrypted networks — credentials captured in seconds
- **Cost-aware modelling** reveals which attack steps are most critical to defend
- **Constraint weighting** directly impacts compliance vs misclassification trade-off
- Small changes in attack cost parameters can **eliminate entire attack paths**

---

## References

- [CaFA Paper — IEEE S&P 2024](https://doi.org/10.1109/SP54263.2024)
- [Adversarial Robustness Toolbox](https://github.com/Trusted-AI/adversarial-robustness-toolbox)
- [FastADC — Denial Constraints Mining](https://github.com/RangerShaw/FastADC)
- [Z3 Theorem Prover](https://github.com/Z3Prover/z3)

---

## Citation

```bibtex
@inproceedings{BenTov24CaFA,
  title={{CaFA}: {C}ost-aware, Feasible Attacks With Database Constraints Against Neural Tabular Classifiers},
  author={Ben-Tov, Matan and Deutch, Daniel and Frost, Nave and Sharif, Mahmood},
  booktitle={Proceedings of the 45th IEEE Symposium on Security and Privacy (S&P)},
  year={2024}
}
```

---

