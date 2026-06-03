---
tags: [ddsm, minimi-quadrati, stima-parametrica, blue, fpe, regolarizzazione, varianza-residua]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/Data_driven_COMPILED.pdf
  - raw/Residual variance, FPE_COMPILED.pdf
---

# Proprietà Statistiche degli Stimatori LS

Questo articolo tratta le proprietà statistiche dello stimatore ai minimi quadrati: non polarizzazione, consistenza, ottimalità (BLUE), stima della varianza del rumore, selezione del modello (FPE) e regolarizzazione.

## Punti chiave

- Il modello statistico è $y_i = \varphi_i^\top\theta^* + \varepsilon_i$; i residui $\varepsilon_i$ sono la sola sorgente di aleatorietà.
- OLS è **non polarizzato** (unbiased): $\mathbb{E}[\hat{\theta}] = \theta^*$ se $\mathbb{E}[\varepsilon_i] = 0$.
- OLS è **consistente**: $\hat{\theta}_N \xrightarrow{P} \theta^*$ se i regressori sono persistentemente eccitanti.
- OLS è il **Best Linear Unbiased Estimator (BLUE)** (teorema di Gauss-Markov).
- La varianza del rumore si stima con $\hat{\sigma}^2_\varepsilon = \frac{1}{N-p}\sum(y_i - \varphi_i^\top\hat{\theta})^2$ (stimatore non polarizzato).
- Il **Final Prediction Error (FPE)** è lo strumento principale per la selezione del modello.
- La **regolarizzazione** bilancia fedeltà ai dati e complessità del modello.

## Setup statistico

Si assume che $f^*$ sia lineare in $\theta^*$:
$$y_i = \varphi_i^\top\theta^* + \varepsilon_i$$

I regressori $\varphi_i$ e il parametro $\theta^*$ sono deterministici; i disturbi $\varepsilon_i$ sono aleatori (non osservabili), quindi anche $y_i$ e $\hat{\theta}$ sono aleatori.

**Approccio Fisheriano (frequentista)**: $\theta^*$ è deterministico ma sconosciuto.

## Non polarizzazione (Unbiasedness)

Con $\mathbb{E}[\varepsilon_i] = 0$ per ogni $i$ e regressori deterministici:

$$\mathbb{E}[\hat{\theta}] = \mathbb{E}[(\Phi^\top\Phi)^{-1}\Phi^\top Y] = \theta^* + (\Phi^\top\Phi)^{-1}\Phi^\top \underbrace{\mathbb{E}[\mathcal{E}]}_{=0} = \theta^*$$

Se i regressori sono aleatori, serve l'indipendenza tra $\Phi$ e $\mathcal{E}$.

> **Attenzione**: se l'ingresso è correlato con il rumore (es. $u_i + \varepsilon_i$ come regressor), OLS è polarizzato.

## Consistenza

Lo stimatore $\hat{\theta}_N$ è consistente se $\hat{\theta}_N \xrightarrow{P} \theta^*$ per $N \to \infty$. La condizione sufficiente è:

$$\text{Var}[\hat{\theta}_N] = \sigma^2_\varepsilon(\Phi^\top\Phi)^{-1} \to 0_{p,p}$$

Questo accade se $\left(\sum_{i=1}^N \varphi_i\varphi_i^\top\right)^{-1} \to 0$ (equivalente a **eccitazione persistente**).

### Eccitazione persistente

$$\lim_{N\to\infty} v^\top \sum_{i=1}^N \varphi_i\varphi_i^\top v \to +\infty \quad \forall v \in \mathbb{R}^p$$

I regressori devono "esplorare" tutte le direzioni di $\mathbb{R}^p$. Se i regressori si ripetono uguali per $i \geq n_0$, la condizione viene meno.

**Condizione sufficiente**: $\frac{1}{N}\sum_{i=1}^N \varphi_i\varphi_i^\top \to M \succ 0$.

**Sottospazio di eccitazione** $\mathcal{E}$: direzioni lungo cui c'è eccitazione. **Sottospazio di non-eccitazione** $\mathcal{U} = \mathcal{E}^\perp$: direzioni in cui la stima non converge.

## Migliore Stimatore Lineare Non Polarizzato (BLUE)

**Teorema di Gauss-Markov**: se $\varepsilon_i$ sono i.i.d. con media 0 e varianza $\sigma^2_\varepsilon$, lo stimatore OLS $\hat{\theta}$ ha varianza minima tra tutti gli stimatori lineari non polarizzati.

Per qualsiasi altro stimatore lineare non polarizzato $L \in \mathbb{R}^{p\times N}$:
$$\text{Var}[LY] - \text{Var}[\hat{\theta}] = \sigma^2_\varepsilon KK^\top \geq 0$$

dove $L = (\Phi^\top\Phi)^{-1}\Phi^\top + K$ con $K\Phi = 0$.

