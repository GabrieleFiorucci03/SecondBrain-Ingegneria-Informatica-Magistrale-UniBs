---
tags: [calcolo-scientifico, sistemi-lineari, metodi-diretti, gauss, LU]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/5 - SL-metodi-diretti.pdf
  - raw/sl_diretti1.pdf
  - raw/fattorizzazione_lu.pdf
  - raw/sl_fillin.pdf
  - raw/sl_meg_pivot.pdf
  - raw/sl_sparse.pdf
---

# Metodi Diretti per Sistemi Lineari

I metodi diretti risolvono $A\mathbf{x} = \mathbf{b}$ trasformando il sistema in uno equivalente più semplice, in un numero finito di operazioni. Il metodo fondamentale è l'Eliminazione di Gauss (MEG), che riduce $A$ a forma triangolare superiore.

## Punti chiave

- **Matrice diagonale**: $x_i = b_i / a_{ii}$; costo $O(n)$ operazioni.
- **Matrice triangolare inferiore**: sostituzione in avanti ($O(n^2)$ operazioni).
- **Matrice triangolare superiore**: sostituzione all'indietro ($O(n^2)$ operazioni).
- **MEG (Eliminazione di Gauss)**: riduce $A$ a triangolare superiore in $O(n^3/3)$ operazioni; poi sostituzione all'indietro in $O(n^2)$.
- **Fattorizzazione LU**: $A = LU$ dove $L$ è triangolare inferiore e $U$ triangolare superiore; i moltiplicatori di Gauss formano $L$.
- **Pivoting parziale**: riordina le righe per evitare divisioni per pivot piccoli → stabilità numerica. Produce $PA = LU$ con $P$ matrice di permutazione.
- **Fill-in**: nelle matrici sparse, l'eliminazione di Gauss può riempire elementi nulli → riordinamento delle righe/colonne per minimizzarlo.
- Il costo dominante è la **fase di riduzione**: $\sim \frac{2}{3}n^3$ flop.

## Casi particolari

### A diagonale
$a_{ij} = 0$ se $i \neq j$. Soluzione immediata: $x_i = b_i / a_{ii}$ per $i = 1, \ldots, n$. Costo: $n$ divisioni.

### A triangolare inferiore ($a_{ij} = 0$ se $j > i$)
**Sostituzione in avanti**:
$$x_i = \frac{b_i - \sum_{j=1}^{i-1} a_{ij} x_j}{a_{ii}}, \quad i = 1, \ldots, n$$
Costo: $n^2$ operazioni elementari.

### A triangolare superiore ($a_{ij} = 0$ se $i > j$)
**Sostituzione all'indietro**:
$$x_i = \frac{b_i - \sum_{j=i+1}^{n} a_{ij} x_j}{a_{ii}}, \quad i = n, n-1, \ldots, 1$$
Costo: $n^2$ operazioni elementari.

## Metodo di Eliminazione di Gauss (MEG)

**Idea**: trasformare $A^{(1)}\mathbf{x} = \mathbf{b}^{(1)}$ (con $A^{(1)} = A$) in una sequenza di sistemi equivalenti fino a ottenere $A^{(n)}\mathbf{x} = \mathbf{b}^{(n)}$ con $A^{(n)}$ triangolare superiore.

**Fase di riduzione** (passo $k$, $k = 1, \ldots, n-1$):
- Per ogni riga $i = k+1, \ldots, n$:
  - Calcola il **moltiplicatore**: $m_{ik} = \frac{a_{ik}^{(k)}}{a_{kk}^{(k)}}$ (il pivot è $a_{kk}^{(k)}$)
  - Aggiorna la riga $i$: riga$_i \leftarrow$ riga$_i - m_{ik} \cdot$ riga$_k$

Costo fase di riduzione: $\sim \frac{2}{3}n^3$ flop.

**Fase di soluzione**: sostituzione all'indietro su $A^{(n)}\mathbf{x} = \mathbf{b}^{(n)}$. Costo: $n^2$ flop.

## Fattorizzazione LU

L'eliminazione di Gauss produce implicitamente la fattorizzazione $A = LU$:
- $U = A^{(n)}$ (triangolare superiore)
- $L$ = matrice triangolare inferiore con 1 sulla diagonale e $l_{ij} = m_{ij}$ per $i > j$

**Vantaggi**: se si deve risolvere $A\mathbf{x} = \mathbf{b}$ per più vettori $\mathbf{b}$ con la stessa $A$:
1. Fattorizza $A = LU$ una volta: $O(n^3/3)$
2. Per ogni $\mathbf{b}$: risolvi $L\mathbf{y} = \mathbf{b}$ e poi $U\mathbf{x} = \mathbf{y}$: $O(n^2)$ per ciascuno

## Pivoting parziale

Se il pivot $a_{kk}^{(k)} = 0$ (o è molto piccolo), il metodo fallisce o è instabile. Soluzione: **scambio di righe** (pivoting parziale) → si sceglie come pivot l'elemento di modulo massimo nella colonna $k$ sotto la diagonale.

Produce la fattorizzazione $PA = LU$ con $P$ matrice di permutazione. In MATLAB: `[L,U,P] = lu(A)`.

## Fill-in e matrici sparse

Nelle matrici sparse, la fase di riduzione può introdurre elementi non nulli dove prima erano zero (**fill-in**). Per limitarlo si usano riordinamenti (es. Cuthill-McKee, minimum degree).

## Articoli correlati

- [[sistemi-lineari-intro]]
- [[metodi-iterativi-sl]]
- [[sistemi-non-lineari]]

## Fonti

- `raw/5 - SL-metodi-diretti.pdf` — appunti lezione 15/10/24
- `raw/sl_diretti1.pdf`, `raw/fattorizzazione_lu.pdf` — materiale integrativo
- `raw/sl_meg_pivot.pdf` — pivoting e stabilità
- `raw/sl_fillin.pdf`, `raw/sl_sparse.pdf` — fill-in e matrici sparse
