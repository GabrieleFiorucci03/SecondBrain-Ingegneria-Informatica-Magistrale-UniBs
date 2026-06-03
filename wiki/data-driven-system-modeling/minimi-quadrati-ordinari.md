---
tags: [ddsm, minimi-quadrati, ols, proiezione-ortogonale, equazioni-normali]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/Data_driven_COMPILED.pdf
  - raw/sida_COMPILED.pdf
  - raw/01_regression_COMPILED.pdf
  - raw/projections_COMPILED.pdf
---

# Minimi Quadrati Ordinari (OLS)

Il metodo dei Minimi Quadrati Ordinari (Ordinary Least Squares, OLS) è lo strumento centrale del corso per la stima parametrica di modelli lineari. Minimizza la somma dei quadrati dei residui rispetto al parametro $\theta$.

## Punti chiave

- Il problema OLS è convesso e differenziabile $\Rightarrow$ il punto stazionario è il minimo globale.
- La soluzione soddisfa le **equazioni normali**: $\Phi^\top \Phi \hat{\theta} = \Phi^\top Y$.
- Le equazioni normali ammettono sempre almeno una soluzione; è unica se $\Phi$ ha rango pieno $p$.
- Interpretazione geometrica: $\hat{Y} = \Phi\hat{\theta}$ è la **proiezione ortogonale** di $Y$ sul range di $\Phi$.
- La **bontà del fit** è misurata da $\rho^2 = \text{EV}/\text{TV} \in [0,1]$ (coefficiente di correlazione al quadrato).
- OLS si estende a: minimi quadrati vincolati (Lagrangiani), pesati (WLS) e con regolarizzazione.

## Il problema OLS

Dato il training set $\{(u_i, y_i)\}_{i=1}^N$ e la classe lineare $\mathcal{M} = \{\varphi(u)^\top\theta : \theta \in \mathbb{R}^p\}$, si cerca:

$$\hat{\theta} = \arg\min_{\theta \in \mathbb{R}^p} \sum_{i=1}^N \left(y_i - \varphi_i^\top \theta\right)^2 = \arg\min_{\theta \in \mathbb{R}^p} \|Y - \Phi\theta\|^2$$

dove $Y = [y_1, \ldots, y_N]^\top \in \mathbb{R}^N$ e $\Phi = [\varphi_1^\top; \ldots; \varphi_N^\top] \in \mathbb{R}^{N \times p}$ è la **matrice dei regressori** (design matrix).

$J(\theta) = \|Y - \Phi\theta\|^2$ è convessa e differenziabile $\Rightarrow$ qualsiasi punto stazionario è un minimo globale.

## Equazioni normali

Derivando $J(\theta)$ e ponendo uguale a zero si ottengono le **equazioni normali**:

$$\underbrace{\Phi^\top\Phi}_{R \in \mathbb{R}^{p\times p}} \theta = \Phi^\top Y$$

La matrice $R = \Phi^\top\Phi = \sum_{i=1}^N \varphi_i\varphi_i^\top$ è simmetrica, semidefinita positiva e si chiama **matrice dei regressori**.

**Esistenza**: le equazioni normali hanno sempre almeno una soluzione (perché $\Phi^\top Y \in \text{range}(\Phi^\top\Phi)$).

**Unicità**: la soluzione è unica se e solo se $\text{rank}(\Phi) = p$ (le colonne di $\Phi$ sono linearmente indipendenti). In tal caso:

$$\hat{\theta} = (\Phi^\top\Phi)^{-1}\Phi^\top Y$$

> **Esempio**: con $\mathcal{M} = \{f(u,\theta) = \theta\}$ (funzioni costanti), la soluzione è $\hat{\theta} = \frac{1}{N}\sum y_i$ (media campionaria). Il minimo raggiunto è la varianza campionaria.

## Interpretazione geometrica: proiezione ortogonale

Il problema OLS minimizza la distanza euclidea tra $Y \in \mathbb{R}^N$ e il sottospazio $V = \text{range}(\Phi) \subseteq \mathbb{R}^N$.

