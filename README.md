# TASEPy – Modeling mRNA Translation through TASEP

This repository contains my Bachelor's Thesis project, in which I studied the **translational motion of mRNA during protein synthesis** using a new Python module called **TASEPy**, developed by *Luca Ciandrini* (Associate Professor, University of Montpellier, Institut Universitaire de France).

---

## 📘 Abstract

The project investigates the **kinetics of protein synthesis** through the *Asymmetric Simple Exclusion Process* (ASEP) and its totally asymmetric version, *TASEP*.  
The model describes ribosome movement along an mRNA strand, representing the ribosome as a particle traveling on a one-dimensional lattice.  

Through this approach, it is possible to model the **sequential nature** of translation and the **steric exclusion** that prevents multiple ribosomes from overlapping on the same codon.  

---

## 🧬 Biological Background

Translation is a sequential process in which a ribosome moves along the mRNA strand.  
At each step, a specific tRNA binds to a codon — a triplet of nitrogenous bases coding for a specific amino acid. After releasing the amino acid, which is incorporated into the growing polypeptide chain during elongation, the tRNA detaches.  

The TASEP model captures this process by describing stochastic motion of particles in one direction along a one-dimensional lattice.

---

## ⚙️ Model Description

The ribosome is modeled as a particle that moves forward with a given **hopping rate**, constrained by the exclusion principle — only one particle can occupy a site at a time.

Two types of boundary conditions are considered:

- **Periodic boundary conditions:** the mRNA is modeled as a ring, simplifying calculations but reducing biological realism.  
- **Open boundary conditions:** particles enter and exit the lattice at defined rates, better representing the cytoplasmic environment.

The system dynamics are governed by a stochastic process where, at each iteration, a site *k* is chosen:
- if site *k* is occupied and site *k+1* is empty, the particle moves forward with a constant hopping rate \( p \);
- otherwise, it remains stationary.

In open boundaries, two additional rates are defined:
- \( \alpha \): **initiation rate** (entry of a new ribosome)
- \( \beta \): **termination rate** (ribosome exit from the lattice)

---

## 🔬 Two-State Model

To better describe translation kinetics, a **two-state model** is introduced to capture the internal dynamics of tRNA–codon pairing.  
This process is characterized by two main states:
1. **Coupling state** (tRNA-codon binding)
2. **Translocation state** (ribosome movement)

Critical points of phase transitions differ from those in the classical model. The *maximal current phase (MC)* becomes predominant, depending on the ratio between:
- \( k_1 \): rate of transition from the coupling to the translocation state  
- \( k_2 \): ribosome translocation rate  

For \( k_1 \to \infty \), the model recovers the original TASEP results.

Experimental data indicate that:
- \( k_2 \approx 35 \, s^{-1} \)
- \( k_1/k_2 \in [0.05, 3.38] \)
- For ~87% of codons, \( k_1/k_2 < 1 \)

Hence, translation typically operates in a **maximal current regime**, optimizing protein synthesis.

---

## ⚛️ Inhomogeneous TASEP and Power Series Approximation (PSA)

In the **inhomogeneous TASEP**, the hopping rate varies between sites to reflect differences in tRNA availability for each codon.

This can be expressed as:

$$
P(C) = \sum_{n=0}^{\infty} c_n \alpha^n
$$

where \( P(C) \) is the probability of the system being in configuration \( C \) of the lattice.

Since the approximation is dominated by the first terms, analyzing the first \( K \) coefficients yields a good estimate of density and current, provided \( K \) is sufficiently large. However, computing many coefficients is computationally demanding.

---

## 🐍 TASEPy: A Python Implementation

**TASEPy** provides an efficient numerical implementation of the PSA method for the inhomogeneous TASEP model.  
It includes:
- Four custom functions for PSA computation  
- Definition of the *hopping rate matrix*  
- Ribosome length ( \ell ) , modeled as an extended object represented by its **tracking site**  

TASEPy can also perform **inverse computations** in mRNA translation analysis.

### Example 1 – Estimating the Initiation Rate

From an experimental density ( \rho_{\text{exp}} ), one can estimate the initiation rate \( \alpha \) using optimization tools that minimize:

```math
f(\alpha) = \sum_i \exp\left( \rho_i^{\text{exp}} - \rho_i(\alpha) \right)
