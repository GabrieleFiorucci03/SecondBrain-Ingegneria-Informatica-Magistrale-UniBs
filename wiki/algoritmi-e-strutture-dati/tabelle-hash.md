---
tags: [tabelle-hash, dizionario, funzione-hash, collisioni, concatenazione, indirizzamento-aperto]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/Tabelle_hash_con_integrazioni_post.pdf
  - raw/tabelle_hash_addendum.pdf
  - raw/indirizzamento_aperto.pdf
---

# Tabelle Hash

Una **tabella hash** è una struttura dati efficace per realizzare **dizionari** (insieme dinamico con solo INSERT, DELETE, SEARCH). Generalizza il concetto di array: invece di usare la chiave come indice direttamente, calcola l'indice tramite una funzione hash. Permette operazioni in **O(1) nel caso medio** usando memoria Θ(|K|), anche quando l'universo delle chiavi U è molto grande.

## Punti chiave

- **Indirizzamento diretto**: T[0..m–1], T[k] = elemento con chiave k — O(1) ma richiede |U| = m posizioni
- **Funzione hash** h: U → {0,…,m–1} con m < |U| — riduce lo spazio ma introduce **collisioni**
- **Fattore di carico** α = n/m (n = elementi, m = slot): misura l'occupazione media per slot
- **Concatenazione**: collisioni risolte con liste; ricerca in Θ(1+α) caso medio
- Se m ∝ n → α = O(1) → tutte le operazioni in **O(1) caso medio**
- **Metodo di divisione**: h(k) = k mod m; m deve essere primo non vicino a potenze di 2
- **Metodo di moltiplicazione**: h(k) = ⌊m·(kA mod 1)⌋ con A costante irrazionale (es. A=(√5–1)/2)
- **Hashing universale**: scelto casualmente h da una famiglia H — garantisce O(1) caso medio indipendentemente dall'input

## Tabella a indirizzamento diretto

Funziona bene se U = {0,1,…,m–1} è piccolo. Array T[0..m–1] dove T[k] = elemento con chiave k (o NIL).

```
DIRECT-ADDRESS-SEARCH(T, k) → O(1): return T[k]
DIRECT-ADDRESS-INSERT(T, x) → O(1): T[key[x]] ← x
DIRECT-ADDRESS-DELETE(T, x) → O(1): T[key[x]] ← NIL
```

Problema: se |U| è grande, T occupa troppa memoria (la maggior parte inutilizzata).

## Tabella hash: concetto

**Funzione hash** h: U → {0,…,m–1} associa a ogni chiave un indice (**valore hash**) nella tabella T[0..m–1]. L'obiettivo è ridurre m rispetto a |U|.

**Collisione**: due chiavi k₁ ≠ k₂ con h(k₁) = h(k₂). Inevitabile se |K| > m. Due metodi di risoluzione:
1. **Concatenazione** (chaining)
2. **Indirizzamento aperto** (open addressing) — vedi sezione dedicata

## Risoluzione per concatenazione

Tutti gli elementi con lo stesso valore hash vengono messi in una lista concatenata. T[j] = puntatore alla testa della lista degli elementi con h(key) = j.

```
CHAINED-HASH-INSERT(T, x) → O(1): inserisci x in testa a T[h(key[x])]
CHAINED-HASH-SEARCH(T, k) → O(n lista): cerca k in T[h(k)]
CHAINED-HASH-DELETE(T, x) → O(n lista): cancella x da T[h(key[x])]
```

### Analisi

**Caso peggiore**: tutte le n chiavi nella stessa lista → ricerca Θ(n) + Θ(1) per hash → non meglio di una lista semplice.

**Caso medio** (ipotesi di uniformità semplice: ∀j, Σ_{k:h(k)=j} P(k) = 1/m):
- **Fattore di carico** α = n/m = lunghezza media delle liste
- Ricerca senza successo: Θ(1+α) — si percorre tutta la lista T[h(k)]
- Ricerca con successo: Θ(1+α) — in media α/2 confronti dopo h(k)

**Se m = Θ(n) → α = O(1) → tutte le operazioni del dizionario in O(1) caso medio**

## Funzioni hash

