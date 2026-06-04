---
tags: [digital-image-processing, quantizzazione, compressione, clustering, lbg]
data_creazione: 2026-06-04
data_aggiornamento: 2026-06-04
fonti:
  - raw/08-Vector-Quantization_COMPILED.pdf
---

# Quantizzazione delle Immagini

La **quantizzazione** riduce il numero di colori/livelli di un'immagine discretizzando i valori di intensità. È un processo non lineare e non invertibile, usato per compressione, indicizzazione, analisi e semplificazione (preprocessing). Si distingue in **scalare** (su singolo canale) e **vettoriale** (su triplette RGB o blocchi).

## Punti chiave
- Quantizzazione Q: ℝ → D = {0,...,L−1}; tutte le immagini digitali sono già quantizzate (discrete in tempo e ampiezza).
- **Scalare**: input = valore scalare (Y, R, G...). **Vettoriale (VQ)**: input = tripletta (R,G,B) o blocco di pixel.
- **Errore di quantizzazione**: E = I − Q(I); MSE = Σ(l−Q(l))²·h_I(l), pesato per l'istogramma ([[equalizzazione-istogramma]]).
- **Quantizzazione ottima**: livelli vicini dove l'istogramma è alto (alta probabilità di grandi errori), livelli distanti dove è basso.
- **Lloyd-Max** (scalare/separabile) e **LBG** (vettoriale, regioni di Voronoi) sono gli algoritmi iterativi di design del quantizzatore.

## Definizione del quantizzatore
Data l'immagine I[m,n] ∈ D = {d₀,...,d_{L−1}}, un insieme di P intervalli semiaperti B = {b₀,...,b_{P−1}} con bk = [dk, dk+∆bk) e livelli di riproduzione vk ∈ R, il **quantizzatore a P livelli** è Q(l) = {vk | l ∈ bk}. Se ∆bk = costante → **quantizzazione uniforme**.

## Lloyd-Max (quantizzazione ottima scalare)
1. Partizionare lo spazio di input in P regioni non sovrapposte (confini bk); t = 1.
2. Calcolare i centroidi Vk (media dei dati di ogni regione) e l'MSE(1).
3. Nuovi confini bk = round((V_{k−1} + Vk)/2).
4. t = t+1; ricalcolare i centroidi e l'MSE(t).
5. Se MSE(t) << MSE(t−1) tornare al passo 3, altrimenti stop.

Applicabile ai vettori (R,G,B) solo **separabilmente** (LM per ogni componente) → regioni rettangolari, non la vera VQ.

## LBG (Linde-Buzo-Gray, VQ vera)
La differenza rispetto a Lloyd-Max è la **formazione dei centroidi**: ogni vettore di input è associato iterativamente al centroide più vicino (regioni di **Voronoi** in 2D). Inizializzazione dei centroidi:
- generazione casuale di P centroidi, oppure
- **binary split**: si parte da un centroide (media di tutti i vettori) e lo si raddoppia iterativamente.

**Variante split**: V₁ = media di tutti i pixel (triplette); per ogni livello si splitta ogni centroide in Vk±εk (εk piccolo vettore casuale), si assegna ogni dato alla regione del centroide più vicino (argmin‖I−Vk,m‖), si ricalcolano i centroidi come media della regione e l'MSE; si raddoppia P fino a P_max.

## VQ e segmentazione: SLIC Superpixel
Quantizzare = riassegnare i colori al loro centroide. Se nell'associazione al centroide si considera **anche la posizione** dei pixel (oltre al colore), si ottengono i **SLIC superpixel**: regioni compatte usate per la segmentazione.

## Articoli correlati
- [[equalizzazione-istogramma]]
- [[colore-e-spazi-colore]]
- [[operatori-morfologici]]

## Fonti
- `raw/08-Vector-Quantization_COMPILED.pdf` — Adami/Leonardi, "Image Vector Quantization" (UniBS). Rif.: Linde, Buzo, Gray, "An Algorithm for Vector Quantizer Design", IEEE Trans. Comm. 28 (1980).
