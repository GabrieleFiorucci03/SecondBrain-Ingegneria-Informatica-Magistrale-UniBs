---
tags: [probabilità, misura, variabili-aleatorie, convergenza, processi-stazionari, hilbert]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/[Campi]_Selected_Topics_in_Probability_COMPILED.pdf
---

# Fondamenti di Probabilità (Selected Topics)

Note di lezione di Marco C. Campi (UniBS, 2021). Trattano i fondamenti matematici della probabilità necessari per la teoria dei sistemi lineari stocastici a tempo discreto: predizione, filtraggio, controllo. Sono il substrato teorico per lo [[scenario-optimization]] e per [[sys-id-and-limits-learning]].

## Punti chiave

- La probabilità è formalizzata come **misura** su uno spazio misurabile (X, σ-algebra) → rigore necessario per gestire spazi di campioni infinito-dimensionali.
- Le variabili aleatorie sono **funzioni misurabili**; l'indipendenza si definisce in termini di σ-algebre.
- La **convergenza stocastica** ha diversi modi (quasi certa, in probabilità, in L², in distribuzione) con relazioni precise.
- Il **Teorema di Proiezione** in spazi di Hilbert è la radice matematica del filtraggio ottimo (Wiener, Kalman).
- I **processi stazionari in senso lato** (WSS) sono caratterizzati dalla funzione di autocorrelazione e dallo spettro di potenza — fondamentali per l'identificazione di sistemi.

## Capitolo 1: Spazi di misura e integrazione

### σ-algebra

**Def 1.1**: Una collezione 𝒳 di sottoinsiemi di un insieme X è una **σ-algebra** se:
- (a) X ∈ 𝒳
- (b) Aₖ ∈ 𝒳, k=1,2,... → ∪ₖ Aₖ ∈ 𝒳 (chiusa per unioni numerabili)
- (c) A ∈ 𝒳 → Aᶜ ∈ 𝒳 (chiusa per complemento)

Implica: ∅ ∈ 𝒳, chiusa per intersezioni numerabili.

**Def 1.2 (spazio misurabile)**: La coppia (X, 𝒳) con 𝒳 σ-algebra su X.

**Def 1.4 (funzione misurabile)**: g: X → X' è misurabile se ∀A' ∈ 𝒳', g⁻¹(A') ∈ 𝒳.

### Misura e spazio di misura

**Def 1.7 (misura)**: m: 𝒳 → [0,∞] è una misura se è σ-additiva:
```
m(∪ₖ Aₖ) = Σₖ m(Aₖ)  per Aₖ disgiunti
```

**Def 1.8 (spazio di misura)**: La tripla (X, 𝒳, m).

Caso fondamentale: **(ℝ, ℬ(ℝ), λ)** dove ℬ(ℝ) è la σ-algebra di Borel (generata dagli aperti (a,b)) e λ è la **misura di Lebesgue** (lunghezza di intervalli).

**Thm 1.9 (Carathéodory)**: Una pre-misura σ-finita su un'algebra 𝒜 si estende in modo unico a una misura su σ(𝒜).

### Integrale di Lebesgue

Costruito in 3 step:
1. Funzioni semplici: g = Σ αₖ 1(Aₖ) → ∫g dm = Σ αₖ m(Aₖ)
2. Funzioni misurabili non negative: ∫g dm = lim ∫gₙ dm (limite di semplici crescenti)
3. Generale: ∫g dm = ∫g⁺dm − ∫g⁻dm

**Thm 1.12 (cambio di spazio di integrazione)**: ∫_X h(g(x))dm(x) = ∫_X' h(x')dm'(x') dove m' = misura immagine di m attraverso g.

## Capitolo 2: Variabili aleatorie

Una **variabile aleatoria** (v.a.) è una funzione misurabile X: (Ω, ℱ, P) → (ℝ, ℬ(ℝ)).

### Indipendenza

Due v.a. X, Y sono **indipendenti** se la σ-algebra generata da X è indipendente da quella generata da Y:
```
P(A ∩ B) = P(A) · P(B)  ∀A ∈ σ(X), B ∈ σ(Y)
```

**Incorrelazione** (E[XY] = E[X]E[Y]) è condizione più debole: indipendenza ⟹ incorrelazione, non viceversa.

### Funzioni caratteristiche

La **funzione caratteristica** di X: φ_X(t) = E[e^{itX}]. Caratterizza completamente la distribuzione; utile per calcolare distribuzioni di somme di v.a. indipendenti.

