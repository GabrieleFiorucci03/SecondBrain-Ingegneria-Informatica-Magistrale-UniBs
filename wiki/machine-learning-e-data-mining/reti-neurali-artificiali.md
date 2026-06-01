---
tags: [machine-learning, reti-neurali, ANN, perceptron, backpropagation, deep-learning]
data_creazione: 2026-05-29
data_aggiornamento: 2026-05-29
fonti:
  - raw/12-ANN.pdf
---

# Reti Neurali Artificiali (ANN)

Le Artificial Neural Networks (ANN) sono modelli computazionali ispirati al cervello biologico: molte unità soglia interconnesse (neuroni artificiali) con pesi regolabili automaticamente. Permettono di apprendere funzioni complesse e non lineari.

## Punti chiave

- Ispirazione biologica: ~10^10 neuroni nel cervello umano, ~10^4-5 connessioni per neurone, elaborazione altamente parallela.
- Proprietà: molte unità threshold, connessioni pesate, processo parallelo e distribuito, **tuning automatico dei pesi**.
- Adatte quando: input ad alta dimensionalità (discreto o reale), output discreto o reale o vettore, dati rumorosi, forma della funzione target sconosciuta, leggibilità del risultato non importante.
- Argomenti principali: Perceptron, gradient descent, multilayer networks, backpropagation, rappresentazioni hidden layer, face recognition.

## Quando usare le reti neurali

- Input è high-dimensional (es. raw sensor input, immagini)
- Output è discreto, reale, o un vettore di valori
- Possibile rumore nei dati di training
- La forma della target function è sconosciuta
- La leggibilità del risultato è secondaria
- Applicazioni: riconoscimento vocale (phonemes), classificazione di immagini

## Perceptron

Un **Perceptron** è un'unità soglia: calcola una somma pesata degli input e applica una funzione segno.

```
o(x) = sgn(w · x) = { 1 if w₀ + w₁x₁ + ... + wₙxₙ > 0
                     {-1 otherwise
```

- `-w₀` è la **threshold** (soglia).
- Notazione vettoriale: aggiungendo x₀ = 1, si ottiene `o(x) = sgn(w·x)`.
- **Decision surface**: iperpiano definito da `w·x = 0` — linea in R², piano in R³.
- Può rappresentare AND, OR, NOT.
- **Non può** rappresentare XOR (non linearmente separabile).

### Perceptron Training Rule

Obiettivo: trovare w tale che il Perceptron classifichi correttamente tutti gli esempi.

```
Δwᵢ = η(t - o)xᵢ
```

dove:
- t = c(x) = valore target noto
- o = output del Perceptron
- η (eta) = learning rate (piccola costante, es. 0.05)

**Convergenza garantita** se e solo se: (1) dati linearly separable, (2) η sufficientemente piccolo.

Se i dati sono linearmente separabili → converge alla soluzione esatta.  
Se non lo sono → converge alla configurazione con errore minimo.

## Linear Unit e Gradient Descent

**Linear Unit** (Perceptron senza soglia): `o(x) = w · x`

Minimizza la **somma degli errori quadratici**:
```
E[w] = ½ Σₖ (t⁽ᵏ⁾ - w·x⁽ᵏ⁾)²
```

### Gradient Descent

Il gradiente indica la direzione di massima salita di E. La **regola di aggiornamento**:
```
Δw = -η∇E[w]    →    Δwᵢ = -η ∂E/∂wᵢ
```

Derivando:
```
∂E/∂wᵢ = Σₖ (t⁽ᵏ⁾ - o(x⁽ᵏ⁾))(-xᵢ⁽ᵏ⁾)
Δwᵢ = η Σₖ (t⁽ᵏ⁾ - o(x⁽ᵏ⁾)) xᵢ⁽ᵏ⁾
```

**Proprietà**: la superficie di E per una Linear Unit ha sempre una forma **parabolica** con un solo minimo assoluto → il gradient descent trova sempre la soluzione ottimale.

