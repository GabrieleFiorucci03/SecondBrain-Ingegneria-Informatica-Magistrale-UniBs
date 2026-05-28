---
tags: [calcolo-scientifico, sistemi-lineari, algebra-lineare]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/4 - sl_intro.pdf
  - raw/sl_inv_det.pdf
  - raw/sl_stima_a_priori.pdf
---

# Sistemi Lineari — Introduzione

Un sistema lineare quadrato $A\mathbf{x} = \mathbf{b}$ con $A \in \mathbb{R}^{n \times n}$, $\mathbf{b} \in \mathbb{R}^n$ è uno dei problemi centrali del calcolo scientifico: emerge nell'ottimizzazione, nella simulazione numerica, nella risoluzione di equazioni differenziali e come sottoproblema del metodo di Newton per sistemi non lineari.

## Punti chiave

- **Esistenza e unicità**: $\exists!$ soluzione $\Leftrightarrow$ $\det(A) \neq 0$ $\Leftrightarrow$ $\exists A^{-1}$ $\Leftrightarrow$ $\text{rank}(A) = n$ $\Leftrightarrow$ $A\mathbf{x} = \mathbf{0} \Rightarrow \mathbf{x} = \mathbf{0}$.
- I metodi si dividono in **diretti** (soluzione esatta in numero finito di operazioni, a meno di roundoff) e **iterativi** (approssimano la soluzione come limite di una successione).
- I metodi diretti garantiscono $\frac{\|\mathbf{x} - \hat{\mathbf{x}}\|}{\|\mathbf{x}\|} \leq C\varepsilon_M$; gli iterativi $\leq C\varepsilon_M + \varepsilon$ (dove $\varepsilon$ è la tolleranza di arresto).
- Applicazioni: circuiti elettrici (leggi di Kirchhoff), equazioni differenziali alle derivate parziali, ottimizzazione multivariabile, ogni iterazione del metodo di Newton.
- Mai calcolare $A^{-1}$ esplicitamente: è computazionalmente costoso e numericamente instabile; si risolve $A\mathbf{z} = \mathbf{b}$ direttamente.

## Formulazione

$$A\mathbf{x} = \mathbf{b}, \quad A \in \mathbb{R}^{n \times n}, \; \mathbf{b} \in \mathbb{R}^n$$

**Teorema (esistenza e unicità)**: le seguenti condizioni sono equivalenti:
1. $\exists!$ soluzione $\mathbf{x}$ di $A\mathbf{x} = \mathbf{b}$
2. $\det(A) \neq 0$
3. $\exists A^{-1}: AA^{-1} = A^{-1}A = I$
4. $\text{rank}(A) = n$
5. $A\mathbf{x} = \mathbf{0} \Rightarrow \mathbf{x} = \mathbf{0}$

## Metodi Diretti vs Iterativi

| | **Diretti** | **Iterativi** |
|---|---|---|
| Approccio | Trasformano $A\mathbf{x}=\mathbf{b}$ in sistema equivalente più semplice | Usano $A$ in prodotti matrice-vettore senza modificarla |
| Soluzione | Esatta a meno di roundoff, in numero finito di passi | $\mathbf{x} = \lim_{k\to\infty} \mathbf{x}^{(k)}$, test d'arresto richiesto |
| Errore | $\frac{\|\mathbf{x}-\hat{\mathbf{x}}\|}{\|\mathbf{x}\|} \leq C\varepsilon_M$ | $\frac{\|\mathbf{x}-\hat{\mathbf{x}}\|}{\|\mathbf{x}\|} \leq C\varepsilon_M + \varepsilon$ |
| Preferiti quando | $n$ moderato, matrice densa | $n$ grande, matrice sparsa |

## Esempio applicativo: circuiti elettrici

Le leggi di Kirchhoff (ai nodi: somma correnti = 0; alle maglie: somma tensioni = 0) generano un sistema lineare $A\mathbf{i} = \mathbf{b}$ dove le incognite sono le intensità di corrente $i_1, \ldots, i_n$.

## Articoli correlati

- [[metodi-diretti-sl]]
- [[metodi-iterativi-sl]]
- [[sistemi-non-lineari]]
- [[minimi-quadrati]]

## Fonti

- `raw/4 - sl_intro.pdf` — slide introduttive sui sistemi lineari (Prof.ssa Gervasio, UniBS)
- `raw/sl_inv_det.pdf` — proprietà di invertibilità e determinante
- `raw/sl_stima_a_priori.pdf` — stime a priori dell'errore
