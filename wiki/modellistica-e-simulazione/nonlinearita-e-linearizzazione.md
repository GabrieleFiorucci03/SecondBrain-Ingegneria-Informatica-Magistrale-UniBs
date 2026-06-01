---
tags: [modellistica, nonlinearità, linearizzazione, feedback-linearization, derivata-di-Lie, funzione-di-trasferimento]
data_creazione: 2026-05-30
data_aggiornamento: 2026-05-30
fonti:
  - raw/MODSIM_L5_NL.pdf
---

# Nonlinearità e Linearizzazione

La **nonlinearità** è la regola, non l'eccezione, nei sistemi reali. Questo articolo raccoglie le tecniche per trattarla e il collegamento formale tra rappresentazione State-Space e forma Input-Output per sistemi lineari.

## Punti chiave

- Due approcci ai sistemi nonlineari: **approssimazione** (linearizzazione classica) e **trasformazione** (feedback linearization).
- La linearizzazione classica è utile per il controllo e per l'analisi di stabilità locale attorno a un punto di equilibrio.
- Il passaggio State-Space → I-O avviene tramite la **trasformata di Laplace** → funzione di trasferimento W(s).
- Il passaggio I-O → State-Space non è univoco; produce solo la parte raggiungibile e osservabile.
- La **feedback linearization** usa una retroazione nonlineare per rendere il sistema "visto dall'esterno" lineare.
- Il **grado relativo** è il numero di volte che si deve derivare l'uscita per far comparire esplicitamente l'ingresso.

## Approcci alle nonlinearità

```
Sistema Nonlineare
├── Approssimazione → Linearizzazione classica (attorno all'equilibrio)
└── Trasformazione  → Feedback linearization
```

La scelta dipende dallo scopo del modello (controllo? analisi? simulazione?) e da quanto è "forte" la nonlinearità.

**N.B.:** un tempo la linearizzazione era necessaria anche perché simulare sistemi nonlineari era oneroso. Oggi questo è meno sentito; rimane fondamentale per il **progetto del controllore** (tecniche di controllo note: spazio degli stati u=-KX, basate su Bode/funzione di trasferimento).

## Linearizzazione Classica

Dato il sistema nonlineare:
```
ẋ = f(x, u)
y = g(x, u)
```

Espansione di Taylor al primo ordine attorno al punto di equilibrio (x̄, ū):

```
ẋ ≈ f(x̄,ū) + (∂f/∂x)|x̄,ū · δx + (∂f/∂u)|x̄,ū · δu
  = A·δx + B·δu         (poiché f(x̄,ū) = 0 all'equilibrio)

y ≈ g(x̄,ū) + (∂g/∂x)|x̄,ū · δx + (∂g/∂u)|x̄,ū · δu
  = ȳ + C·δx + D·δu
```

Le matrici A, B, C, D sono le **Jacobiane** di f e g valutate nell'equilibrio. Il sistema linearizzato è valido solo per **piccole variazioni** δx e δu attorno a (x̄, ū).

## Passaggio State-Space ↔ Forma Input-Output

### Da State-Space a I-O (via Laplace)

Per un sistema LTI SISO (ẋ = Ax+Bu, y = Cx+Du), la **funzione di trasferimento** è:

```
W(s) = Y(s)/U(s) = C(sI - A)⁻¹B + D
```

W(s) è una **funzione razionale fratta** con numeratore di grado m e denominatore di grado n.

### Da I-O a State-Space

L'EDO input-output:
```
y⁽ⁿ⁾ + a₁y⁽ⁿ⁻¹⁾ + ... + aₙy = b₀u⁽ᵐ⁾ + ... + bₘu
```

**Caso 1** (bₘ=1, bᵢ=0 ∀i≠m → W(s) = 1/denominatore):

Pongo x₁=y, x₂=ẏ, ..., xₙ=y⁽ⁿ⁻¹⁾, ottenendo la **forma canonica di raggiungibilità**:

```
A = [[0,1,0,...,0],[0,0,1,...,0],...,[-aₙ,-aₙ₋₁,...,-a₁]]
B = [0,0,...,0,1]ᵀ,  C = [1,0,...,0]
```

**Caso 2** (m < n): si introduce una variabile ausiliaria z tale che W(s) = Z(s)/U(s) · Y(s)/Z(s); si applica il Caso 1 a Z/U, poi l'uscita è y = bₘx₁ + bₘ₋₁x₂ + ... + b₀xₘ₊₁.

**Caso 3** (m = n): si divide numeratore per denominatore → termine D (feed-through) + parte con m < n (Caso 2).

**NOTA:** il passaggio I-O → SS non è univoco. Rappresenta solo la parte raggiungibile e osservabile del sistema.

## Feedback Linearization

Tecnica di **trasformazione esatta** (non approssimazione): si usa una retroazione nonlineare dallo stato che "cancella" le nonlinearità, così il controllore esterno vede un sistema lineare.

**Concetti matematici preliminari:**

- **Campo vettoriale smooth**: f continua con tutte le derivate parziali fino all'ordine necessario
- **Derivata di Lie di h rispetto a f**: Lₓh = ∇h · f = [∂h/∂x₁,...,∂h/∂xₙ] · [f₁,...,fₙ]ᵀ
  - Lf²h = ∇(Lfh)·f, in generale Lf^r·h = ∇(Lf^(r-1)h)·f
  - Se g è un altro campo vettoriale: LgLfh = ∇(Lfh)·g
- **Diffeomorfismo**: T:Rᵈ→Rⁿ differenziabile, invertibile con inversa differenziabile
- **Grado relativo** di un sistema S: numero di derivazioni dell'uscita necessarie per ottenere dipendenza esplicita dall'ingresso

## Articoli correlati

- [[introduzione-modellistica]]
- [[modelli-fisici]]
- [[modelli-popolazioni]]
- [[identificazione-modelli]]

## Fonti

- raw/MODSIM_L5_NL.pdf
