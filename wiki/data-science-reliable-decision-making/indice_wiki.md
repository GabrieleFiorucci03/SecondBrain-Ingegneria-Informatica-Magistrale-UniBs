---
tags: [data-science, ottimizzazione, scenario-optimization, garanzie, probabilità]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
---

# Data Science for Reliable Decision Making — Indice

Corso UniBS tenuto da Marco C. Campi e Algo Carè. Si occupa di ottimizzazione data-driven con garanzie certificate: come passare dai dati a decisioni affidabili combinando priors probabilistici e campioni (scenario optimization). Il filo conduttore è la distinzione tra approccio indiretto (stima modello → ottimizza) e diretto (dati → decisione), e come dotare il secondo di certificati di qualità rigorosi.

## Articoli

- [[introduzione]] — Panoramica del corso: i due dilemmi dell'ottimizzazione data-driven (indiretto vs diretto, certificati di qualità), esempi motivazionali (portafoglio, defibrillazione). Fonte: slides Budapest Jan. 2026.
- [[scenario-optimization]] — Il framework Scenario Optimization: problema, soluzione SP_N, teorema di generalizzazione, bound esatto binomiale, distribuzione Beta della violation probability, support constraints, applicazioni. Fonti: libro SIAM 2018, TAC 2006, SIAM 2008.
- [[probability-foundations]] — Fondamenti probabilistici (note di Campi 2021): spazi di misura, variabili aleatorie, convergenza stocastica, Teorema di Proiezione in spazi di Hilbert, processi WSS e spettro di potenza.
- [[noise-compensation]] — Esempio completo feedforward noise compensation su sistema ARMAX con parametri incerti: formulazione SP_N, risultato numerico (N=5427, ℓ*=5.8), Risk-Return Tradeoff da scenario removal.
- [[sys-id-and-limits-learning]] — Identificazione di sistemi (PEM, ARX, OE) e limiti fondamentali dell'apprendimento: Lemma di convergenza, Teorema PEM, teoria VC per predittori nonlineari, Glivenko-Cantelli. Fonte: note Campi 2006.
