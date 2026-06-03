---
tags: [data-science, ottimizzazione, data-driven, certificati, decisioni]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/introduction_COMPILED.pdf
---

# Introduzione a Data Science for Reliable Decision Making

Il corso affronta il problema di prendere decisioni ottimali a partire da dati, in presenza di incertezza, con garanzie certificate sulla qualità della soluzione. Il framework centrale è lo [[scenario-optimization]], sviluppato da Marco C. Campi e collaboratori.

## Punti chiave

- **Problema generale**: dati campioni δ₁,…,δ_N da un mondo reale incerto, trovare una decisione ν che minimizzi una perdita attesa ℓ(ν, δ).
- **Dilemma 1 — Indiretto vs diretto**: l'approccio indiretto stima prima un modello del mondo (es. distribuzione dei ritorni), poi ottimizza su quel modello; l'approccio diretto va dai dati alla decisione senza stimare il modello.
- **Dilemma 2 — Certificati**: l'approccio data-driven deve produrre *garanzie di qualità* (certificates) sulla decisione, non solo una soluzione empiricamente buona.
- **Quando il percorso indiretto fallisce**: per problemi semplici funziona bene; al crescere della complessità (es. portafoglio con q=100 asset → 5150 parametri) la stima del modello diventa inaffidabile e il percorso diretto conviene.
- **Obiettivo ambizioso**: `prior + dati → design → verifica feature di interesse` e `prior + dati → garanzie → sempre intatte`.
- **Logica delle molte idee**: generare molte soluzioni candidate (scenari) e selezionare tra esse, sviluppando fiducia nella decisione finale.

## Il problema di ottimizzazione data-driven

Dato uno spazio di decisioni ν e un parametro di incertezza δ ~ P (distribuzione sconosciuta), il problema ideale è:

```
ν* = argmin_ν E_δ[ℓ(ν, δ)]
```

Poiché P è sconosciuta, si sostituisce con N campioni δ₁,…,δ_N (scenari), ottenendo il **Scenario Program**:

```
ν_N = argmin_ν  max_{i=1,...,N} ℓ(ν, δᵢ)
```

La domanda chiave è: quali garanzie ha ν_N rispetto alla distribuzione reale P?

## Esempio: ottimizzazione di portafoglio

- q asset, ν^k = percentuale di capitale investita sull'asset k, ν = [ν¹ … νq]^T
- Perdita: ℓ(ν, δ) = Σ νᵏ Rᵏ, con δ = [R¹ … Rq]^T vettore dei rendimenti
- Modello Black-Scholes → distribuzione log-normale
- Per q=100 → 5150 parametri da stimare: il percorso indiretto diventa fragile
- **Soluzione diretta**: usare campioni storici dei rendimenti come scenari

## Esempio: defibrillazione

- δᵢ = (uᵢ, yᵢ): uᵢ = traccia ECG (fibrillazione ventricolare), yᵢ ∈ {0,1} (shock inefficace/efficace)
- L'ECG è un oggetto infinito-dimensionale → si estraggono feature finite: PTP, Amax, Amin, WA, RMS, DF, CF, EF, AMSA
- Obiettivo: classificatore che predica se lo shock sarà efficace, con garanzie probabilistiche sull'errore
- Il percorso indiretto (stimare le densità di probabilità condizionali) produce due distribuzioni con sovrapposizione → confine di decisione ambiguo
- Il percorso diretto (scenario approach su features) produce direttamente il confine con certificato

## Approccio indiretto vs diretto

| Aspetto | Indiretto | Diretto |
|---|---|---|
| Stima | Modello completo del mondo | Non necessaria |
| Ottimizzazione | Su modello stimato | Su campioni (scenari) |
| Scalabilità | Crolla con la dimensionalità | Dipende dalla dimensione del problema di ottimizzazione |
| Garanzie | Derivano dalla qualità della stima | Teorema dello scenario (probabilistiche, esatte) |
| Applicabilità | Semplici problemi | Problemi complessi, alta dimensione |

## La struttura delle garanzie

Il framework ambizioso:
- **prior + dati → design**: la soluzione viene costruita combinando informazione a priori e campioni
- **prior + dati → garanzie**: le garanzie probabilistiche restano valide indipendentemente dal problema specifico
- Le garanzie devono *sempre rimanere intatte*, non solo in media

## Articoli correlati

- [[scenario-optimization]]
- [[probability-foundations]]
- [[sys-id-and-limits-learning]]

## Fonti

- raw/introduction_COMPILED.pdf — Slides introduttive del corso (Campi & Carè, Budapest Jan. 2026)
