---
tags: [digital-image-processing, morfologia, erosione, dilatazione, segmentazione]
data_creazione: 2026-06-04
data_aggiornamento: 2026-06-04
fonti:
  - raw/Morphological Operators_COMPILED.pdf
  - raw/Image Filtering - part1 spatial implementation_COMPILED.pdf
---

# Operatori Morfologici

La **morfologia matematica** analizza ed elabora le strutture geometriche di un'immagine basandosi sulla **teoria degli insiemi**. Confrontando ogni pixel con i suoi vicini tramite un **structuring element**, evidenzia o modifica forme specifiche. Nata per immagini binarie, si estende al grayscale tramite min(·)/max(·).

## Punti chiave
- Servono due ingredienti: un'**immagine binaria** e un **structuring element (kernel)**, maschera binaria che definisce l'intorno (es. N4, N8).
- **Erosione** (I ⊖ H): mantiene il pixel solo se H è interamente contenuto nel foreground → restringe il foreground.
- **Dilatazione** (I ⊕ H): unione delle copie di H centrate sui pixel del foreground → espande il foreground.
- **Opening** = erosione poi dilatazione (rimuove outlier/regioni più piccole di H); **Closing** = dilatazione poi erosione (riempie buchi più piccoli di H).
- Dualità: l'erosione si può calcolare come dilatazione del background. La dilatazione è commutativa/associativa, l'erosione no.

## Immagine binaria come insieme
- Foreground: Q_I = {(u,v) | I(u,v) = 1}. Background: Q̄_I = {(u,v) | I(u,v) = 0}.
- S_I = {M×N} è l'insieme completo delle coordinate. Notazione semplificata: p = (u,v), Q_I = {p | I(p) = 1}.
- **Operazioni su insiemi**: complemento (NOT puntuale), unione (OR), intersezione (AND), traslazione Q_Id = {p+d}, riflessione Q_Î = {−p}.

## Operatori fondamentali
- **Erosione**: I ⊖ H = {p ∈ S_I | (p+q) ∈ I, ∀q ∈ H}. Tiene solo i pixel p dove H si sovrappone completamente a I. (Con N4: tutti i vicini devono essere 1.)
- **Dilatazione**: I ⊕ H = {(p+q) | p ∈ I, q ∈ H}. Unione delle copie di H centrate su ogni pixel. (Con N4: basta un vicino a 1.)
- **Opening**: I ∘ H = (I ⊖ H) ⊕ H → rimuove regioni più piccole di H (outlier nel background).
- **Closing**: I • H = (I ⊕ H) ⊖ H → riempie i buchi del foreground più piccoli di H.

## Proprietà e applicazioni
- **Dualità**: erosione ↔ dilatazione del background (e viceversa).
- Dilatazione: commutativa e associativa; erosione: né l'una né l'altra.
- **Contour**: il contorno chiuso di un oggetto binario si estrae con dilatazione seguita da sottrazione (XOR).
- **Skeleton**: si ottiene iterando l'erosione finché resta una linea di un pixel.
- Esempio applicativo: conteggio di globuli rossi (rimozione delle piccole aree di rumore dopo segmentazione).
- Versione grayscale: dilation/erosion = max/min del valore nell'intorno sommato al kernel (vedi [[filtraggio-spaziale]]).

## Articoli correlati
- [[filtraggio-spaziale]]
- [[quantizzazione-immagini]]

## Fonti
- `raw/Morphological Operators_COMPILED.pdf` — Image Processing and Visualization, "Morphological Operators" (UniBS).
- `raw/Image Filtering - part1 spatial implementation_COMPILED.pdf` — Adami/Leonardi (famiglie di filtri non lineari).
