---
tags: [digital-image-processing, demosaicing, bayer, interpolazione, colore]
data_creazione: 2026-06-04
data_aggiornamento: 2026-06-04
fonti:
  - raw/Demosaicing_ICASSP04_COMPILED.pdf
---

# Demosaicing

Il **demosaicing** è la ricostruzione di un'immagine RGB completa a partire dai dati catturati da un singolo sensore CCD con **Color Filter Array (CFA)** in pattern di **Bayer**. Per ogni pixel, che registra un solo canale, occorre interpolare i due colori mancanti.

## Punti chiave
- Nel **Bayer pattern**, ogni blocco 2×2 ha 2 pixel verdi (diagonali), 1 rosso e 1 blu: il verde è campionato al doppio della frequenza di R e B perché porta la maggior parte della luminanza per l'HVS ([[sistema-visivo-umano]]).
- L'approccio più semplice è l'**interpolazione bilineare** indipendente per canale, ma genera artefatti su bordi e alte frequenze (ignora la correlazione tra R, G, B).
- I metodi migliori sfruttano la correlazione tra canali (constant-hue, gradient-based, iterativi) assumendo cromaticità lentamente variabile.
- Il metodo **Malvar-He-Cutler** è un'interpolazione lineare **gradient-corrected**: corregge la bilineare con un termine di gradiente del colore noto, controllato da guadagni {α, β, γ}.
- I guadagni ottimi (in senso **Wiener**) approssimati: α = 1/2, β = 5/8, γ = 3/4; filtri FIR su finestra 5×5, quasi ottimi.

## Dal Bayer all'RGB
- **Bilineare per il verde**: a un pixel R/B, ĝ(i,j) = media dei 4 verdi a croce.
- Per R e B su pixel verde: media di 2 vicini (croce diagonale).
- La bilineare garantisce range dinamico preservato (no overflow) ma artefatti sui bordi.

## Oltre la bilineare
- **Constant-hue (Freeman)**: G bilineare, poi R e B interpolati mantenendo costanti i rapporti R/G e B/G; migliorabile con filtri mediani sulle differenze R−G, B−G.
- **Gradient-based (Laroche-Prescott, Hamilton-Adams, Chang et al.)**: stimano la direzione dei bordi e interpolano preferenzialmente lungo di essi (Hamilton-Adams usa differenze di primo e secondo ordine).
- **Iterativi (Kimmel, proiezioni alternate Gunturk)**: usano R/B per correggere G e viceversa; ottime prestazioni ma alta complessità (fino a 480 operazioni/pixel).

## Metodo Malvar-He-Cutler (gradient-corrected linear)
- Criterio: i bordi hanno componente di **luminanza** molto più forte della cromaticità. Il valore noto al pixel non va scartato.
- Per G a un pixel R: ĝ(i,j) = ĝ_B(i,j) + α·∆_R(i,j), dove ĝ_B è la stima bilineare e ∆_R è il gradiente di R (differenza tra R osservato e sua stima bilineare su finestra 5 punti).
- Analogamente R su pixel verde (β, regione 9 punti) e R su pixel blu (γ, regione 5 punti); simmetrico per B.
- Guadagni Wiener approssimati a multipli interi di potenze di 1/2: α=1/2, β=5/8, γ=3/4.
- **Prestazioni**: +5.5 dB di PSNR sulla bilineare, +0.7 dB su R/B rispetto a Pei-Tam, superando molti metodi non lineari senza i loro artefatti e con minore complessità (9 coeff. per G, 11 per R e B su finestra 5×5).

## Articoli correlati
- [[colore-e-spazi-colore]]
- [[trasformazioni-geometriche-e-interpolazione]]
- [[filtraggio-spaziale]]
- [[sistema-visivo-umano]]

## Fonti
- `raw/Demosaicing_ICASSP04_COMPILED.pdf` — H. S. Malvar, L.-w. He, R. Cutler, "High-Quality Linear Interpolation for Demosaicing of Bayer-Patterned Color Images", Microsoft Research, ICASSP 2004.
