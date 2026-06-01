---
tags: [modellistica, dinamica-popolazioni, Malthus, logistico, Lotka-Volterra, sistemi-sociali]
data_creazione: 2026-05-30
data_aggiornamento: 2026-05-30
fonti:
  - raw/MODSIM_L4_Sociali.pdf
---

# Modelli di Dinamica delle Popolazioni

I modelli di dinamica delle popolazioni descrivono l'evoluzione nel tempo di una o più specie/gruppi, usando equazioni differenziali nonlineari. Rientrano nella categoria dei **sistemi sociali** e costituiscono la base per i modelli epidemiologici.

Tutti questi modelli sono **sistemi nonlineari a tempo continuo** della forma Ẋ = f(t,X,U); i punti di equilibrio si trovano risolvendo f(t,X,U) = 0 e la stabilità si studia linearizzando attorno ad essi (matrice Jacobiana).

## Punti chiave

- **Modello Malthusiano**: crescita esponenziale, ipotesi di risorse illimitate. È lineare.
- **Modello Logistico**: crescita con saturazione alla capacità portante K. Nonlineare.
- **Lotka-Volterra I**: modello preda-predatore con preda a dinamica malthusiana → oscillazioni cicliche.
- **Lotka-Volterra II**: variante con preda a dinamica logistica → stabilizzazione a lungo termine.
- Tutti i modelli condividono le ipotesi: popolazione isolata (no immigrazione/emigrazione) e individui omogenei.
- La stabilità locale di un punto di equilibrio si studia calcolando gli autovalori della Jacobiana J(x̄).

## Modello Malthusiano

**Autore:** Malthus (1798).

**Ipotesi:** popolazione isolata, individui omogenei, risorse illimitate.

**Equazione:**
- dN/dt = α·N(t),  N(0) = N₀
- **Soluzione analitica:** N(t) = N₀·e^(αt)

**Comportamento:** crescita esponenziale se α > 0, decrescita se α < 0, costante se α = 0. Realistico solo nel breve periodo o in ambienti con risorse abbondanti.

## Modello Logistico

**Ipotesi:** risorse **limitate** (capacità portante K).

**Equazione:**
- dN/dt = α·N − β·N² = α·N·(1 − N/K),  con K = α/β
- **Soluzione analitica:** N(t) = K·N₀·e^(αt) / (K + N₀·(e^(αt) − 1))

**Comportamento:** curva sigmoide — crescita iniziale quasi esponenziale, poi rallentamento e asintoto a K. Punti di equilibrio: N* = 0 (instabile) e N* = K (stabile).

## Modello Preda-Predatore (Lotka-Volterra I)

**Autori:** Lotka e Volterra (1920s).

**Ipotesi:**
- Preda x: da sola ha dinamica malthusiana (crescente, α > 0)
- Predatore y: da solo ha dinamica malthusiana decrescente (γ > 0)
- I predatori si nutrono esclusivamente delle prede

**Equazioni:**
- ẋ = α·x(t) − β·x(t)·y(t) = [α − β·y(t)]·x(t)
- ẏ = −γ·y(t) + δ·x(t)·y(t) = [δ·x(t) − γ]·y(t)

**Parametri:**
- β: probabilità di incontro × capacità predatoria (riduce prede)
- δ: probabilità di incontro × capacità predatoria × conversione di biomassa (aumenta predatori)

**Punti di equilibrio:**
- (x*, y*) = (0, 0): instabile (autovalori λ₁=α, λ₂=−γ, di segno opposto)
- (x*, y*) = (γ/δ, α/β): autovalori puramente immaginari λ₁,₂ = ±i√(αγ) → **orbite chiuse** (oscillazioni cicliche neutralmente stabili)

**Comportamento:** le due popolazioni oscillano in modo periodico sfasato — il picco dei predatori segue il picco delle prede.

## Modello Preda-Predatore (Lotka-Volterra II)

**Variante:** la preda ha dinamica **logistica** invece che malthusiana.

**Ipotesi:**
- Preda x: da sola ha dinamica logistica (con capacità portante)
- Predatore y: da solo ha dinamica malthusiana decrescente
- I predatori si nutrono esclusivamente delle prede

**Effetto:** il punto di equilibrio non triviale diventa asintoticamente stabile → le oscillazioni si smorzano nel tempo invece di essere periodiche.

## Linearizzazione e stabilità (ripasso)

Per un sistema nonlineare Ẋ = f(t,X,U), il sistema linearizzato attorno al punto di equilibrio x̄ si ottiene calcolando la **matrice Jacobiana**:

```
A = [∂fᵢ/∂xⱼ] valutata in x̄
B = [∂fᵢ/∂uⱼ] valutata in x̄
C = [∂gᵢ/∂xⱼ] valutata in x̄
D = [∂gᵢ/∂uⱼ] valutata in x̄
```

La stabilità del punto x̄ dipende dagli **autovalori di A**:
- Tutti con parte reale negativa → stabile
- Almeno uno con parte reale positiva → instabile
- Puramente immaginari → neutralmente stabile (caso Lotka-Volterra I)

## Articoli correlati

- [[introduzione-modellistica]]
- [[modelli-epidemiologici]]

## Fonti

- raw/MODSIM_L4_Sociali.pdf
