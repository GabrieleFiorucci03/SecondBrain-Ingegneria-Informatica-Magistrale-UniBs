---
tags: [digital-image-processing, trasformazioni, interpolazione, geometria]
data_creazione: 2026-06-04
data_aggiornamento: 2026-06-04
fonti:
  - raw/spatial-linear-transform_COMPILED.pdf
---

# Trasformazioni Geometriche e Interpolazione

Le **trasformazioni geometriche** rimappano le coordinate spaziali dei pixel (rotazione, scaling, traslazione, warping). Poiché le coordinate trasformate sono in genere non intere, è necessaria l'**interpolazione** per stimare i valori dei pixel.

## Punti chiave
- Una trasformazione lineare 2D è T(x) = A·x con A matrice [2×2] (rotazione, scaling, riflessione, warping).
- La **traslazione** non è lineare in 2D: richiede coordinate omogenee (matrice affine [3×3]).
- Per riempire l'immagine trasformata si usa l'**inverse mapping**: per ogni pixel di output si cerca da dove proviene nell'input (invertendo A); il forward mapping lascerebbe buchi.
- L'inverse mapping produce coordinate non intere → serve interpolazione.
- **Nearest neighbour** (pixel più vicino) vs **bilinear** (combinazione pesata dei 4 vicini): la bilineare è più liscia.

## Trasformazioni lineari
- **Scaling**: matrice diagonale diag(Sx, Sy); es. [[2,0],[0,3/2]] ingrandisce in x di 2 e in y di 1.5.
- **Rotation**: [[cosφ, −sinφ],[sinφ, cosφ]] ruota di φ attorno all'origine.
- **Reflection / warping**: altre matrici [2×2].
- Dimensioni: size(A)=[2×2], size(x)=[2×1].

## Traslazione e coordinate omogenee
La traslazione x' = x+∆x, y' = y+∆y non è esprimibile come prodotto [2×2]. Si introducono le **coordinate omogenee**: il vettore [x,y,1]ᵀ moltiplicato per la matrice affine [[1,0,∆x],[0,1,∆y],[0,0,1]]. Questo unifica trasformazioni lineari e traslazione in un'unica **trasformazione affine**.

## Problema di mapping
1. **Ridefinizione dell'origine**: lo spazio matrice (righe/colonne, origine in alto a sinistra) differisce dallo spazio euclideo (assi x,y centrati).
2. **Mapping**:
   - **Forward mapping** (dove va ogni pixel originale): inutile, lascia buchi nell'output.
   - **Inverse mapping** (da dove proviene ogni pixel finale): si inverte la matrice di trasformazione (l'inversa della rotazione si ottiene con −φ). È l'approccio corretto.

## Interpolazione
Quando l'inverse mapping dà coordinate non intere (es. [3.3, 5.8]):
- **Nearest neighbour**: si sceglie il pixel noto più vicino. Veloce ma a blocchi.
- **Bilinear interpolation**: considera i 4 vicini P1..P4. Con frazioni a (orizzontale) e b (verticale):
  - R1 = a·P2 + (1−a)·P1
  - R2 = a·P4 + (1−a)·P3
  - R = b·R2 + (1−b)·R1

## Oltre il lineare
**Warping** (trasformazione non lineare) e **morphing** estendono queste idee a deformazioni arbitrarie e transizioni tra immagini.

## Articoli correlati
- [[filtraggio-spaziale]]
- [[equalizzazione-istogramma]]
- [[demosaicing]]

## Fonti
- `raw/spatial-linear-transform_COMPILED.pdf` — Prof. N. Adami, Digital Image Processing, "Transformations and Interpolation" (UniBS).
