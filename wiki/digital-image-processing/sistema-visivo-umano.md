---
tags: [digital-image-processing, percezione, hvs, luminanza]
data_creazione: 2026-06-04
data_aggiornamento: 2026-06-04
fonti:
  - raw/01-HumanVisualSystem_COMPILED.pdf
---

# Sistema Visivo Umano (HVS)

Il **Human Visual System (HVS)** è composto da occhio (sensore/camera), nervo ottico (canale di trasmissione) e cervello (unità di elaborazione). Comprenderlo è essenziale per progettare algoritmi di elaborazione immagini efficienti e percettivamente coerenti: l'obiettivo del [[indice_wiki|digital image processing]] è far "percepire" e manipolare al calcolatore l'informazione visiva (pre-processing, enhancement, estrazione di informazione).

## Punti chiave
- L'occhio adatta la sensibilità su ~10 ordini di grandezza di luminosità, ma ne discrimina solo ~6 contemporaneamente (**brightness adaptation**).
- La **luminosità percepita** è funzione logaritmica dell'intensità luminosa: la percezione non è una misura fisica.
- Due tipi di fotorecettori: **coni** (6-7 milioni, fovea, visione fotopica e colore, dettaglio) e **bastoncelli** (75-150 milioni, periferia, visione scotopica, no colore, movimento).
- La **Weber ratio** ∆Ic/I misura la discriminabilità del contrasto: valore piccolo = buona discriminazione.
- Il **simultaneous contrast** mostra che la luminosità percepita dipende dal contesto/posizione relativa.

## Anatomia dell'occhio
- **Cornea**: membrana esterna trasparente, superficie anteriore.
- **Sclera**: membrana esterna opaca, copre il resto del globo.
- **Coroide (choroid)**: vasi sanguigni, nutrizione.
- **Pupilla**: apertura centrale dell'iride, regola la luce entrante (diametro 2-8 mm).
- **Cristallino (lens)**: strati concentrici di cellule fibrose, 60-70% acqua, mette a fuoco l'immagine.
- **Retina**: strato più interno, "sensore" su cui si forma l'immagine; contiene i fotorecettori.

## Fotorecettori retinici
- **Coni**: nella porzione centrale (fovea), responsabili della visione fotopica (luce intensa) e del colore, risolvono dettagli fini.
- **Bastoncelli (rods)**: distribuiti su tutta la retina, visione scotopica (luce debole), non sensibili al colore, danno il quadro generale.
- **Fovea**: depressione circolare al centro della retina (~1.5 mm), densa di coni; responsabile della visione spaziale (immagini statiche). La periferia rileva il movimento.
- **Blind spot**: punto dove emerge il nervo ottico, privo di fotorecettori.
- La densità dei recettori varia con l'eccentricità: i coni sono concentrati alla fovea (eccentricità 0°), i bastoncelli formano due picchi laterali.

## Adattamento e discriminazione della luminosità
- **Brightness adaptation**: a una data sensibilità (livello di adattamento Ba) l'occhio percepisce solo un piccolo intervallo di intensità; sotto Bb tutto appare nero.
- **Dynamic range**: capacità di discriminare cambiamenti di luminosità. Misurata tramite l'incremento ∆Ic discriminabile su un fondo di intensità I.
- **Weber ratio** = ∆Ic/I. Piccola → buona discriminazione (basta una piccola variazione %); grande → cattiva discriminazione.

## Fenomeni percettivi
- **Simultaneous contrast**: la stessa intensità appare diversa a seconda dello sfondo (più chiara su sfondo scuro e viceversa) — "everything is affected by its opposite".
- **Contrast Sensitivity Function (CSF)**: la sensibilità al contrasto dipende dalla frequenza spaziale del pattern.

## Modello di formazione dell'immagine
L'occhio è modellabile come una **camera pinhole**: i raggi dalla scena attraversano un punto (cristallino) e formano un'immagine invertita sul piano immagine (retina). Esempio: un albero di 15 m a 100 m proietta un'immagine sulla retina a ~17 mm dal centro ottico.

## Articoli correlati
- [[colore-e-spazi-colore]]
- [[equalizzazione-istogramma]]
- [[quantizzazione-immagini]]

## Fonti
- `raw/01-HumanVisualSystem_COMPILED.pdf` — Prof. N. Adami, Digital Image Processing, "Human Visual System" (UniBS).
