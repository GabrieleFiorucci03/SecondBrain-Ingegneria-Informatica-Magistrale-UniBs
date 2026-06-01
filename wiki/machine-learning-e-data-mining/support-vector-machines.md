---
tags: [machine-learning, svm, classificazione, kernel, supervisionato]
data_creazione: 2026-05-29
data_aggiornamento: 2026-05-29
fonti:
  - raw/MLDM-Support-Vector-Machines.pdf
---

# Support Vector Machines (SVM)

Le Support Vector Machines sono algoritmi di classificazione (e regressione) sviluppati negli anni '90 che trovano il **iperpiano separatore a margine massimo** tra le classi. Usano funzioni kernel per separare classi non linearmente separabili senza minimi locali.

## Punti chiave

- Anni '80: quasi tutti i metodi erano superfici di decisione **lineari** (buone proprietà teoriche ma limitati).
- Anni '80-'90: DT e reti neurali consentono superfici non-lineari, ma con problemi di minimi locali e poca teoria.
- Anni '90: SVM — algoritmi efficienti per funzioni non-lineari con **solide basi teoriche** (computational learning theory).
- Due idee chiave: **funzioni kernel** (generalizzazione della similarità tramite prodotti scalari) e **ottimizzazione quadratica** (evita minimi locali delle NN).
- L'algoritmo è un **problema di ottimizzazione**, non una ricerca greedy.

## Rappresentazione geometrica

Ogni istanza (paziente, esempio) con n attributi (feature) è un **vettore in R^n**, con coda all'origine e testa nei valori delle feature. La classificazione corrisponde a trovare un iperpiano che separa i due insiemi di vettori.

## Idee chiave

### Kernel Functions

Generalizzano il concetto di "similarità" attraverso prodotti scalari. Permettono di proiettare implicitamente i dati in spazi ad alta dimensione dove sono linearmente separabili, senza calcolare esplicitamente la proiezione.

Kernel comuni:
- Lineare: K(x,y) = x·y
- Polinomiale: K(x,y) = (x·y + c)^d
- RBF (Gaussian): K(x,y) = exp(-||x-y||² / 2σ²)

### Ottimizzazione Quadratica

Invece di ricerca greedy (come nei DT) o gradient descent con minimi locali (NN), SVM risolve un problema di **ottimizzazione quadratica convessa** → soluzione unica garantita, nessun minimo locale.

## Fondamenti Matematici

### Vettori e operazioni

Ogni istanza con n attributi è rappresentata come un **vettore in Rⁿ** (con coda all'origine). I punti corrispondono alle teste dei vettori.

Operazioni fondamentali:
- **Moltiplicazione scalare**: cā = (ca₁, ca₂, ..., caₙ) — stretching nella stessa/opposta direzione.
- **Addizione**: ā + b̄ = (a₁+b₁, ..., aₙ+bₙ)
- **Norma L2**: ‖ā‖₂ = √(a₁² + a₂² + ... + aₙ²)
- **Prodotto scalare (dot product)**: ā·b̄ = Σ aᵢbᵢ = ‖ā‖‖b̄‖cosθ
  - Vettori perpendicolari: ā·b̄ = 0
  - La regressione lineare y = w̄·x̄ + b è un dot product

### Iperpiani come decision surface

Un **iperpiano** in Rⁿ è uno spazio piatto di dimensione n-1:
- In R²: è una retta
- In R³: è un piano

**Equazione**: `w̄·x̄ + b = 0`

dove w̄ è il **vettore normale** (perpendicolare all'iperpiano) e b è l'offset.

**Derivazione**: P è sull'iperpiano sse (x̄ - x̄₀) ⊥ w̄, cioè w̄·(x̄-x̄₀)=0 → w̄·x̄ + b = 0 con b = -w̄·x̄₀.

**Variazione di b**: spostare b significa traslare l'iperpiano parallelamente a se stesso lungo la direzione di w̄.

**Distanza tra due iperpiani paralleli** w̄·x̄ + b₁ = 0 e w̄·x̄ + b₂ = 0:
```
D = |b₁ - b₂| / ‖w̄‖
```

### Classificazione con iperpiano

Segno di `w̄·x̄ + b`:
- Positivo (+) → punto nel semispazio positivo → classe +1
- Negativo (−) → punto nel semispazio negativo → classe -1

## Margine Massimo

Il cuore di SVM: trovare l'iperpiano separatore che **massimizza il margine** (distanza tra l'iperpiano e i punti più vicini di ciascuna classe = **support vectors**).

Margine = 2/‖w̄‖ → massimizzare il margine equivale a **minimizzare ‖w̄‖**.

Problema di ottimizzazione **quadratica convessa** → soluzione unica, no minimi locali.

## Articoli correlati

- [[alberi-di-decisione]]
- [[reti-neurali-artificiali]]
- [[ensemble-methods]]
- [[valutazione-ipotesi]]

## Fonti

- raw/MLDM-Support-Vector-Machines.pdf
