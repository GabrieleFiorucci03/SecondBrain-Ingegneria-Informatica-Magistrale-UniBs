---
tags: [digital-image-processing, frequenza, dft, fft, filtraggio]
data_creazione: 2026-06-04
data_aggiornamento: 2026-06-04
fonti:
  - raw/Image Filtering - part2 frequency implementation+specification_COMPILED.pdf
  - raw/freq_filter_template_match_COMPILED.pdf
---

# Filtraggio in Frequenza

Tramite la **2D-DFT** un'immagine si rappresenta nel dominio delle frequenze spaziali. Poiché la **convoluzione diventa un prodotto** in frequenza, il filtraggio LSI ([[filtraggio-spaziale]]) si può realizzare moltiplicando lo spettro dell'immagine per la risposta in frequenza del filtro, sfruttando la FFT per ridurre la complessità.

## Punti chiave
- **DSFT**: Ĩ(f,g) = Σ I[m,n]·exp(−j2π(fm+ng)), periodica; campionandola con passo (1/M, 1/N) si ottiene la **2D-DFT** Ĩ[k,l], periodica M×N.
- Proprietà fondamentale: O = I * h ⟷ Õ = Ĩ·H̃ (convoluzione ↔ prodotto). Il filtraggio LSI **rimuove o limita** un intervallo di frequenze spaziali.
- f,g (o k,l) sono le frequenze spaziali orizzontale/verticale; la DC (frequenza zero) sta agli angoli dello spettro.
- La 2D-DFT è **separabile**: M trasformate 1D sulle righe + N sulle colonne, ognuna via FFT con complessità O((L/2)·log₂L).
- Per una convoluzione corretta via DFT serve **zero padding** fino a Mo×No = (M+Mh−1)×(N+Nh−1), altrimenti si ha **convoluzione circolare** con errori ai bordi.

## Dalla DSFT alla 2D-DFT
- Si introducono i twiddle factor W_P = exp(j2π/P): Ĩ[k,l] = Σ I[m,n]·W_M^(−mk)·W_N^(−nl), con k∈[0,M−1], l∈[0,N−1].
- **Inversa**: I[m,n] = (1/MN)·Σ Ĩ[k,l]·W_M^(mk)·W_N^(nl).

## Calcolo e trucchi
- **Separabilità**: prima le DFT 1D di riga (I → Ĩ₁[m,l]), poi quelle di colonna (Ĩ₁ → Ĩ[k,l]); più veloce e abilita la FFT.
- **fftshift**: moltiplicare l'immagine per (−1)^(m+n) prima della trasformata sposta la DC al centro: F{(−1)^(m+n)·I[m,n]} = Ĩ[k−M/2, l−N/2]. Utile per la visualizzazione (lo spettro contiene numeri complessi → si visualizza il modulo).

## Filtraggio indiretto (in frequenza)
Schema: pre-processing → **Forward DFT** → moltiplicazione per la **filter function H̃[k,l]** → **Inverse DFT** → post-processing.
Procedura corretta per la convoluzione lineare:
1. Zero padding di I e h fino a Mo×No.
2. Calcolare Ĩ e H̃ (o usare overlap-add / overlap-save).
3. Moltiplicare Ĩ·H̃.
4. Inversa 2D-DFT e crop dei pixel non necessari.
Senza padding adeguato (P = A+C−1, Q = B+D−1) si producono errori di bordo dovuti alla replica periodica.

## Specifiche nel dominio della frequenza
- **Ideal low-pass filter**: H(u,v) = 1 entro un cerchio di raggio D₀, 0 fuori → sfoca.
- **Ideal high-pass filter**: complementare (cerchio nero al centro) → evidenzia bordi/dettagli.
- **Fan filter**: settori angolari → cattura i bordi con una data orientazione.

## Articoli correlati
- [[filtraggio-spaziale]]
- [[template-matching]]
- [[operatori-morfologici]]

## Fonti
- `raw/Image Filtering - part2 frequency implementation+specification_COMPILED.pdf` — Adami/Leonardi, "Frequency based filtering & Template matching" (UniBS).
- `raw/freq_filter_template_match_COMPILED.pdf` — Prof. N. Adami, "Frequency analysis, filters and template matching" (UniBS).
