---
tags: [ottimizzazione, metaeuristica, tabu-search, local-search, intensificazione, diversificazione]
data_creazione: 2026-06-02
data_aggiornamento: 2026-06-02
fonti:
  - raw/Tabu Search.pdf
---

# Tabu Search

Metaeuristica basata su local search che supera i limiti dei puri metodi di discesa permettendo **mosse peggioranti** e mantenendo una **memoria adattiva** per evitare cicli. Classificazione: M/Y (con memoria), neighbourhood, iterativo. Introdotta da Fred Glover (1986).

## Punti chiave

- Accetta mosse che **peggiorano** la funzione obiettivo per sfuggire ai minimi locali.
- La **Tabu List (TL)** contiene le mosse recentemente eseguite, vietandone la ripetizione per j iterazioni (**tabu tenure**).
- Usa **due livelli di memoria**: Short Term Memory (STM) per evitare cicli locali; Long Term Memory (LTM) per guidare intensificazione e diversificazione globale.
- Le **aspiration criteria** consentono di "liberare" una mossa tabu in condizioni particolari (es. se migliora il best noto).
- Breve tabu tenure → intensificazione (esplorazione locale); lunga tabu tenure → diversificazione (esplorazione globale).
- La memoria identifica le soluzioni secondo quattro dimensioni: Recency, Frequency, Quality, Influence.

## Background e motivazione

I **local search** tradizionali non ammettono mosse peggioranti → rimangono bloccati in ottimi locali.

**Metaeuristica** = algoritmo di local search con tecniche per:
- **Sfuggire** dagli ottimi locali (mosse peggioranti).
- **Evitare il ciclismo** (la stessa soluzione non viene rivisitata inutilmente).

Esempi di metaeuristiche: Simulated Annealing (SA), Tabu Search (TS), Genetic Algorithms (GA).

## Terminologia

- **Mossa**: operazione che definisce il vicinato N(x) di una soluzione x.
- **Tabu move**: mossa vietata perché recentemente eseguita.
- **Tabu tenure j**: durata dello stato tabu-attivo di un attributo.

## Features principali

1. **Accettazione di mosse peggioranti**: evita di rimanere in un ottimo locale.
2. **Tabu List**: contiene tutte le mosse vietate; evita di ritornare all'ottimo locale da cui si è appena usciti.
3. **Due tipi di memoria**: short term memory (STM) e long term memory (LTM).
4. **Fasi di intensificazione e diversificazione**: organizzano il processo di ricerca.
5. **Aspiration criteria**: superano le limitazioni di una TL troppo restrittiva.

## Algoritmo base (Short Term Memory)

```
1. Trova soluzione iniziale x̃; x := x̃; TL := ∅
2. Genera il vicinato N(x)
3. Trova y ∈ N(x) tale che:
   · f(y) è minimale
   · y ≠ x (mossa non ciclica)
   · la mossa non viola nessun tabu
4. x := y
5. Aggiorna TL aggiungendo la mossa x → y
6. Se criterio di stop non soddisfatto, vai a 2; altrimenti STOP
```

**Criteri di stop**: numero massimo di iterazioni; numero di iterazioni dall'ultimo miglioramento; limite di tempo.

## Memoria adattiva

Caratteristica distintiva del TS rispetto ad altri metodi: separazione tra **STM** (memoria a breve termine) e **LTM** (memoria a lungo termine). Il vicinato modificato N*(x) è il risultato di una **storia selettiva** degli stati incontrati.

- **STM-based**: N*(x) ⊂ N(x) — la tabu list esclude elementi da N(x).
- **LTM-based**: N*(x) può estendersi oltre N(x) includendo soluzioni non-vicine.

Questo rende TS un **metodo a vicinato dinamico**.

## Identificazione delle soluzioni

Le memorie tracciano le soluzioni lungo quattro dimensioni:

| Dimensione | Descrizione |
|-----------|-------------|
| **Recency** | Tiene traccia delle caratteristiche modificate di recente |
| **Frequency** | Frequenza di modifica di una caratteristica durante la ricerca |
| **Quality** | Capacità di distinguere le soluzioni identificando gli elementi comuni delle migliori |
| **Influence** | Grado di cambiamento strutturale indotto nella soluzione da una mossa |

## Short Term Memory (STM)

La STM più comune è quella **recency-based**:
- Tiene traccia degli attributi di soluzione cambiati di recente.
- Crea la **Tabu List**: gli attributi sono classificati *tabu-active* e le mosse corrispondenti vietate per le prossime j iterazioni (**tabu tenure**).

