---
tags: [identificazione-sistemi, PEM, ARX, OE, convergenza, VC-theory, apprendimento]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/[Campi]_sys-id-and-limits-learning_COMPILED.pdf
---

# Identificazione di Sistemi e Limiti dell'Apprendimento

Note del corso di M.C. Campi (UniBS, 2006): "System Identification and the Limits of Learning from Data". Studia la convergenza asintotica dei metodi PEM (Prediction Error Minimization) e i limiti teorici di ciò che si può imparare dai dati. Fondamento teorico per capire quando e perché un metodo data-driven converge, e quando fallisce.

## Punti chiave

- **PEM (Prediction Error Minimization)**: θ̂_t = argmin_{θ∈Θ} (1/t) Σ [yₖ − ŷₖ(θ)]² — minimizza l'errore di predizione empirico.
- **Convergenza asintotica**: sotto stazionarietà e stabilità, θ̂_t → Θ* q.c., dove Θ* minimizza il costo atteso J̄(θ).
- **OE vs ARX**: il predittore Output Error (OE) converge al parametro vero θ°; il predittore ARX può essere biased se il sistema non è ARX.
- **Lemma fondamentale**: convergenza uniforme di 𝒥_t(θ) → J̄(θ) implica convergenza del minimizzatore.
- **Predittori nonlineari**: la convergenza uniforme richiede la teoria di Vapnik-Chervonenkis (VC) — non basta la LGN puntuale.
- **Teorema di Glivenko-Cantelli**: la CDF empirica converge uniformemente alla vera CDF — base per la classificazione.

## Capitolo 1: Convergenza asintotica dei metodi PEM

### Setup

Sistema vero: yₜ = θ° uₜ + wₜ (semplice esempio lineare introduttivo)

Stima PEM: θ̂_t = argmin_{θ∈[0,1]} (1/t) Σₖ [yₖ − ŷₖ(θ)]² dove ŷₖ(θ) = θuₖ

**Domanda fondamentale**: θ̂_t → θ° (il parametro vero) quando t → ∞?

### Approccio via LGN

Il minimizzatore dell'empirico tende al minimizzatore del costo atteso:
```
𝒥_t(θ) = (1/t) Σ [yₖ − ŷₖ(θ)]²   →   J̄(θ) = E[(yₜ − ŷₜ(θ))²]
```

La differenza 𝒥_t(θ) − J̄(θ) → 0 quasi certamente (per la LGN). Se la convergenza è **uniforme in θ**, allora θ̂_t → Θ* q.c.

### Lemma 1.1 (Lemma fondamentale di convergenza)

**Lemma**: Sia {𝒥_t(θ): Θ → ℝ} una famiglia di funzioni, θ̂_t = argmin_θ 𝒥_t(θ). Se:

```
sup_{θ∈Θ} |𝒥_t(θ) − J̄(θ)| → 0
```

allora:
- i) J̄(θ̂_t) → inf_{θ∈Θ} J̄(θ)  (il valore ottimo converge)
- ii) Se Θ ⊂ ℝᵖ è **compatto** e J̄(·) è **continua**, allora θ̂_t → Θ* = {θ ∈ Θ: J̄(θ) = inf J̄(θ)}

**Attenzione**: la convergenza deve essere **uniforme in θ**, non solo puntuale. La compattezza di Θ e la continuità di J̄ sono essenziali.

## Predittori lineari (Sezione 1.3)

### Classe di predittori

```
ŷ_t(θ) = W_u(z⁻¹, θ) u_t + W_y(z⁻¹, θ) y_t
```

con W_u, W_y funzioni di trasferimento razionali con coefficienti continui in θ, asintoticamente stabili ∀θ∈Θ (Ipotesi 1.4).

### Mecanismo di generazione dei dati (Ipotesi 1.5)

```
u_t = G_u(z⁻¹) w_t
y_t = G_y(z⁻¹) w_t
```

con G_u, G_y razionali stabili e w processo indipendente a varianza costante finita al 4° momento.

### Teorema 1.6 (Convergenza dei metodi PEM)

Sotto le Ipotesi 1.4 e 1.5:
- i) J̄(θ̂_t) → inf_{θ∈Θ} J̄(θ),  quasi certamente
- ii) θ̂_t → Θ*,  quasi certamente (in senso dist(θ̂_t, Θ*) → 0)

