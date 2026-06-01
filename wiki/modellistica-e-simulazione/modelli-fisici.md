---
tags: [modellistica, sistemi-fisici, circuiti-elettrici, sistemi-meccanici, state-space]
data_creazione: 2026-05-30
data_aggiornamento: 2026-05-30
fonti:
  - raw/MODSIM_L2_SistemiFisici.pdf
---

# Modelli Fisici

I **modelli fisici** sono descrizioni matematiche di fenomeni tipici della fisica classica. In ingegneria ci si concentra principalmente su **circuiti elettrici** e **sistemi meccanici**.

Tutti i modelli fisici sono sistemi a tempo continuo "nativamente": si ricavano le equazioni differenziali che descrivono l'evoluzione delle variabili fisiche di interesse, poi si scrivono in forma State-Space (Ẋ = f(t,X,U), Y = g(t,X,U)). La regola pratica per scegliere le variabili di stato è: **guardare le derivate** nelle equazioni fisiche.

## Punti chiave

- I sistemi fisici danno naturalmente EDO a tempo continuo → forma State-Space continua.
- Nei circuiti: **variabili di stato = tensioni sui condensatori + correnti negli induttori**.
- Nei sistemi meccanici: **variabili di stato = posizioni + velocità** (a volte accelerazioni).
- Le nonlinearità nascono da componenti nonlineari (es. molla nonlineare F = −k₁s − k₂s³) o da fenomeni nonlineari (attrito coulombiano, pendolo inverso).
- Per gestire la nonlinearità si linearizza attorno a un punto di equilibrio (→ [[introduzione-modellistica]]).

## Circuiti Elettrici

**Componenti principali e leggi:**

| Componente | Equazione |
|---|---|
| Resistore | v(t) = R·i(t) |
| Condensatore | i(t) = C·dv(t)/dt |
| Induttore | v(t) = L·di(t)/dt |
| Gen. tensione | v(t) = Vg(t) |
| Gen. corrente | i(t) = Ig(t) |

**Scelta variabili:**
- **Variabili di stato:** tensione ai capi dei condensatori, corrente negli induttori
- **Ingressi:** tensione/corrente in ingresso al circuito (da generatori)
- **Uscite:** tensioni/correnti di interesse (misurabili)

**Esempio 1** (circuito con C, R, L in parallelo, ingresso Ig):
- x₁ = tensione ai capi C, x₂ = corrente in L, u = Ig
- ẋ₁ = −(1/RC)x₁ − x₂ + u → A = [[-1/RC, -1/C], [1/L, 0]], B = [1/C, 0]ᵀ

**Esempio 2** (circuito con R, L, 2×C, ingressi V1, V2):
- x₁, x₂ = tensioni sui condensatori, x₃ = corrente nell'induttore
- Sistema 3×2 (3 stati, 2 ingressi)

**N.B.:** scegliere tensioni di due condensatori collegati può portare a più variabili di stato del necessario (ridondanza algebrica).

## Sistemi Meccanici

**Componenti principali:**

| Componente | Legge (traslazione) |
|---|---|
| Massa M | F = M·ṡ = M·s̈ |
| Molla (costante kl) | F = −kl·Δs |
| Smorzatore (costante ql) | F = −ql·Δv = −ql·ṡ |

Analogie angolari: massa angolare J (C = J·θ̈), molla angolare kω, smorzatore angolare qω.

**Scelta variabili:**
- **Variabili di stato:** posizioni e velocità (→ le derivate delle posizioni danno le velocità, le equazioni di Newton danno le derivate delle velocità)
- **Ingressi:** forze applicate
- **Uscite:** posizioni/velocità misurabili

**Esempio 4** (massa M con molla kl e smorzatore ql, ingresso forza F):
- x₁ = posizione, x₂ = velocità, u = F
- ẋ₁ = x₂
- ẋ₂ = −(kl/M)x₁ − (ql/M)x₂ + (1/M)u
- A = [[0, 1], [-kl/M, -ql/M]], B = [0, 1/M]ᵀ

**Esempio 5 — Sospensione meccanica** (automobile, 4 stati):
- M₁ = massa ruota, M₂ = massa vettura (su una ruota)
- Pneumatico ≈ molla lineare kl1; sospensione = molla kl2 + smorzatore ql2
- 4 variabili di stato: x₁ = s₁, x₂ = ṡ₁, x₃ = s₂, x₄ = ṡ₂
- Ingresso: profilo stradale u₁

## Nonlinearità nei sistemi fisici

Le nonlinearità possono essere dovute a:
1. **Componenti nonlineari** (es. molla con caratteristica F = −kl1·s − kl2·s³)
2. **Fenomeni fisici nonlineari** (attrito coulombiano, pendolo inverso)

In questi casi il modello non è più LTI ma ha la forma generale Ẋ = f(t,X,U). Si ricorre alla **linearizzazione attorno al punto di equilibrio** per l'analisi di stabilità locale.

## Articoli correlati

- [[introduzione-modellistica]]
- [[sistemi-nonlineari-e-identificazione]]

## Fonti

- raw/MODSIM_L2_SistemiFisici.pdf
