---
tags: [strutture-dati, pila, coda, lista, insiemi-dinamici, array]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/Strutture_dati_con_integrazioni.pdf
  - raw/figure_strutture_dati.pdf
---

# Strutture Dati

Le strutture dati sono realizzazioni concrete di **insiemi dinamici finiti** (il cui contenuto cambia nel tempo). La scelta della struttura ottimale dipende dalle operazioni richieste. Ogni elemento è un oggetto con una chiave `key[x]` (identificatore univoco) e dati satellite.

## Punti chiave

- **Operazioni di modifica**: INSERT(S, x) e DELETE(S, x)
- **Operazioni di interrogazione**: SEARCH, MINIMUM, MAXIMUM, SUCCESSOR, PREDECESSOR
- **Pila (Stack)**: LIFO — PUSH/POP in O(1); realizzabile con array
- **Coda (Queue)**: FIFO — ENQUEUE/DEQUEUE in O(1); realizzabile con array circolare
- **Lista concatenata**: ordine lineare degli elementi; semplice o doppia, ordinata o non, circolare o non
- **Sentinella**: oggetto fittizio che semplifica la gestione dei casi limite (ma usa memoria extra)
- Le strutture concatenate si possono realizzare con puntatori espliciti, array multipli o singolo array

## Pila (Stack)

Insieme con politica **LIFO** (Last-In-First-Out). INSERT → PUSH, DELETE → POP.

Realizzazione: array S[1..n] con attributo `top[S]` = indice dell'elemento più recente. Pila vuota ↔ top[S] = 0.

```
STACK-EMPTY(S)   → O(1)
  if top[S] = 0 then return TRUE else return FALSE

PUSH(S, x)       → O(1)
  top[S] ← top[S] + 1
  S[top[S]] ← x

POP(S)           → O(1)
  if STACK-EMPTY(S) then error "underflow"
  else top[S] ← top[S] – 1; return S[top[S]+1]
```

## Coda (Queue)

Insieme con politica **FIFO** (First-In-First-Out). INSERT → ENQUEUE, DELETE → DEQUEUE.

Realizzazione: array circolare Q[1..n] con:
- `head[Q]` = indice della testa (primo elemento)
- `tail[Q]` = indice della prossima posizione libera
- Coda vuota ↔ head[Q] = tail[Q] = 1
- Coda piena ↔ head[Q] = (tail[Q] + 1) mod n

Capacità massima: n–1 elementi.

```
ENQUEUE(Q, x)    → O(1)
  Q[tail[Q]] ← x
  if tail[Q] = length[Q] then tail[Q] ← 1
  else tail[Q] ← tail[Q] + 1

DEQUEUE(Q)       → O(1)
  x ← Q[head[Q]]
  if head[Q] = length[Q] then head[Q] ← 1
  else head[Q] ← head[Q] + 1
  return x
```

## Lista concatenata

Insieme con ordine lineare degli elementi. Attributo `head[L]` = puntatore al primo elemento. Lista vuota ↔ head[L] = NIL.

**Classificazioni ortogonali**:
- **Semplice / Doppia**: semplice ha solo `next`; doppia ha anche `prev` (prev[x]=NIL → x è la testa, next[x]=NIL → x è la coda)
- **Ordinata / Non ordinata**: ordinata se l'ordine lineare corrisponde all'ordine delle chiavi
- **Circolare**: solo bidirezionale; prev della testa punta alla coda e next della coda punta alla testa

### Operazioni su lista bidirezionale non ordinata (non circolare)

```
LIST-SEARCH(L, k)       → Θ(n) caso peggiore
  x ← head[L]
  while x ≠ NIL e key[x] ≠ k do x ← next[x]
  return x

LIST-INSERT(L, x)       → O(1)  (inserisce in testa)
  next[x] ← head[L]
  if head[L] ≠ NIL then prev[head[L]] ← x
  head[L] ← x; prev[x] ← NIL

LIST-DELETE(L, x)       → O(1) se x è già noto; Θ(n) se serve cercare per chiave
  if prev[x] ≠ NIL then next[prev[x]] ← next[x]
  else head[L] ← next[x]
  if next[x] ≠ NIL then prev[next[x]] ← prev[x]
```

### Sentinella

Oggetto fittizio `nil[L]` che rappresenta NIL, semplificando i casi limite (lista diventa circolare con sentinella tra testa e coda):
- `next[nil[L]]` = puntatore alla testa; `prev[nil[L]]` = puntatore alla coda
- Lista vuota: `next[nil[L]] = nil[L]` e `prev[nil[L]] = nil[L]`

Con sentinella, LIST-DELETE' diventa 2 righe: `next[prev[x]] ← next[x]` e `prev[next[x]] ← prev[x]`.

**Pro/contro**: migliora chiarezza del codice, non la velocità. Con molte piccole liste, la memoria extra per le sentinelle può essere un costo da valutare.

## Realizzazione senza puntatori espliciti

**Array multipli**: un array per ogni campo (key[], next[], prev[]). Gli indici fanno da puntatori; NIL = –1.

**Singolo array**: un unico array che contiene tutti i campi contigui per ogni oggetto.

## Riepilogo complessità

| Struttura | SEARCH | INSERT | DELETE | MIN/MAX | SUCC/PRED |
|---|---|---|---|---|---|
| Pila/Coda | — | O(1) | O(1) | — | — |
| Lista non ordinata | Θ(n) | O(1) | O(1)* | Θ(n) | Θ(n) |
| Lista ordinata | Θ(n) | Θ(n) | O(1)* | O(1) | O(1) |

(*) assumendo puntatore diretto all'elemento

## Articoli correlati

- [[alberi-binari-di-ricerca]]
- [[tabelle-hash]]
- [[algoritmi-di-ordinamento]]

## Fonti

- `raw/Strutture_dati_con_integrazioni.pdf` — Prof.ssa Marina Zanella, UniBS
- `raw/figure_strutture_dati.pdf` — figure di supporto