La soluzione $\hat{Y} = \Phi\hat{\theta}$ è l'unica **proiezione ortogonale** di $Y$ su $V$, caratterizzata da:
$$Y - \hat{Y} \perp V \quad \Leftrightarrow \quad \Phi^\top(Y - \Phi\hat{\theta}) = 0$$

La **matrice di proiezione ortogonale** è:
$$\Pi_\Phi = \Phi(\Phi^\top\Phi)^{-1}\Phi^\top \in \mathbb{R}^{N\times N}$$

con proprietà: $\Pi_\Phi = \Pi_\Phi^\top$ (simmetria), $\Pi_\Phi^2 = \Pi_\Phi$ (idempotenza).

**Conseguenza**: espandere lo spazio dei regressori (aggiungere features) può solo migliorare o mantenere il fit: $\|Y - \hat{Y}_2\| \leq \|Y - \hat{Y}_1\|$ se $\mathcal{M}_1 \subset \mathcal{M}_2$.

## Bontà del fit ($\rho^2$)

Per confrontare classi di modelli si usano:
- **Total Variance** (TV): $\text{TV} = \frac{1}{N}\sum(y_i - \bar{y})^2$ (varianza campionaria di $y$)
- **Residual Variance** (RV): $\text{RV} = \frac{1}{N}\sum(y_i - \hat{y}_i)^2$
- **Explained Variance** (EV): $\text{EV} = \text{TV} - \text{RV}$

$$\rho^2 = \frac{\text{EV}}{\text{TV}} \in [0,1]$$

$\rho^2 \times 100\%$ è la percentuale di varianza spiegata dal modello. Un modello con intercetta (regressor $\varphi = 1$) ha sempre $\rho^2 \geq 0$.

> **Attenzione**: $\rho^2 \to 1$ quando $p \approx N$ può indicare overfitting: si modella anche il rumore.

## Minimi Quadrati Vincolati

Il problema con vincoli lineari uguaglianza $K\theta = 0$ (con $K \in \mathbb{R}^{m\times p}$, $\text{rank}(K) = m$):

$$\hat{\theta} = \arg\min_{\theta: K\theta=0} \|Y - \Phi\theta\|^2$$

Si risolve con i **moltiplicatori di Lagrange** (Lagrangiano $\mathcal{L}(\theta,\lambda) = \|Y-\Phi\theta\|^2 + \lambda^\top K\theta$), ottenendo la formula chiusa:

$$\hat{\theta} = (\Phi^\top\Phi)^{-1}\Phi^\top Y - (\Phi^\top\Phi)^{-1}K^\top\left[K(\Phi^\top\Phi)^{-1}K^\top\right]^{-1}K(\Phi^\top\Phi)^{-1}\Phi^\top Y$$

**Applicazione**: regressione a tratti (piecewise) con vincoli di continuità e differenziabilità.

## Minimi Quadrati Pesati (WLS)

Si attribuisce importanza diversa ai residui tramite pesi $w_i \geq 0$:

$$\hat{\theta} = \arg\min_{\theta} \sum_{i=1}^N w_i(y_i - \varphi_i^\top\theta)^2 = \arg\min_\theta \|Y - \Phi\theta\|_W^2$$

Soluzione: $\hat{\theta} = (\Phi^\top W\Phi)^{-1}\Phi^\top W Y$, dove $W = \text{diag}(w_1, \ldots, w_N)$.

**Usi**: escludere misure lontane nel tempo, tenere conto di precisioni diverse degli strumenti.

## Articoli correlati

- [[introduzione-e-setup]]
- [[proprieta-statistiche-stimatori-ls]]
- [[identificazione-sistemi]]

## Fonti

- Bontempi & Venturini, *Data Driven System Modelling*, March 2020 (`raw/Data_driven_COMPILED.pdf`, Cap. 3)
- Ramponi, *Lecture notes on system identification and data analysis* (`raw/sida_COMPILED.pdf`, Cap. 1)
- Ramponi, *Regression: review*, slides DDSM (`raw/01_regression_COMPILED.pdf`)
- Ramponi, *Least Squares as projections* (`raw/projections_COMPILED.pdf`)