**Solution selection criteria** basati su STM:
- **Aspiration plus**: soglia sulla qualità della soluzione; vengono mantenute solo le soluzioni che superano la soglia.
- **Elite list**: lista delle migliori soluzioni incontrate durante la ricerca.

## Tabu Tenure

Il tabu tenure j controlla il bilanciamento intensificazione/diversificazione:

| Tenure corta | Tenure lunga |
|---|---|
| Esplora soluzioni più vicine all'ottimo locale | Esamina porzioni inesplorate dello spazio |
| **Intensificazione** | **Diversificazione** |
| Rischio di ciclismo se troppo corta | Rischio di soluzioni di scarsa qualità se troppo lunga |

**Tenure dinamico**:
- **Random dynamic**: range [t_min, t_max] stabilito all'inizio; tenure estratto da distribuzione uniforme.
- **Systematic dynamic**: tenure variato durante l'esecuzione per seguire tendenze specifiche.

## Influence

L'influence misura il grado di cambiamento strutturale indotto nella soluzione da una mossa.

- **Low influence move**: modifica poco la struttura (es. swap di edge adiacente nell'albero); utile per fine-tuning.
- **High influence move**: introduce nodi nuovi nella soluzione; può non migliorare l'obiettivo ma consente di uscire da ottimi locali.

**Uso**: lo stato tabu-attivo di una mossa a bassa influenza può essere revocato se nel frattempo è stata eseguita una mossa ad alta influenza (aspiration criterion).

## Aspiration Criteria

Consentono di **ignorare lo stato tabu-attivo** di una mossa in condizioni specifiche:

1. **Default**: se tutte le mosse possibili sono tabu, si seleziona quella con tabu più vecchio.
2. **Objective**: seleziona una mossa tabu se porta a una soluzione migliore del best noto.
3. **Search direction**: un attributo può essere aggiunto/rimosso se la direzione di ricerca non cambia.
4. **Influence**: lo stato tabu di una mossa a bassa influenza è revocato se è stata eseguita una mossa ad alta influenza.

## Long Term Memory (LTM)

La LTM guida **intensificazione** e **diversificazione** globale basandosi su misure di frequenza.

**Transition measure**: numero di volte in cui una caratteristica è stata modificata durante l'esecuzione (volatilità). Alto indice di transizione → caratteristica usata spesso per fine-tuning locale.

**Residence measure**: numero di iterazioni in cui una caratteristica appartiene a una soluzione visitata. Buon indicatore di attrattività o segnale di inizio diversificazione.

**Frequency-based approach**:
- **Intensificazione**: focalizza la ricerca nei vicinati di soluzioni di qualità elevata.
- **Diversificazione**: sposta la ricerca in porzioni inesplorate; il processo può ripartire da una soluzione diversa.

## Critical Event Memory (CEM)

Strategia di diversificazione che registra il verificarsi di **eventi critici**:
1. Identificazione di una nuova soluzione iniziale.
2. Esecuzione di una mossa che migliora la soluzione precedente.

**Utilizzo**: al momento del restart, le soluzioni corrispondenti a eventi critici vengono combinate per costruire un sottografo; la nuova soluzione iniziale viene costruita penalizzando gli edge contenuti nel sottografo.

## Esempio: Minimum k-tree Problem

Dato un grafo pesato non orientato G, trovare il minimum spanning tree con esattamente k archi.

**Costruzione iniziale** (greedy, k=4): archi selezionati = {(1,2), (1,4), (4,7), (6,7)}, peso totale = 40.

**Move**: rimuovi un arco e aggiungi un arco diverso (swap). **Tabu activation**: archi rimossi → tabu per 2 iter; archi aggiunti → tabu per 1 iter.

**Risultato** (10 iterazioni): ottimo globale trovato all'iterazione 9, peso = 34.
Andamento: la funzione obiettivo oscilla peggiorando (fino a 63) e poi migliora (fino a 34), mostrando l'efficacia delle mosse peggioranti.

**Dopo CEM + restart**: partendo da nuova soluzione (peso 38), si trova peso 34 in 2 iterazioni.

## Articoli correlati

- [[metaeuristiche-introduzione]]
- [[vns]]
- [[alns]]
- [[grasp]]

## Fonti

- `raw/Tabu Search.pdf` — slide del corso Optimization Algorithms, Prof. Renata Mansini, UniBS A.A. 2020/2021.
