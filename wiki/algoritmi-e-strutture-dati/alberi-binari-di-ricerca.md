---
tags: [alberi-binari, BST, strutture-dati, visita, dizionario]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/Alberi binari di ricerca con integrazioni.pdf
---

# Alberi Binari di Ricerca (BST)

Un **albero binario di ricerca** (BST, Binary Search Tree) è una struttura dati usabile sia come dizionario sia come coda con priorità. Ogni nodo x ha campi: `key[x]`, `left[x]`, `right[x]`, `p[x]` (padre).

**Proprietà BST**: per ogni nodo x, tutti i nodi del sottoalbero sinistro hanno chiave ≤ key[x]; tutti i nodi del sottoalbero destro hanno chiave ≥ key[x].

## Punti chiave

- Tutte le operazioni base richiedono **O(h)**, con h = altezza dell'albero
- Albero completo: h = Θ(lg n) → operazioni in Θ(lg n)
- Albero lineare (degenerato): h = n–1 → operazioni in Θ(n)
- La visita in ordine simmetrico (in-order) stampa le chiavi in ordine **non decrescente**
- Tre tipi di visita: simmetrica (L-radice-R), anticipata (radice-L-R), differita (L-R-radice)
- La cancellazione è la più complessa: tre casi in base al numero di figli di z

## Visite dell'albero

```
INORDER-TREE-WALK(x)          ▷ visita simmetrica — T(n) = Θ(n)
  if x ≠ NIL then
    INORDER-TREE-WALK(left[x])
    stampa key[x]
    INORDER-TREE-WALK(right[x])
```

Analogamente per visita anticipata (stampa prima di ricorrere) e differita (stampa dopo entrambe le ricorsioni). Tutte valgono per qualsiasi albero binario, non solo BST.

**Analisi**: T(n) = Θ(n) perché INORDER-TREE-WALK è chiamata esattamente 2 volte per ogni nodo → c·n ≤ T(n) ≤ 3c·n.

## Ricerca

```
TREE-SEARCH(x, k)         → O(h)
  if x = NIL o k = key[x] then return x
  if k < key[x]
    then return TREE-SEARCH(left[x], k)
    else return TREE-SEARCH(right[x], k)

ITERATIVE-TREE-SEARCH(x, k)   → O(h), più efficiente in pratica
  while x ≠ NIL e k ≠ key[x]
    do if k < key[x] then x ← left[x]
       else x ← right[x]
  return x
```

## Minimo, massimo, successore, predecessore

```
TREE-MINIMUM(x)    → O(h): scende sempre a sinistra
  while left[x] ≠ NIL do x ← left[x]; return x

TREE-MAXIMUM(x)    → O(h): scende sempre a destra
  while right[x] ≠ NIL do x ← right[x]; return x
```

**Successore** di x (nodo con la più piccola chiave > key[x]):
- Se right[x] ≠ NIL: TREE-MINIMUM(right[x])
- Altrimenti: risalire finché si è figlio sinistro — il padre è il successore

```
TREE-SUCCESSOR(x)    → O(h)
  if right[x] ≠ NIL then return TREE-MINIMUM(right[x])
  y ← p[x]
  while y ≠ NIL e x = right[y]
    do x ← y; y ← p[y]
  return y
```

TREE-PREDECESSOR è simmetrica (sostituire right↔left, MINIMUM→MAXIMUM).

## Inserimento

```
TREE-INSERT(T, z)    → O(h); precondizione: left[z] = right[z] = NIL
  y ← NIL; x ← root[T]
  while x ≠ NIL
    do y ← x
       if key[z] < key[x] then x ← left[x] else x ← right[x]
  p[z] ← y
  if y = NIL then root[T] ← z          ▷ albero vuoto
  else if key[z] < key[y] then left[y] ← z
  else right[y] ← z
```

## Cancellazione

TREE-DELETE(T, z) gestisce tre casi → T(n) = O(h):

1. **z senza figli**: si rimuove z direttamente
2. **z con un figlio**: si collega il figlio al padre di z
3. **z con due figli**: si trova il successore y di z (che ha al più un figlio destro), si estrae y fisicamente dall'albero e si sostituisce il contenuto di z con quello di y

```
TREE-DELETE(T, z)    → O(h)
  if left[z] = NIL o right[z] = NIL then y ← z
  else y ← TREE-SUCCESSOR(z)             ▷ y ha al più un figlio
  if left[y] ≠ NIL then x ← left[y]
  else x ← right[y]                      ▷ x = figlio non-NIL di y (o NIL)
  if x ≠ NIL then p[x] ← p[y]
  if p[y] = NIL then root[T] ← x
  else if y = left[p[y]] then left[p[y]] ← x
  else right[p[y]] ← x
  if y ≠ z then key[z] ← key[y]          ▷ copia anche altri campi satellite
  return y                                ▷ y riutilizzabile nella lista libera
```

## Analisi della complessità

Tutte le operazioni (SEARCH, MIN, MAX, SUCCESSOR, PREDECESSOR, INSERT, DELETE) richiedono **O(h)**.

| Albero | Altezza h | Complessità operazioni |
|---|---|---|
| Completo / bilanciato | Θ(lg n) | Θ(lg n) |
| Lineare (degenerato) | n–1 | Θ(n) |
| Casuale (n inserimenti) | O(lg n) atteso | O(lg n) atteso |

Per garantire O(lg n) nel caso peggiore si usano alberi bilanciati (es. Red-Black Tree).

## Articoli correlati

- [[strutture-dati]]
- [[tabelle-hash]]
- [[algoritmi-di-ordinamento]]

## Fonti

- `raw/Alberi binari di ricerca con integrazioni.pdf` — Prof.ssa Marina Zanella, UniBS
