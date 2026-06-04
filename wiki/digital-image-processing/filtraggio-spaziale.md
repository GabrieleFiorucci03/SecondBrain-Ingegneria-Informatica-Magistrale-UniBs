---
tags: [digital-image-processing, filtraggio, convoluzione, kernel, lti]
data_creazione: 2026-06-04
data_aggiornamento: 2026-06-04
fonti:
  - raw/03-Image-filtering spatial domain_COMPILED.pdf
  - raw/Image Filtering - part1 spatial implementation_COMPILED.pdf
---

# Filtraggio Spaziale

Un **filtro spaziale** assegna a ogni pixel di output un valore funzione dei pixel nel suo intorno (neighborhood) nell'immagine di input. I filtri **lineari e shift-invarianti (LSI)** si riducono a una **convoluzione** con la risposta impulsiva (kernel). È la base di enhancement, edge detection e delle CNN.

## Punti chiave
- **Filtro lineare**: S{α₁I₁ + α₂I₂} = α₁S{I₁} + α₂S{I₂}. **Shift-invariante**: S{I[m−m₀,n−n₀]} = O[m−m₀,n−n₀].
- Lineare + shift-invariante ⇒ **convoluzione**: O[m,n] = I[m,n] * h[m,n], con h = S{δ} = **risposta impulsiva** (kernel H).
- Output esteso: se I è M×N e h è Mh×Nh, O ha dimensione (M+Mh−1)×(N+Nh−1); per la visualizzazione si croppa a M×N.
- La convoluzione è **associativa** (filtri in cascata combinabili), **distributiva** sull'addizione; identità = δ, shift = δ traslato.
- Complessità diretta O(MN·MhNh); con **kernel separabili** h[m,n]=ha[m]·hb[n] scende a O(MN·(Mh+Nh)); per kernel grandi (MhNh ≈ 50) conviene la [[filtraggio-in-frequenza|FFT]].

## Risposta di un filtro LSI
- L'immagine è una somma di impulsi pesati: I[m,n] = Σ I[m',n']·δ[m−m', n−n'] = I * δ.
- Per un filtro lineare: S{I} = Σ I[m',n']·S{δ[m−m',n−n']}.
- Se anche shift-invariante: S{I} = Σ I[m',n']·h[m−m', n−n'] = convoluzione con h.
- Calcolo **"in-place"**: kernel a supporto finito simmetrico attorno all'origine (hot spot) → nessuno shift tra input e output.

## Implementazione (kernel)
- H è la matrice/kernel del filtro; per la media 3×3: H = (1/9)·matrice di 1.
- Per kernel piccoli si applica direttamente la definizione; per kernel grandi si usano implementazioni indirette via FFT; la separabilità riduce ulteriormente il costo.

## Esempi di filtri lineari
- **Identità**: kernel con solo il centro = 1.
- **Shift spaziale**: kernel con 1 fuori centro → trasla l'immagine.
- **Mean (media locale)**: kernel uniforme 1/9; sfoca, riduce rumore. L'effetto aumenta con la dimensione (7×7, 15×15, 41×41).
- **Sharpen**: identità − media (es. (1/9)·[[−1,−1,−1],[−1,17,−1],[−1,−1,−1]]); i coefficienti sommano a 1 → media preservata.
- **Gradiente (Sobel)**: kernel Sx e Sy; modulo √(Ox²+Oy²) per edge detection.
- **Laplacian of Gaussian (LoG)**: smoothing gaussiano seguito da laplaciano ([[0,1,0],[1,−4,1],[0,1,0]]) per evidenziare i bordi.

## Gestione dei bordi (padding)
I dati sono noti solo entro l'immagine; per il bordo si **padda** l'esterno con:
- **Propagation** (replica prima/ultima riga/colonna);
- **Periodic replica** (replica periodica, surround continuo);
- **Mirroring** (riflessione);
- **estrapolazione gradient/edge-driven**.
Alternative all'output: crop, pad, extend, wrap.

## Filtri non lineari
- **Mediano**: assegna la mediana dei pixel nell'intorno (numero dispari). Molto efficace contro il rumore **salt & pepper** (pixel "bruciati" sparsi), a differenza del filtro gaussiano adatto al **rumore gaussiano** (valore additivo gaussiano).
- **Operatori morfologici** (dilation/erosion = max/min nell'intorno): vedi [[operatori-morfologici]].

## Passa-basso e passa-alto
- Kernel **low-pass**: media, gaussiano G(x,y) = A·exp(−...). Riducono il rumore.
- **High-pass image = original − low-pass image**.
- Interpretazione in frequenza: vedi [[filtraggio-in-frequenza]].

## Articoli correlati
- [[filtraggio-in-frequenza]]
- [[operatori-morfologici]]
- [[template-matching]]
- [[trasformazioni-geometriche-e-interpolazione]]

## Fonti
- `raw/03-Image-filtering spatial domain_COMPILED.pdf` — Prof. N. Adami, "Image Filtering" (UniBS).
- `raw/Image Filtering - part1 spatial implementation_COMPILED.pdf` — Adami/Leonardi, "2D Image Linear Filtering" (UniBS).
