# WAG All-Around Prediction: Rotterdam 2026

## Introduction

Women's Artistic Gymnastics, particularly the All-Around (AA) event, represents one of the most complex contexts for performance evaluation, as it requires the integration of multiple technical dimensions across four apparatuses: vault (VT), uneven bars (UB), balance beam (BB), and floor exercise (FX).

The multidimensional nature of performance, combined with execution variability, makes outcome prediction a challenging task from both statistical and sports perspectives.

In recent years, data science approaches have been widely applied to sports analytics. However, there is still a significant gap in applying predictive modeling to highly technical individual sports such as gymnastics.

This project proposes an exploratory statistical model to estimate the probability of victory in the women's All-Around competition at Rotterdam 2026, using data from 2025–2026.

---

## Data

The dataset was manually constructed from official competition results and includes:

### All-Around Competitions

- 2025 World Championships  
- 2025 Pan-American Championships  
- 2025 Asian Championships  
- 2026 City of Jesolo Trophy  

### Apparatus-Based Competitions (World Cups)

- Cottbus World Cup (2025–2026)  
- Baku World Cup (2025–2026)  
- Antalya World Cup 2025  
- Cairo World Cup 2026  
- Osijek World Cup (2025–2026)  
- Doha World Cup 2025  
- Szombathely Challenge Cup 2025  

Each observation includes:

- Athlete name  
- Nationality  
- Competition  
- Year  
- Apparatus scores (VT, UB, BB, FX)  
- All-Around score (when available)  

---

## Methods

### Performance Variable

The performance metric is defined as:

$$
y_{ij} =
\begin{cases}
AA_{ij}, & \text{if available} \\
\sum_{k \in \{VT, UB, BB, FX\}} s_{ijk}, & \text{otherwise}
\end{cases}
$$

where:

- $y_{ij}$ is the performance of athlete $i$ in event $j$  
- $AA_{ij}$ is the All-Around score  
- $s_{ijk}$ is the score on apparatus $k$  

---

### Weighting Scheme

Each observation is assigned a weight:

$$
w_{ij} = w_{\text{event}} \cdot w_{\text{recency}} \cdot w_{\text{type}}
$$

with:

- Event importance:
  - World Championships = 1.00  
  - Continental events = 0.75  
  - International events = 0.60  
  - World Cups = 0.45  

- Recency:
  - 2026 = 1.20  
  - 2025 = 1.00  

- Data type:
  - All-Around = 1.00  
  - Apparatus sum = 0.70  

---

### Feature Engineering

For each athlete $i$:

**Weighted mean:**

$$
\mu_i = \frac{\sum_j w_{ij} y_{ij}}{\sum_j w_{ij}}
$$

**Best score:**

$$
M_i = \max_j y_{ij}
$$

**Recent performance:**

$$
L_i = y_{i,t_{\max}}
$$

**Stability (standard deviation):**

$$
\sigma_i = \sqrt{\frac{1}{n_i} \sum_j (y_{ij} - \mu_i)^2}
$$

**Apparatus form:**

$$
A_i = \frac{1}{K} \sum_{k=1}^{K} s_{ik}
$$

---

### Predictive Score

The final score is defined as:

$$
S_i =
0.65 \cdot \mu_i +
0.20 \cdot M_i +
0.10 \cdot L_i +
\beta_1 A_i +
\beta_2 n^{AA}_i -
\gamma_1 \sigma_i -
\gamma_2 \frac{1}{\sqrt{n_i}}
$$

---

### Probability Estimation

Probabilities are obtained using the softmax function:

$$
P(i) = \frac{\exp\left(\frac{S_i}{T}\right)}{\sum_j \exp\left(\frac{S_j}{T}\right)}
$$

where $T$ is a temperature parameter.

---

### Interpretation

The model combines:

- Historical performance  
- Recent form  
- Performance consistency  

providing a probabilistic ranking of athletes.

---

## Results

The model produces a probabilistic ranking of the top contenders.

The initial projected podium is:

1. Angelina Melnikova (AIN)  
2. Leanne Wong (USA)  
3. Kaylia Nemour (ALG)  

---

## Limitations

- Manually constructed dataset  
- Limited sample size  
- No execution/difficulty breakdown  
- Partial competition coverage  

---

## Conclusion

This work demonstrates how statistical modeling and data science can be applied to a highly complex and underexplored sport.

The proposed approach provides an interpretable and flexible framework for ranking athletes based on performance data.

---

## References

- Baio, G., & Blangiardo, M. (2010). Bayesian hierarchical model for the prediction of football results.  
- Bishop, C. M. (2006). Pattern Recognition and Machine Learning.  
- Bunker, R. P., & Thabtah, F. (2019). A machine learning framework for sport result prediction.  
- Glickman, M. E. (1999). Parameter estimation in paired comparison models.  
- Hvattum, L. M., & Arntzen, H. (2010). Using ELO ratings for match prediction.  