**Generalizzazione WLS**: se $\mathbb{E}[\mathcal{E}\mathcal{E}^\top] = \Sigma \succ 0$ (errori correlati o varianze diverse), il BLUE è $\hat{\theta}_W = (\Phi^\top\Sigma^{-1}\Phi)^{-1}\Phi^\top\Sigma^{-1}Y$, con pesi ottimali $w_i = 1/\sigma^2_i$.

> Solo quando il rumore è gaussiano, OLS è ottimale in senso assoluto (anche rispetto a stimatori nonlineari).

## Stima della varianza del rumore

La varianza $\sigma^2_\varepsilon$ è tipicamente sconosciuta. Si stima con:

$$\hat{\sigma}^2_\varepsilon = \frac{1}{N-p}\sum_{i=1}^N(y_i - \varphi_i^\top\hat{\theta})^2$$

Si può dimostrare che $\mathbb{E}[\hat{\sigma}^2_\varepsilon] = \sigma^2_\varepsilon$ (stimatore non polarizzato), grazie al fatto che:
$$\mathbb{E}\left[\sum_{i=1}^N(y_i - \varphi_i^\top\hat{\theta})^2\right] = \sigma^2_\varepsilon(N-p)$$

Il termine $(N-p)$ sono i **gradi di libertà** dei residui: $N$ misure meno $p$ parametri stimati.

La varianza dello stimatore si stima come $\widehat{\text{Var}}[\hat{\theta}] = \hat{\sigma}^2_\varepsilon(\Phi^\top\Phi)^{-1}$.

## Costo atteso e scomposizione degli errori

Il costo atteso $\bar{J}(\theta) = \mathbb{E}[(y_i - \varphi_i^\top\theta)^2]$ si decompone in:
$$\bar{J}(\theta) = \sigma^2_o + (\theta - \theta^o)^\top\Sigma(\theta - \theta^o)$$

dove $\sigma^2_o = \bar{J}(\theta^o)$ è l'errore irriducibile (dovuto al rumore e alla mancata copertura di $f^*$ da parte di $\mathcal{M}$), e $\theta^o$ è il parametro ottimale teorico.

Il costo atteso valutato nello stimatore $\hat{\theta}$ è il **Final Prediction Error**:

## Selezione del modello: Final Prediction Error (FPE)

$$\text{FPE} = \mathbb{E}[\bar{J}(\hat{\theta})] \approx \frac{N+p}{N-p} \cdot \frac{1}{N}\sum_{i=1}^N(y_i - \varphi_i^\top\hat{\theta})^2$$

- Il fattore $\frac{N+p}{N-p}$ penalizza la complessità $p$.
- Il termine $\frac{1}{N}\sum r_i^2$ misura il fit ai dati (decresce con $p$).
- Si calcola FPE per diversi valori di $p$, si sceglie il modello con FPE minimo.

> FPE aumenta sia con complessità eccessiva (overfitting) che insufficiente (underfitting). È equivalente all'AIC (Akaike Information Criterion) in molti contesti.

## Regolarizzazione

Alternativa a FPE: penalizzare la complessità nella funzione di costo.

### Ridge regression ($l_2$-regolarizzazione)

$$\hat{\theta} = \arg\min_\theta \|\Phi\theta - Y\|^2_2 + \alpha\|\theta\|^2_2$$

Soluzione: $\hat{\theta} = (\Phi^\top\Phi + \alpha I)^{-1}\Phi^\top Y$.

- $\alpha \to 0$: tende a OLS standard.
- $\alpha \to \infty$: $\hat{\theta} \to 0$.
- Alcune componenti di $\theta$ restano piccole (non esattamente zero).

**Variante con prior**: $\hat{\theta} = \arg\min \|\Phi\theta - Y\|^2_2 + \alpha\|\theta - \bar{\theta}\|^2_2$ (conoscenza a priori su $\theta$).

### Lasso regression ($l_1$-regolarizzazione)

$$\hat{\theta} = \arg\min_\theta \|\Phi\theta - Y\|^2_2 + \alpha\|\theta\|_1$$

- Forza alcune componenti **esattamente a zero** (modello sparso).
- Preferita quando si vuole selezionare un sottoinsieme di feature.

### $l_0$-regolarizzazione

$$\hat{\theta} = \arg\min_\theta \|\Phi\theta - Y\|^2_2 + \alpha\|\theta\|_0$$

dove $\|\theta\|_0$ = numero di componenti non nulle. Problema non convesso, difficile da risolvere.

## Articoli correlati

- [[introduzione-e-setup]]
- [[minimi-quadrati-ordinari]]
- [[identificazione-sistemi]]

## Fonti

- Bontempi & Venturini, *Data Driven System Modelling*, March 2020 (`raw/Data_driven_COMPILED.pdf`, Cap. 4)
- Ramponi, *Lecture notes DDSM — Residual variance, FPE* (`raw/Residual variance, FPE_COMPILED.pdf`)
