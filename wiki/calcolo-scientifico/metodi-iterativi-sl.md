---
tags: [calcolo-scientifico, sistemi-lineari, metodi-iterativi, jacobi, gauss-seidel]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/6 - SL-Metodi-iterativi.pdf
  - raw/sl_iter1.pdf
  - raw/sl_iterativi.pdf
---

# Metodi Iterativi per Sistemi Lineari

I metodi iterativi approssimano la soluzione di $A\mathbf{x} = \mathbf{b}$ tramite una successione $\{\mathbf{x}^{(k)}\}$ con $\lim_{k\to\infty} \mathbf{x}^{(k)} = \mathbf{x}$. Non modificano $A$, ma la usano in prodotti matrice-vettore. Preferiti per sistemi grandi e sparsi.

## Punti chiave

- Struttura generale: $\mathbf{x}^{(k+1)} = B\mathbf{x}^{(k)} + \mathbf{g}$, dove $B$ è la **matrice di iterazione**.
- Convergenza iff $\rho(B) < 1$ (raggio spettrale), cioè tutti gli autovalori di $B$ hanno modulo $< 1$.
- **Metodo di Jacobi**: $B_J = -D^{-1}(L+U)$; aggiorna tutte le componenti usando i valori al passo $k$.
- **Metodo di Gauss-Seidel**: $B_{GS} = -(D+L)^{-1}U$; usa immediatamente i valori aggiornati → tipicamente converge più velocemente di Jacobi.
- Convergenza garantita se $A$ è **diagonalmente dominante** per righe: $|a_{ii}| > \sum_{j \neq i} |a_{ij}|$.
- **Test d'arresto**: $\frac{\|\mathbf{x}^{(k+1)} - \mathbf{x}^{(k)}\|}{\|\mathbf{x}^{(k)}\|} < \varepsilon$ (criterio relativo sull'incremento).
- Errore finale: $\frac{\|\mathbf{x} - \hat{\mathbf{x}}\|}{\|\mathbf{x}\|} \leq C\varepsilon_M + \varepsilon$ dove $\varepsilon$ è la tolleranza del test d'arresto.

## Struttura Generale

Decomposizione di $A = D + L + U$ dove:
- $D$: parte diagonale
- $L$: parte triangolare inferiore (strettamente, zeri sulla diagonale)
- $U$: parte triangolare superiore (strettamente)

Il metodo iterativo ha la forma:
$$P\mathbf{x}^{(k+1)} = (P - A)\mathbf{x}^{(k)} + \mathbf{b}$$
$$\mathbf{x}^{(k+1)} = (I - P^{-1}A)\mathbf{x}^{(k)} + P^{-1}\mathbf{b} = B\mathbf{x}^{(k)} + \mathbf{g}$$

$P$ è scelta in modo che $P^{-1}$ sia facile da calcolare.

## Metodo di Jacobi

Scelta: $P = D$.
$$x_i^{(k+1)} = \frac{1}{a_{ii}}\left(b_i - \sum_{j \neq i} a_{ij} x_j^{(k)}\right), \quad i = 1, \ldots, n$$

Matrice di iterazione: $B_J = -D^{-1}(L+U) = I - D^{-1}A$.

Tutte le componenti vengono aggiornate usando i valori **al passo $k$** → parallelizzabile.

## Metodo di Gauss-Seidel

Scelta: $P = D + L$.
$$x_i^{(k+1)} = \frac{1}{a_{ii}}\left(b_i - \sum_{j=1}^{i-1} a_{ij} x_j^{(k+1)} - \sum_{j=i+1}^{n} a_{ij} x_j^{(k)}\right)$$

Usa immediatamente i valori $x_j^{(k+1)}$ già calcolati nello stesso passo. Matrice di iterazione: $B_{GS} = -(D+L)^{-1}U$.

Tipicamente **più veloce** di Jacobi perché usa informazioni più aggiornate, ma non parallelizzabile come Jacobi.

## Convergenza

**Teorema**: il metodo $\mathbf{x}^{(k+1)} = B\mathbf{x}^{(k)} + \mathbf{g}$ converge $\forall \mathbf{x}^{(0)}$ se e solo se $\rho(B) < 1$, dove $\rho(B) = \max_i |\lambda_i(B)|$ è il raggio spettrale.

**Condizione sufficiente**: se $A$ è **diagonalmente dominante per righe** ($|a_{ii}| > \sum_{j \neq i} |a_{ij}|$ $\forall i$), allora Jacobi e Gauss-Seidel convergono.

La velocità di convergenza è determinata da $\rho(B)$: più è piccolo, più veloce la convergenza.

## Articoli correlati

- [[sistemi-lineari-intro]]
- [[metodi-diretti-sl]]

## Fonti

- `raw/6 - SL-Metodi-iterativi.pdf` — slide del corso
- `raw/sl_iter1.pdf`, `raw/sl_iterativi.pdf` — materiale integrativo
