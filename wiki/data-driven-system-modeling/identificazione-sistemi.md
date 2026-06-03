---
tags: [ddsm, identificazione-sistemi, processi-stocastici, arma, arx, pem, predittore-ottimale]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/Data_driven_COMPILED.pdf
  - raw/sida_COMPILED.pdf
  - raw/sheet_COMPILED.pdf
  - raw/ztransform2_COMPILED.pdf
---

# Identificazione di Sistemi Dinamici

Il capitolo applica i minimi quadrati all'identificazione di sistemi dinamici stocastici a tempo discreto. Si passa dalla regressione statica a quella dinamica, dove il tempo e la memoria del sistema giocano un ruolo centrale.

## Punti chiave

- Un processo stazionario in senso lato (WSS) si decompone in parte deterministica e parte non deterministica (**decomposizione di Wold**).
- I modelli di serie temporali principali sono: MA, AR, ARMA, ARX, ARMAX.
- Il **predittore ottimale** di un processo PND è la proiezione ortogonale su $H_{t-1}$ (il passato).
- Per sistemi minimum phase, il predittore ottimale si calcola con il filtro di Wiener: $\hat{y}(t|t-1) = (1 - 1/W(z))y(t)$.
- Il **Prediction Error Method (PEM)** stima i parametri minimizzando gli errori di predizione.
- Per modelli ARX, PEM coincide con OLS; per ARMA il problema diventa non convesso.
- La consistenza del PEM è garantita sotto opportune condizioni (3 teoremi).

## Processi stocastici stazionari (WSS)

**Processo stazionario in senso lato** $\{y(t)\}_{-\infty}^{+\infty}$:
- Media costante: $m(t) = \mathbb{E}[y(t)] = \text{cost}$
- Correlazione dipende solo dal lag: $r_y(\tau) = \mathbb{E}[y(t)y(t+\tau)]$

**Densità spettrale di potenza**: $S_y(\omega) = \mathcal{F}[r_y](\omega) = \sum_{\tau=-\infty}^{+\infty} r_y(\tau)e^{-j\omega\tau}$

$S_y(\omega)$ è reale, pari, non negativa. Spettro piatto $\Leftrightarrow$ processo scarsamente correlato.

**Rumore bianco** $\{e(t)\}$: i.i.d., media 0, varianza $\sigma^2_e$. Correlazione: $r_e(\tau) = \sigma^2_e\delta(\tau)$. Spettro costante: $S_e(\omega) = \sigma^2_e$.

### Processi deterministici vs non deterministici

- **Puramente deterministico (PD)**: conoscere un campione permette di predire tutti gli altri (es. sinusoide $A\sin(\bar{\omega}t + \phi)$).
- **Puramente non deterministico (PND)**: il futuro non è predicibile dal passato (es. rumore bianco).

### Decomposizione di Wold

Ogni processo WSS si decompone univocamente in:
$$y(t) = y_D(t) + y_{ND}(t)$$

dove $y_D(t) \in H_{-\infty}$ (parte PD) e $y_{ND}(t) = \sum_{\tau=-\infty}^{t} w(t-\tau)e(\tau)$ (filtraggio di rumore bianco).

### Filtraggio di rumore bianco (Teorema Wiener-Khinchin)

Se un processo WSS $\{u(t)\}$ entra in un sistema LTI stabile con funzione di trasferimento $W(z)$, l'uscita $\{y(t)\}$ è WSS con:
$$S_y(\omega) = |W(e^{j\omega})|^2 S_u(\omega)$$

**Esempio AR(1)**: $y(t) = ay(t-1) + e(t)$ con $|a| < 1$:
$$S_y(\omega) = \frac{\sigma^2_e}{1 + a^2 - 2a\cos(\omega)} \quad \text{(filtro passa-basso)}$$

## Classi di modelli per serie temporali

| Classe | Equazione | Note |
|--------|-----------|------|
| **MA(n)** | $y(t) = c_0e(t) + c_1e(t-1) + \ldots + c_ne(t-n)$ | n poli in 0, n zeri liberi |
| **AR(n)** | $y(t) = a_1y(t-1) + \ldots + a_ny(t-n) + e(t)$ | n zeri in 0, n poli liberi |
| **ARMA(m,n)** | $y(t) = \sum a_iy(t-i) + \sum c_je(t-j) + e(t)$ | m poli, n zeri |
| **ARX(m,p)** | $y(t) = \sum a_iy(t-i) + \sum b_ju(t-j) + e(t)$ | con ingresso esogeno |
| **ARMAX(m,n,p)** | combinazione di AR, MA e ingresso esogeno | più generale |

## Predittore ottimale di un processo PND

Per un processo PND $y(t) = \sum_{\tau=-\infty}^{t} w(t-\tau)e(\tau)$ con $W(\infty) = w(0) = 1$, il predittore ottimale è la proiezione ortogonale di $y(t)$ su $H_{t-1} = \overline{\text{span}}\{\ldots, e(t-2), e(t-1)\}$:

$$\hat{y}(t|t-1) = \sum_{\tau=-\infty}^{t-1} w(t-\tau)e(\tau) = W(z)e(t) - e(t)= (W(z)-1)e(t)$$

**Problema**: $e(t)$ non è osservabile. Se $W(z)$ è **minimum phase** (poli e zeri dentro il cerchio unitario), esiste l'inverso causale stabile $W^{-1}(z)$, e si può estrarre $e(t) = W^{-1}(z)y(t)$:

$$\hat{y}(t|t-1) = \left(1 - \frac{1}{W(z)}\right)y(t)$$

