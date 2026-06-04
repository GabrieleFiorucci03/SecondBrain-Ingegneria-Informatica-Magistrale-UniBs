---
tags: [digital-image-processing, template-matching, correlazione, frequenza]
data_creazione: 2026-06-04
data_aggiornamento: 2026-06-04
fonti:
  - raw/freq_filter_template_match_COMPILED.pdf
---

# Template Matching

Il **template matching** localizza un pattern (template) all'interno di un'immagine cercando la posizione di massima somiglianza. Si basa sulla **correlazione** (non sulla convoluzione) e, come il [[filtraggio-in-frequenza|filtraggio]], si può calcolare efficientemente nel dominio della frequenza.

## Punti chiave
- Si usa la **correlazione**, non la convoluzione: il template non viene "filtrato" ma cercato nell'immagine (non si ribalta il kernel).
- Proprietà in frequenza: f(x,y) ⊗ h(x,y) ⟷ F*(u,v)·H(u,v) (coniugato complesso di F), dopo padding come per la convoluzione.
- Il picco della correlazione individua la posizione di miglior corrispondenza (highest correlation value).
- Realizzabile sia nel dominio spaziale sia in quello della frequenza.

## Funzionamento
- Dato un template (es. la lettera "T") e un'immagine contenente più simboli ("UTK"), si calcola la correlazione tra template e immagine.
- Il **profilo di grey-level** della correlazione presenta un picco (highest correlation value) in corrispondenza della posizione del template nell'immagine.
- La differenza chiave rispetto al filtraggio: nella correlazione si usa F*(u,v) (coniugato), mentre nella convoluzione si moltiplicano direttamente le trasformate.

## Articoli correlati
- [[filtraggio-in-frequenza]]
- [[filtraggio-spaziale]]

## Fonti
- `raw/freq_filter_template_match_COMPILED.pdf` — Prof. N. Adami, "Frequency analysis, filters and template matching" (UniBS).