### Variabili gaussiane

X ~ N(μ, σ²): φ_X(t) = exp(iμt − σ²t²/2).

Vettore gaussiano X ~ N(μ, Σ): la distribuzione è caratterizzata da media μ e matrice di covarianza Σ. **Proprietà chiave**: per gaussiane, incorrelazione ⟺ indipendenza.

### Densità indotta da una funzione

Se X ha densità f_X e Y = g(X), la densità di Y è:
```
f_Y(y) = f_X(g⁻¹(y)) / |g'(g⁻¹(y))|  (se g monotona)
```

## Capitolo 3: Convergenza stocastica

Quattro nozioni di convergenza per una sequenza di v.a. {Xₙ}:

| Tipo | Definizione | Forza |
|---|---|---|
| **Quasi certa** (a.s.) | P(lim Xₙ = X) = 1 | Forte |
| **In probabilità** | P(\|Xₙ−X\| > ε) → 0 | Media |
| **In L²** | E[\|Xₙ−X\|²] → 0 | Media |
| **In distribuzione** | F_{Xₙ}(x) → F_X(x) | Debole |

Relazioni: a.s. ⟹ in prob ⟹ in distribuzione; L² ⟹ in probabilità.

**LGN (Legge dei Grandi Numeri)**: Per {Xₙ} i.i.d. con E[\|X₁\|] < ∞: (1/N)Σ Xᵢ → E[X₁] q.c.

**TCL (Teorema Centrale del Limite)**: Per {Xₙ} i.i.d. con varianza finita σ²:
```
√N · (X̄_N − μ) →_d N(0, σ²)
```

## Capitolo 4: Teorema di Proiezione

### Spazi di Hilbert

Uno **spazio di Hilbert** H è uno spazio vettoriale con prodotto interno ⟨·,·⟩ completo rispetto alla norma \|x\| = √⟨x,x⟩.

Esempio principale: L²(Ω, ℱ, P) = {X: E[X²] < ∞} con ⟨X,Y⟩ = E[XY].

**Thm di Proiezione**: Per ogni sottospazio chiuso M ⊆ H e ogni x ∈ H, esiste un **unico** elemento x* ∈ M tale che:
```
‖x − x*‖ = min_{m ∈ M} ‖x − m‖
```

x* è la proiezione ortogonale di x su M: ⟨x − x*, m⟩ = 0 ∀m ∈ M.

**Applicazione**: La **stima lineare ottima** di Y dato X è la proiezione di Y sullo spazio lineare chiuso generato da X. Questo è il fondamento del filtro di Wiener e del filtro di Kalman.

## Capitolo 5: Speranza condizionale e densità condizionale

**E[Y | X]** è la proiezione di Y su L²(σ(X)) = {funzioni di X in L²}. Soddisfa:
```
E[E[Y|X] · g(X)] = E[Y · g(X)]  ∀g misurabile e limitata
```

**Densità condizionale** f_{Y|X}(y|x): se (X,Y) ha densità congiunta f_{X,Y}(x,y):
```
f_{Y|X}(y|x) = f_{X,Y}(x,y) / f_X(x)
```

## Capitolo 6: Processi stazionari in senso lato (WSS)

Un processo stocastico {Xₜ} è **WSS** se:
- E[Xₜ] = μ (costante)
- Cov(Xₜ, Xₜ₊ₖ) = R(k) (dipende solo dal lag k, non da t)

### Spettro di potenza

La **Trasformata di Fourier** della funzione di autocorrelazione R(k) dà la **densità spettrale di potenza** S(ω):
```
S(ω) = Σₖ R(k) e^{-iωk}
```

S(ω) ≥ 0 ∀ω (teorema di Bochner). L'area sotto S(ω) dà la varianza totale del processo.

**Rappresentazione spettrale**: Xₜ = ∫ e^{iωt} dZ(ω) dove Z è un processo ortogonale a incrementi.

### Processi multivariabili

Per X = [X₁, …, Xₙ]^T WSS: matrice di spettro di potenza S(ω) = {Sᵢⱼ(ω)} dove Sᵢⱼ = spettro incrociato tra Xᵢ e Xⱼ. S(ω) è hermitiana e semidefinita positiva ∀ω.

## Articoli correlati

- [[scenario-optimization]]
- [[sys-id-and-limits-learning]]
- [[noise-compensation]]

## Fonti

- raw/[Campi]_Selected_Topics_in_Probability_COMPILED.pdf — Note di lezione (Campi, UniBS, Jan. 2021)