### Batch vs Incremental (Stochastic) Gradient Descent

**Batch Gradient Descent**:
```
Calcola ∇E_D[w] su tutti gli esempi D
w ← w - η∇E_D[w]
```

**Incremental (Stochastic) Gradient Descent**:
```
Per ogni d⁽ᵏ⁾ ∈ D:
  Calcola ∇E_{d⁽ᵏ⁾}[w]
  w ← w - η∇E_{d⁽ᵏ⁾}[w]
  (oppure: wᵢ ← wᵢ - η(t-o)xᵢ)
```

La regola incrementale è la **Delta Rule** (Least Mean Squares, LMS).

**Vantaggi dell'incremental**: aggiornamento più frequente, meno sensibile ai minimi locali (per reti più complesse del perceptron), può approssimare il batch con η piccolo.

## Multilayer Networks e Sigmoid Unit

Il Perceptron non può rappresentare XOR → serve una rete **multistrato**.

**Problema**: le unità threshold non sono differenziabili → gradient descent impossibile.  
**Soluzione**: unità **Sigmoid** (differenziabili).

### Sigmoid Unit

```
σ(x) = 1 / (1 + e^(-net))    con net = Σ wᵢxᵢ
```

Proprietà chiave: `dσ/dx = σ(x)(1 - σ(x))` — la derivata è espressa in termini dell'output.

### ReLU (Rectified Linear Unit)

```
y = max(0, x)
```

Le NN più recenti usano ReLU (più semplice, vanishing gradient minore).

## Backpropagation

Algoritmo per addestrare reti multistrato tramite gradient descent sulla funzione di errore.

### Funzione di errore in una 2-layer network

```
E(w) = ½ Σ_{d∈D} Σ_{μ=1}^{n_out} (tμ⁽ᵈ⁾ - oμ(x⁽ᵈ⁾))²
```

Con multiple layer → la superficie di E può avere **multipli minimi locali** (a differenza di una Linear Unit che ha un solo minimo globale).

### Algoritmo Backpropagation (2 layer)

```
Inizializza tutti i pesi wⱼᵢ a valori piccoli casuali.
Repeat (finché condizione di terminazione):
  Per ogni training example ⟨x,t⟩:
    1. Input x → calcola output di tutta la rete
    2. Per ogni output unit k: δₖ = oₖ(1 - oₖ)(tₖ - oₖ)
    3. Per ogni hidden unit h: δₕ = oₕ(1 - oₕ) Σ_{k∈outputs} wₖₕδₖ
    4. Aggiorna ogni peso: wⱼᵢ ← wⱼᵢ + Δwⱼᵢ = wⱼᵢ + η·δⱼ·xⱼᵢ
```

**Nota**: δₖ = errore (t-o) pesato dalla derivata del sigmoid (analogo alla Delta Rule).  
δₕ non ha target diretto → usa gli errori δₖ degli output units downstream pesati per wₖₕ.

### Reti di profondità arbitraria

Per ogni hidden unit r al layer m:
```
δᵣ = oᵣ(1 - oᵣ) Σ_{s ∈ layer m+1} wₛᵣδₛ
```
Per DAG arbitrari: Σ su `Downstream(r)` = tutti i nodi che ricevono output di r.

### Terminazione e Momentum

**Condizioni di terminazione**:
- Numero fisso di iterazioni (epoche)
- Errore sotto una soglia
- Errore su validation set sotto una soglia

Troppo poche iterazioni → underfitting. Troppo molte → overfitting.

**Momentum** α (per accelerare convergenza e uscire da minimi locali):
```
Δwⱼᵢ(n) = η·δⱼ·xⱼᵢ + α·Δwⱼᵢ(n-1)
```

### Convergenza e minimi locali

Backpropagation converge verso **minimi locali** (non globali).  
Strategia empirica: addestrare più reti con diverse inizializzazioni → prendere la migliore o la media degli output.

## Capacità espressiva delle reti