Le chiavi sono trattate come numeri naturali (stringhe codificate in base 2^p).

### Metodo di divisione
h(k) = k mod m

Valori di m da evitare:
- m = 2^p → h dipende solo dagli ultimi p bit di k
- m = 2^p – 1 → stringhe con caratteri scambiati hanno lo stesso hash
- m = b^p per chiavi in base b

**Scelta ottimale**: m = numero primo non vicino a potenze esatte di 2.
Esempio: n=2000 chiavi, α desiderato ≤ 3 → m ≥ 2000/3 = 667 → m = 701 (primo).

### Metodo di moltiplicazione
h(k) = ⌊m · (kA mod 1)⌋ dove A ∈ (0,1)

- `kA mod 1` = parte frazionaria di kA
- Vantaggio: il valore di m non è critico; si usa spesso m = 2^p per efficienza
- Costante suggerita da Knuth: A = (√5–1)/2 ≈ 0.6180339887

### Hashing universale
Si sceglie casualmente h da una famiglia H di funzioni hash prima di ogni esecuzione. Una famiglia H è **universale** se per ogni coppia k₁≠k₂: Pr_{h∈H}[h(k₁)=h(k₂)] ≤ 1/m.

Proprietà: con hashing universale il n° atteso di collisioni di k con gli altri elementi è < 1 → ricerca in O(1) caso medio, qualunque sia l'input.

## Indirizzamento aperto

Tutti gli elementi sono memorizzati nella tabella stessa (no liste esterne). In caso di collisione si cerca un'altra posizione tramite una **sequenza di ispezione** h(k, 0), h(k, 1), …, h(k, m–1).

**Condizione**: α = n/m ≤ 1 (non si possono inserire più di m elementi).

**Operazioni**:
```
HASH-INSERT(T, k): ispeziona h(k,0), h(k,1), ... fino a slot NIL → inserisce
HASH-SEARCH(T, k): ispeziona h(k,0), h(k,1), ... fino a k trovato o NIL
HASH-DELETE: non si cancella con NIL (rompe la ricerca) → si usa marcatore DELETED
```

### Metodi di ispezione

**Ispezione lineare**: h(k, i) = (h'(k) + i) mod m
- Semplice ma soffre di **clustering primario**: lunghe sequenze di slot occupati consecutivi

**Ispezione quadratica**: h(k, i) = (h'(k) + c₁i + c₂i²) mod m
- Riduce clustering primario ma soffre di **clustering secondario**: due chiavi con stesso h'(k) hanno stessa sequenza

**Doppio hashing**: h(k, i) = (h₁(k) + i·h₂(k)) mod m
- La sequenza dipende da k in due modi → distribuzione migliore
- Richiede che h₂(k) sia coprimo con m (es. m = 2^p, h₂(k) sempre dispari)

### Analisi con hashing uniforme

Ipotesi (hashing uniforme): ogni permutazione degli m slot è ugualmente probabile.

| | Ricerca senza successo | Inserimento | Ricerca con successo |
|---|---|---|---|
| N° ispezioni atteso | ≤ 1/(1–α) | ≤ 1/(1–α) | ≤ (1/α)·ln(1/(1–α)) |

Con α costante, tutte le operazioni sono O(1).

## Confronto strutture per dizionario

| Struttura | Search | Insert | Delete | Note |
|---|---|---|---|---|
| Lista non ordinata | Θ(n) | O(1) | O(1)* | — |
| BST bilanciato | O(lg n) | O(lg n) | O(lg n) | Supporta min/max/succ |
| Hash con concat. | O(1) medio | O(1) | O(1)* | No operazioni d'ordine |
| Hash indirizzam. aperto | O(1) medio | O(1) | O(1)† | α ≤ 1 |

## Articoli correlati

- [[strutture-dati]]
- [[alberi-binari-di-ricerca]]
- [[ordinamento-lineare]]

## Fonti

- `raw/Tabelle_hash_con_integrazioni_post.pdf` — Prof.ssa Marina Zanella, UniBS
- `raw/tabelle_hash_addendum.pdf` — integrazioni manoscritte
- `raw/indirizzamento_aperto.pdf` — indirizzamento aperto
