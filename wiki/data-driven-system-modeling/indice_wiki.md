---
tags: [ddsm, indice]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti: []
---

# Data Driven System Modeling — Indice

Corso UniBS su modellistica di sistemi a partire da dati: regressione lineare, minimi quadrati, proprietà statistiche degli stimatori, identificazione di sistemi dinamici stocastici. Testi principali: Bontempi & Venturini (2020), Ramponi (2016).

## Articoli

- [[introduzione-e-setup]] — Setup del problema, classi di modelli lineari, tipi di regressione, funzioni di costo (OLS / $l_1$ / min-max) e loro pro/contro.
- [[minimi-quadrati-ordinari]] — OLS: equazioni normali, esistenza/unicità della soluzione, interpretazione geometrica come proiezione ortogonale, bontà del fit ($\rho^2$), variante vincolata (Lagrangiani) e pesata (WLS).
- [[proprieta-statistiche-stimatori-ls]] — Proprietà statistiche: non polarizzazione, consistenza, eccitazione persistente, BLUE (Gauss-Markov), stima di $\sigma^2_\varepsilon$, FPE per selezione del modello, regolarizzazione Ridge/Lasso.
- [[identificazione-sistemi]] — Processi stocastici WSS, decomposizione di Wold, classi MA/AR/ARMA/ARX/ARMAX, predittore ottimale (filtro di Wiener), equazioni di Yule-Walker, PEM e consistenza (3 teoremi).