L'errore di predizione è $\tilde{y}(t|t-1) = y(t) - \hat{y}(t|t-1) = e(t)$ con $\text{Var} = \sigma^2_e$.

**Pipeline**: filtro sbiancante $\frac{1}{W(z)}$ (estrae $e(t)$ da $y(t)$) seguito dal filtro di predizione $W(z)-1$.

### Caso non minimum phase

Se $W(z)$ ha zeri fuori dal cerchio unitario, si fattorizza $W(z) = W_{MP}(z) \cdot H^{-1}(z)$ dove $H^{-1}(z)$ è un filtro all-pass ($|H^{-1}(e^{j\omega})|^2 = 1$). Si applica il predittore a $W_{MP}(z)$ con rumore bianco modificato di varianza $\sigma^2_{\bar{e}} \neq \sigma^2_e$.

### Predittore per AR(n)

Per $y(t) = a_1y(t-1) + \ldots + a_ny(t-n) + e(t)$, il predittore ottimale è semplicemente:
$$\hat{y}(t|t-1) = a_1y(t-1) + \ldots + a_ny(t-n)$$

(l'innovazione $e(t)$ è ortogonale al passato per causalità).

## Identificazione data-driven di sistemi AR

**Equazioni di Yule-Walker**: moltiplicando l'equazione AR per $y(t-k)$ e prendendo il valore atteso, si ottiene il sistema:

$$\begin{bmatrix} r_y(1) \\ r_y(2) \\ \vdots \end{bmatrix} = \underbrace{\begin{bmatrix} r_y(0) & r_y(1) & \cdots \\ r_y(1) & r_y(0) & \cdots \\ \vdots & & \ddots \end{bmatrix}}_{\text{matrice di Toeplitz}} \begin{bmatrix} a_1 \\ a_2 \\ \vdots \end{bmatrix}$$

Poiché $r_y(\tau)$ non è nota, si stima con $\hat{r}_y(\tau) = \frac{1}{T-\tau}\sum_{t=1}^{T-\tau} y(t)y(t+\tau)$.

## Prediction Error Method (PEM)

Data una classe di modelli $\mathcal{M}$ che definisce predittori $\hat{y}_\theta(t|t-1)$, si stima $\theta$ minimizzando gli errori di predizione:

$$\hat{\theta} = \arg\min_{\theta \in \mathbb{R}^p} \sum_t (y(t) - \hat{y}(t|t-1))^2$$

### PEM = OLS per modelli ARX

Per ARX$(m,p)$: $y(t) = a_1y(t-1) + \ldots + b_1u(t-1) + \ldots + e(t)$, il predittore è $\hat{y}(t|t-1) = \varphi_t^\top\theta$ con $\varphi_t = [y(t-1), \ldots, u(t-1), \ldots]^\top$.

PEM coincide con OLS: $\hat{\theta} = (\Phi^\top\Phi)^{-1}\Phi^\top Y$ (problema convesso).

### PEM per ARMA (problema difficile)

Per ARMA$(1,1)$: $y(t) = ay(t-1) + ce(t-1) + e(t)$, il predittore necessita di $e(t-1)$ non osservabile. Si sostituisce con il residuo $\varepsilon(t-1) = y(t-1) - \hat{y}(t-1|t-2)$, ma questo rende i regressori dipendenti da $\theta$ $\Rightarrow$ problema **non convesso** e difficile.

## Consistenza del PEM (3 Teoremi)

### Teorema 1 — ARMA (solo output)

Sotto le ipotesi: misure da un sistema stabile, rumore bianco i.i.d. forte, classe di modelli stabile e compatta, il PEM converge quasi certamente all'insieme degli ottimizzatori del costo vero:
$$D = \{\bar{\theta} \in \Theta : \bar{\theta} \text{ minimizza } \mathbb{E}[(y(t)-\hat{y}_\theta(t))^2]\}$$

### Teorema 2 — ARX (con ingresso, no feedback)

Stesse ipotesi + l'ingresso $\{u(t)\}$ è quasi-stazionario, "sufficientemente informativo" (eccitazione persistente di ordine $\geq$ numero di ritardi dell'ingresso), e indipendente dal rumore. La condizione sull'ingresso **esclude la presenza di feedback**.

### Teorema 3 — Con feedback (chiuso in anello)

Con un segnale di riferimento $\{v(t)\}$ che chiude l'anello di controllo: la condizione di persistente eccitazione si sposta su $v(t)$ anziché su $u(t)$.

> Se $D = \{\bar{\theta}\}$ è un singoletto, i tre teoremi garantiscono $\hat{\theta} \to \bar{\theta}$ (convergenza puntuale).

### Esempio: identificazione di MA(1) con AR(1)

Se il processo vero è MA(1): $y(t) = e(t) + c^*e(t-1)$, e si usa il modello AR(1): $y(t) = ay(t-1) + \eta(t)$, il PEM converge a:
$$a^o = \frac{c^*}{1+c^{*2}}$$

(non al parametro vero del sistema, ma al migliore AR(1) che approssima il processo MA(1)).

## Articoli correlati

- [[introduzione-e-setup]]
- [[minimi-quadrati-ordinari]]
- [[proprieta-statistiche-stimatori-ls]]

## Fonti

- Bontempi & Venturini, *Data Driven System Modelling*, March 2020 (`raw/Data_driven_COMPILED.pdf`, Cap. 5)
- Ramponi, *Lecture notes on system identification and data analysis* (`raw/sida_COMPILED.pdf`, Cap. 3)
- Ramponi, *D.D.S.M. — Consistency of the PEM method* (`raw/sheet_COMPILED.pdf`)
