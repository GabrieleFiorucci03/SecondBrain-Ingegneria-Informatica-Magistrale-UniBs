---
tags: [ddsm, regressione, modelli-parametrici, classe-di-modelli]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/Data_driven_COMPILED.pdf
  - raw/01_regression_COMPILED.pdf
---

# Introduzione e Setup del Contesto

Il corso Data Driven System Modeling (DDSM) studia come costruire modelli matematici di sistemi partendo da dati empirici, senza dover derivare un modello dalle leggi fisiche. L'approccio è utile quando la fisica è troppo complessa, sconosciuta o troppo costosa da modellare analiticamente.

## Punti chiave

- Un **modello** è una limitazione sull'associazione tra ingressi e uscite di un sistema.
- L'obiettivo è trovare una funzione $\hat{f}$ che approssimi (non interpoli esattamente) i dati, tollerando il rumore.
- La scelta della classe di modelli richiede buon senso e conoscenza a priori del sistema.
- **Regressione** (output continuo) vs **Classificazione** (output discreto): nel corso si tratta la regressione.
- **Statica** (nessuna memoria, no dipendenza dal tempo) vs **Dinamica** (il sistema ha stato, il passato conta).
- Il trade-off fondamentale è **complessità vs generalizzazione**: modelli troppo semplici underfittano, troppo complessi overfittano.

## Setup del problema

Il sistema genera misure secondo:
$$y_i = f^*(u_i) + \varepsilon_i, \quad i = 1, \ldots, N$$

dove:
- $u_i \in \mathcal{U}$: ingresso (osservabile, assunto esatto)
- $y_i \in \mathbb{R}$: uscita (osservabile, continua $\Rightarrow$ regressione)
- $\varepsilon_i$: rumore additivo (non osservabile, variabile aleatoria)
- $f^*: \mathcal{U} \to \mathbb{R}$: la funzione vera, sconosciuta, da stimare

Il **training set** è $\{(u_i, y_i)\}_{i=1}^N$. Le misure sono corrotte da rumore (outliers possibili).

## Classi di modelli

### Classe parametrica

Una **classe di modelli parametrica** ha la forma:
$$\mathcal{M} = \{f_\theta(u) = f(u, \theta) : \theta \in \mathbb{R}^p\}$$

Ogni $\theta$ identifica univocamente una funzione. Trovare $\hat{f}$ si riduce a trovare $\hat{\theta} \in \mathbb{R}^p$ (**stima parametrica**).

### Ipotesi di linearità (classe lineare)

Per rendere il problema computazionalmente trattabile si introduce l'**ipotesi di linearità**: $\mathcal{M}$ è uno spazio vettoriale $p$-dimensionale di funzioni, con:
$$\mathcal{M} = \{f_\theta(u) = \varphi(u)^\top \theta : \theta \in \mathbb{R}^p\}$$

dove $\varphi: \mathcal{U} \to \mathbb{R}^p$ è il **vettore di regressori** (può essere nonlineare in $u$, ma il modello è lineare in $\theta$).

**Esempi**:
- Funzioni affini: $\varphi(u) = [1, u^{(1)}, u^{(2)}, u^{(3)}]^\top$
- Polinomio di grado 3: $\varphi(u) = [1, u, u^2, u^3]^\top$
- $\varphi_i := \varphi(u_i)$ sono chiamati **regressori** (uno per ogni misura)

### Regole per scegliere la classe di modelli

1. La classe deve poter spiegare ragionevolmente il training set.
2. Non deve spiegarlo troppo bene (evitare overfitting e non-unicità dello stimatore).
3. Usare qualsiasi conoscenza a priori su $f^*$.
4. Il problema numerico deve essere computazionalmente trattabile.

## Funzioni di costo per la stima

Dati i residui $r_i(\theta) = y_i - f(u_i, \theta)$, si minimizza una funzione di costo $J(\theta)$:

| Nome | Formula | Norma |
|------|---------|-------|
| Min-max (Chebyshev) | $\max_i \|r_i(\theta)\|$ | $l_\infty$ |
| Somma valori assoluti | $\sum_i \|r_i(\theta)\|$ | $l_1$ |
| **Minimi quadrati** | $\sum_i r_i(\theta)^2$ | $l_2$ |

Con l'ipotesi di linearità, tutti e tre diventano problemi convessi e facili da risolvere.

### Pro/contro dei minimi quadrati (OLS)

**Pro**: soluzione unica (salvo casi patologici), formula chiusa, aggiornamento ricorsivo, bella interpretazione geometrica, garantisce alcune proprietà probabilistiche, estensione naturale al caso dinamico.

**Contro**: difficile dare garanzie probabilistiche forti.

## Articoli correlati

- [[minimi-quadrati-ordinari]]
- [[proprieta-statistiche-stimatori-ls]]
- [[identificazione-sistemi]]

## Fonti

- Bontempi & Venturini, *Data Driven System Modelling*, March 2020 (`raw/Data_driven_COMPILED.pdf`, Cap. 1-2)
- Ramponi, *Regression: review*, slides DDSM (`raw/01_regression_COMPILED.pdf`)
