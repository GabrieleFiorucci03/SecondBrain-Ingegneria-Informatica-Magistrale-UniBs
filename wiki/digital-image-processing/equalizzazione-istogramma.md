---
tags: [digital-image-processing, istogramma, enhancement, contrasto, statistica]
data_creazione: 2026-06-04
data_aggiornamento: 2026-06-04
fonti:
  - raw/histogram_equalization_COMPILED.pdf
---

# Equalizzazione dell'Istogramma

L'**istogramma** di un'immagine stima la distribuzione di probabilità delle intensità dei pixel. L'**equalizzazione dell'istogramma** è una point transformation per il contrast enhancement che redistribuisce le intensità verso una distribuzione uniforme, migliorando il contrasto.

## Punti chiave
- L'istogramma normalizzato h[k] = nk/(M·N) è una stima della distribuzione di probabilità; vale Σh[k] = 1.
- La **distribuzione cumulativa** F[k] = Σᵢ₌₀ᵏ h[i] = P(X ≤ k); ha dinamica [0,1].
- Un'immagine a basso contrasto ha istogramma stretto; una a buon contrasto ha istogramma vicino all'uniforme U[0, L−1].
- L'**equalizzazione** applica come trasformazione di punto la cumulativa stessa: Ieq[i,j] = F(I[i,j]), poi riscala da [0,1] a [0, L−1].
- La frequenza relativa è uno stimatore della probabilità (esempio del dado a 6 facce → ~1/6 ciascuna).

## Probabilità: densità e distribuzione
- La **frequenza relativa** (conteggio/totale prove) stima la **distribuzione di probabilità** P; Σ P[k] = 1.
- Per variabili continue su [A,B] si usa la **densità di probabilità**: uniforme U[A,B] (tutti i valori equiprobabili) o gaussiana G(μ,σ²).
- Differenza densità/distribuzione: per una V.A. continua P(X = valore puntuale) = 0; la distribuzione cumulativa integra la densità.
- La **cumulativa** discreta F[k] = Σᵢ₌₀ᵏ h[i] (per le continue l'integrale sostituisce la somma).

## Istogramma di un'immagine
- h[k] = nk/(M·N), con k ∈ [0, L−1], L = numero di livelli, nk = pixel con intensità k, M·N = totale pixel.
- Usi statistici dell'istogramma: **compressione** (source coding), **quantizzazione** ([[quantizzazione-immagini]]), **enhancement** (equalizzazione).

## Algoritmo di equalizzazione
1. Calcolare l'istogramma dell'immagine (distribuzione di probabilità).
2. Calcolare la distribuzione cumulativa F[k].
3. Applicare la point transformation Ieq[i,j] = T(I[i,j]) = F(I[i,j]).
4. Riscalare Ieq da [0,1] a [0, L−1].

Il risultato avvicina l'istogramma alla distribuzione uniforme. L'estensione a immagini a colori richiede di operare per canale o sulla sola luminanza (vedi [[colore-e-spazi-colore]]).

## Articoli correlati
- [[quantizzazione-immagini]]
- [[sistema-visivo-umano]]
- [[trasformazioni-geometriche-e-interpolazione]]

## Fonti
- `raw/histogram_equalization_COMPILED.pdf` — Prof. N. Adami, Digital Image Processing, "Image Contrast Enhancement: Histogram Equalization" (UniBS).
