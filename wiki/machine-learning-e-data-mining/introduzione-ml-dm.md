---
tags: [machine-learning, data-mining, corso, unibs]
data_creazione: 2026-05-29
data_aggiornamento: 2026-05-29
fonti:
  - raw/ilovepdf_merged.pdf
---

# Introduzione al Machine Learning e Data Mining

Corso della magistrale UniBS tenuto dai Prof. Alfonso E. Gerevini e Prof. Ivan Serina. Copre un ampio spettro di metodi di ML e Data Mining con enfasi su applicabilità pratica, non solo teoria.

## Punti chiave

- **Obiettivo**: formulare correttamente il problema, scegliere l'algoritmo adatto, valutarne l'efficacia sperimentalmente.
- **Python** è il linguaggio usato per esercitazioni pratiche.
- L'enfasi è su **concetti e tecniche** applicabili, non su formalizzazione matematica pura.
- Il corso usa datasets da UCI Repository, UCI KDD Archive, Statlib.
- Testi di riferimento: Mitchell *Machine Learning* (1997), Géron *Hands-On ML with Scikit-Learn, Keras, TF* (2019).

## Programma del corso

1. **Introduzione** a ML e Data Mining
2. **Rappresentazione e preprocessing** dei dati
3. **Apprendimento supervisionato**: apprendimento induttivo, [[alberi-di-decisione]], [[reti-neurali-artificiali]], regressione/classificazione lineare, [[support-vector-machines]]
4. **Apprendimento Bayesiano**: [[bayesian-learning]]
5. **Apprendimento non supervisionato e clustering**: [[cluster-analysis]]
6. Python per esercitazioni

## Definizioni di Machine Learning

- **"Machine learning is programming computers to improve the performance of a computer program using past experience or example data"**
- **"Machine learning (or data mining) is the task of producing useful knowledge from (possibly big) data"**
- ML = cuore della **Data Science**

**ML è utile soprattutto quando**:
- L'expertise umana non esiste o la conoscenza disponibile è insufficiente (navigazione autonoma su Marte, fondo oceanico)
- Gli umani non sono in grado di spiegare/formalizzare la propria expertise (speech recognition, face recognition, guida autonoma)

**I tre fattori che alimentano il ML moderno**:
1. Progresso in algoritmi e teoria
2. Crescita esponenziale di dati online (big data)
3. Aumento della potenza computazionale (hardware, GPU)

## Discipline rilevanti

ML è interdisciplinare: Intelligenza Artificiale, Metodi Bayesiani e Statistica, Teoria della complessità computazionale, Control theory, Information theory, Algebra Lineare, Filosofia, Psicologia e neuroscienze. (vedi Mitchell per una discussione sull'influenza di ciascuna disciplina)

## Applicazioni principali

- Diagnosi medica, terapia, prognosi
- Credit scoring, fraud detection, spam detection
- Web mining, motori di ricerca
- Speech recognition
- Agenti autonomi, robotica
- Giochi e video-giochi
- Previsioni finanziarie/sociali/industriali/militari (Industry 4.0: **predictive maintenance** di macchine)

**Esempi concreti dal corso**:
- **ALVINN (1993)**: rete neurale con 30×32 pixel input → 4 hidden units → 30 output (angolo di sterzata) per guida autonoma
- **Apple classification**: classificare mele per dimensione e colore su nastro trasportatore
- **ARGOS**: classificare barche a Venezia per tipo tramite video

## Il processo di Data Mining

Non è semplicemente `Data → Python → Risultato`. È un ciclo iterativo che include:
- **Ask a question**: definire precisamente cosa si vuole sapere
- **Gather data**: raccogliere abbastanza dati (più dati battono un algoritmo più furbo)
- **Clean the data**: i dati reali sono sempre "sporchi"
- **Define new features**: feature engineering è la chiave del data mining
- **Deploy**: implementazione tecnica e comunicazione dei risultati

## Articoli correlati

- [[alberi-di-decisione]]
- [[bayesian-learning]]
- [[cluster-analysis]]
- [[ensemble-methods]]
- [[support-vector-machines]]
- [[reti-neurali-artificiali]]
- [[valutazione-ipotesi]]

## Fonti

- raw/ilovepdf_merged.pdf (slide introduttive del corso)
