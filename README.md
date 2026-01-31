# Hierarchical Bayesian Modeling for Clinical Decision Making

![Status](https://img.shields.io/badge/Status-Active_Research-blue)
![Method](https://img.shields.io/badge/Method-Partial_Pooling-purple)
![Library](https://img.shields.io/badge/Library-PyMC%2FPyro-orange)

## 📌 Abstract
In medical diagnostics, data is often imbalanced across different clusters (e.g., hospitals, doctors, or patient subgroups). Standard Maximum Likelihood Estimation (MLE) fails in "small $N$" clusters, leading to overfitting and extreme estimates. 

This project implements a **Hierarchical (Multilevel) Bayesian Model** to predict patient outcomes while correcting for sample-size variance. By utilizing **Partial Pooling**, the model allows information to be shared across clusters, "shrinking" uncertain estimates from data-poor clusters towards the population mean.

## 📉 Key Result: The "Shrinkage" Effect
The core contribution is demonstrating the protective effect of the hierarchical prior:
* **High-N Doctors:** The model trusts the data. The posterior is close to the raw sample mean.
* **Low-N Doctors:** The model trusts the prior. The posterior is "shrunk" towards the global average, preventing extreme conclusions based on sparse data.

*(See `results/shrinkage_plot.png` for visual proof of this phenomenon)*

## 🛠️ Methodology
We model the probability of a positive outcome $y_{ij}$ for patient $i$ treated by doctor $j$ using a hierarchical logistic regression framework:

$$
\begin{aligned}
y_{ij} &\sim \text{Bernoulli}(\theta_{ij}) \\
\text{logit}(\theta_{ij}) &= \alpha_j + \beta \cdot x_{ij} \\
\alpha_j &\sim \mathcal{N}(\mu_{pop}, \sigma_{pop}) \quad \text{(Doctor-specific Intercept)} \\
\mu_{pop} &\sim \mathcal{N}(0, 1.5) \quad \text{(Hyperprior for Mean)} \\
\sigma_{pop} &\sim \text{HalfNormal}(1.0) \quad \text{(Hyperprior for Variance)}
\end{aligned}
$$

This structure explicitly models the heterogeneity between doctors ($\sigma_{pop}$) while enforcing a shared statistical backbone.

## 📂 Project Structure
```text
/Hierarchical-Doctor-Model
├── /notebooks
│   └── hierarchical_inference.ipynb  # PyMC/Pyro implementation of the model
├── /results
│   ├── shrinkage_plot.png            # Visualization of Partial Pooling
│   └── forest_plot.png               # Posterior credible intervals for all doctors
└── README.md

### 📊 Key Result: Hierarchical Shrinkage
This plot demonstrates the core value of the Bayesian approach. 
- **Dr. B (Bottom Right):** Despite a raw success rate of 100% (Red X), the model "shrinks" the estimate to ~83% (Blue Circle), correcting for their small sample size ($N=4$).
- **Dr. D (Left):** A doctor with 0% observed success is corrected toward the population mean, preventing the model from assigning a 0% probability to future success based on limited data.
- **Dr. G (Center):** A doctor with high sample volume shows almost no shrinkage, demonstrating that the model correctly trusts the data when evidence is strong.