La capacità dipende da **ampiezza** (unità per layer) e **profondità** (numero di layer).

- **Boolean functions**: ogni funzione booleana è rappresentabile con **2 layer** di unità (nel caso peggiore il numero di unità hidden è esponenziale nel numero di input).
- **Funzioni continue**: con sufficiente ampiezza del primo hidden layer si può approssimare qualsiasi funzione continua.

## Capacità espressiva: versione completa

| Tipo di funzione | Architettura necessaria |
|-----------------|------------------------|
| **Boolean** | 2 layer (hidden + output); hidden units esponenziali nel caso peggiore |
| **Continua** | 2 layer (sigmoid hidden + unthresholded output); numero hidden dipende dalla funzione |
| **Arbitraria** | 3 layer (2 hidden + output); numero unità non noto |

## Rappresentazioni apprese negli hidden layer

Esempio classico: **funzione identità** su 8 input binari (`f(x) = x`).

Architettura: 8 input → 3 hidden → 8 output. Dopo 5000 epoche (convergenza):

| Input | Hidden values | Output |
|-------|--------------|--------|
| 10000000 | .89, .04, .08 | 10000000 |
| 01000000 | .15, .99, .99 | 01000000 |
| 00100000 | .01, .97, .27 | 00100000 |
| ... | ... | ... |

I valori hidden (arrotondati) corrispondono alla **codifica binaria** di 1-8 con 3 bit. La rete ha **scoperto autonomamente** la rappresentazione binaria senza che fosse specificata.

## Hypothesis Space e Inductive Bias

**Hypothesis Space**: spazio Euclideo n-dimensionale dei pesi della rete → **continuo** (a differenza degli alberi di decisione o del Version Space).

**Inductive Bias**: interpolazione smooth tra i punti dati. La rete generalizza "smussando" la funzione tra i dati osservati.

## Overfitting nelle ANN e come evitarlo

Sintomo: errore sul training decresce, errore sul validation set aumenta dopo certe iterazioni.

**Strategia con k-fold cross validation**:
1. Repeat k volte: dividi training in k subset, usa uno come validation, trova il numero di iterazioni ī che minimizza l'errore sul validation set.
2. Calcola la media ī.
3. Esegui la backpropagation su tutti gli esempi per ī iterazioni.
4. Valuta sul test set separato.

## Varianti avanzate

### Regularization (Weight Decay)

Aggiungere un termine di penalità per pesi grandi:
```
E(w) = ½ Σ_{d∈D} Σ_μ (tμ - oμ(x))² + γ Σᵢ,ⱼ w²ⱼᵢ
```

Penalizza pesi grandi → riduce l'overfitting. Equivalente a un'assunzione a priori che i pesi siano piccoli.

### Alternative al Gradient Descent

- **Line search**: scelta ottimale dello step size
- **Conjugate gradient**: metodo più efficiente per superfici di errore quadratiche

### Reti Ricorrenti (Recurrent Networks)

Supportano **cicli diretti**: l'output al tempo t è input ad alcune unità al tempo t+1. Capaci di modellare processi sequenziali e temporali.

### Dynamic Modification della Struttura

- **Cascade-Correlation** (Fahlman & Lebiere, 1990): aggiunta incrementale di hidden units
- **Optimal Brain Damage** (LeCun et al., 1990): pruning incrementale dei pesi
- Risultati empirici: **misti** — non sempre superano le architetture fisse.

## Connessioni con altri modelli

Le ANN sono collegate alle SVM attraverso la teoria dell'apprendimento computazionale. Entrambe soffrono di problemi di **minimi locali** nella ricerca del modello — le SVM li evitano con ottimizzazione quadratica, le ANN li affrontano con gradient descent e varianti.

## Articoli correlati

- [[support-vector-machines]]
- [[overfitting-decision-trees]]
- [[ensemble-methods]]
- [[valutazione-ipotesi]]

## Fonti

- raw/12-ANN.pdf
