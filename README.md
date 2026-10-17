# Variational Quantum Classifier on `make_moons`

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/supernova-sys/Quantum-not-so-computing/blob/main/vqc_moons_notebook.ipynb)

Problem 3: classify the two-class `make_moons` dataset with a **variational quantum classifier (VQC)** built in Qiskit, and compare it honestly against classical models with a similar number of trainable parameters.

Everything lives in one notebook: [`vqc_moons_notebook.ipynb`](vqc_moons_notebook.ipynb).

## What's in the notebook

1. **Data**: `make_moons` with 100 points, 2 features, noise 0.2 (seed 42), a stratified 70/30 train/test split, and features scaled to `[0, π]` so they can be used as rotation angles.
2. **Classical baselines** (parameter counts in brackets):
   - Logistic regression (3)
   - Logistic regression with x² terms (6)
   - Small neural net, 2 tanh hidden units (9)
   - RBF SVM, reference only (29 support vectors)
3. **Qiskit's ready-made VQC**: `ZZFeatureMap` (2 reps) + `RealAmplitudes` (3 reps, 8 parameters), trained with COBYLA (100 iterations).
4. **A VQC built from scratch**:
   - *Feature map*: `RY(x₀)` on qubit 0 and `RY(x₁)` on qubit 1.
   - *Ansatz*: 4 layers of trainable `RY` rotations, with CNOTs between layers (8 parameters).
   - *Readout*: probability of measuring `1` on qubit 0, with the label predicted as 1 if it is above 0.5.
   - *Loss*: binary cross-entropy, minimised with SciPy COBYLA (200 iterations) using exact `Statevector` simulation.
5. **Robustness check**: all models re-run over 10 different train/test splits (mean and standard deviation).
6. **Decision boundaries**: the from-scratch VQC compared with the SVM.
7. **Package versions** for reproducibility.

## Results

Single split (seed 42):

| Model | Params | Train acc | Test acc |
|---|---:|---:|---:|
| Logistic regression (linear) | 3 | 0.87 | 0.83 |
| Logistic regression + x² terms | 6 | 0.87 | 0.83 |
| Small neural net (2 hidden) | 9 | 0.89 | 0.73 |
| SVM (RBF), reference only | 29 | 0.91 | 0.93 |
| VQC ready-made (ZZ + RealAmplitudes) | 8 | 0.56 | 0.60 |
| **VQC from scratch (RY)** | 8 | 0.89 | 0.83 |

Mean test accuracy over 10 splits:

| Model | Mean | Std |
|---|---:|---:|
| Logistic regression (linear) | 0.850 | 0.056 |
| Logistic regression + x² terms | 0.840 | 0.061 |
| Small neural net (2 hidden) | 0.850 | 0.052 |
| SVM (RBF), reference only | 0.913 | 0.034 |
| **VQC from scratch (RY)** | 0.840 | 0.061 |
| VQC ready-made (ZZ) | 0.567 | 0.083 |

### Takeaways

- The from-scratch VQC, with 8 parameters, matches the small classical models (about 0.84 mean test accuracy) but does not beat them. The RBF SVM remains the strongest.
- The ready-made `ZZFeatureMap` VQC performs close to chance here. With only 100 COBYLA iterations and a 2-qubit circuit, it is a poor fit for this dataset. A simpler `RY` encoding worked much better.
- With 30 test points, split-to-split spread is large (std about 0.05 to 0.06), so differences of a few percent between the small models are within noise.

## Running it

**Colab (easiest):** click the badge above, then *Runtime → Run all*. The first cell installs the dependencies. If Colab asks you to restart the session, restart and run all again.

**Locally:**

```bash
pip install qiskit qiskit-machine-learning scikit-learn matplotlib pylatexenc scipy
jupyter notebook vqc_moons_notebook.ipynb
```

Section 6 (from-scratch VQC over 10 splits) is slow, roughly 5 to 10 minutes, since each loss evaluation simulates every training circuit.

## Tested versions

| Package | Version |
|---|---|
| qiskit | 2.6.0 |
| qiskit-machine-learning | 0.9.1 |
| scikit-learn | 1.6.1 |
| numpy | 2.1.3 |
| scipy | 1.16.3 |
| matplotlib | 3.10.0 |

## Notes

- All simulation is exact (`Statevector`), so there is no shot noise and no hardware noise.
- Seeds are fixed (`SEED = 42`, and the split index for the 10-split runs), so results should be reproducible on the versions above.