**Prova**: Si mostra che 𝒥_t(θ) → J̄(θ) uniformemente q.c. grazie a:
1. **Stabilità**: garantisce che campioni lontani nel tempo siano poco correlati → LGN applicabile
2. **Linearità**: garantisce convergenza uniforme (non solo puntuale)

### OE vs ARX: bias potenziale

**Caso OE** (Output Error): ŷ_t(θ) = bz⁻¹/(1 + az⁻¹) u_t
- Il predittore è associato al modello vero → θ* = θ° (nessun bias)

**Caso ARX**: ŷ_t(θ) = ay_{t-1} + bu_{t-1}
- Vantaggio: minimizzazione quadratica in θ → soluzione in forma chiusa
- Svantaggio: se il vero sistema non è ARX (o ha rumore non bianco), il limite θ* può differire dal parametro vero θ°
- Il bias è deterministico e caratterizzato dalla teoria asintotica (non visibile in un singolo esperimento, ma previsto teoricamente)

**Esempio**: sistema con a° = −0.5, b° = 1
- OE: θ̂_t converge a (−0.5, 1) — il valore vero
- ARX: θ̂_t converge a (−0.28, ...) — valore biasato

## Predittori nonlineari (Sezione 1.4)

Per predittori nonlineari, la convergenza uniforme di 𝒥_t(θ) non è garantita automaticamente. La LGN si applica punto per punto ma non uniformemente.

### Problema di non-uniformità

Se ŷ_t(θ) è nonlineare in θ, 𝒥_t(θ) può convergere a J̄(θ) puntualmente ma non uniformemente → il Lemma 1.1 non si applica → θ̂_t potrebbe non convergere.

### Teoria di Vapnik-Chervonenkis (VC)

La teoria VC fornisce condizioni per la convergenza uniforme in un contesto di classificazione (output binario):

**Setting**: sistema con output y_t ∈ {0,1}, predittore ŷ_t(θ) ∈ {0,1} parametrizzato da θ.

**Dimensione VC (d_VC)**: misura la "capacità" della classe di predittori. Una classe con d_VC finita ha convergenza uniforme.

**Risultato chiave**: se d_VC < ∞, allora:
```
sup_{θ∈Θ} |𝒥_t(θ) − J̄(θ)| → 0  quasi certamente
```

Questo garantisce convergenza del minimizzatore per predittori nonlineari nella classificazione.

### Teorema di Glivenko-Cantelli

**Teorema 1.10**: Per una sequenza i.i.d. {v_t} con CDF F, la CDF empirica F̂_t(x) = (1/t) Σ 1(vₖ ≤ x) converge uniformemente:

```
sup_{x∈ℝ} |F̂_t(x) − F(x)| → 0,  quasi certamente
```

Questo è il fondamento della convergenza dei classificatori basati su intervalli (Esempio 1.8 del corso).

## Limiti dell'apprendimento dai dati

Il titolo del corso allude a **limiti fondamentali**: anche con infiniti dati, non tutto è apprendibile.

- **Bias del modello**: se la classe di predittori non contiene il vero sistema, il limite θ* è il "miglior approssimante" — non il vero parametro.
- **Non identificabilità**: parametri diversi possono produrre la stessa predizione ottima (Θ* non singleton).
- **Curse of dimensionality**: per predittori nonlineari ad alta dimensione, d_VC può essere troppo grande e la convergenza troppo lenta per dataset finiti.
- **Dipendenza temporale**: la teoria presuppone stazionarietà (o quasi-stazionarietà); in ambienti non stazionari i risultati di convergenza possono cadere.

## Relazione con lo Scenario Approach

La teoria PEM e lo Scenario Approach affrontano problemi diversi:
- **PEM**: identifica un modello del sistema da dati temporali, con garanzie asintotiche (N → ∞)
- **Scenario Approach**: ottimizza una decisione data una distribuzione di scenari, con garanzie in campione finito

Lo Scenario Approach è l'approccio **diretto** (dati → decisione); PEM è il nucleo dell'approccio **indiretto** (dati → modello → decisione). Vedi [[introduzione]] per il confronto.

## Articoli correlati

- [[scenario-optimization]]
- [[probability-foundations]]
- [[introduzione]]
- [[noise-compensation]]

## Fonti

- raw/[Campi]_sys-id-and-limits-learning_COMPILED.pdf — Note del corso (Campi, UniBS, Sept. 2006)
