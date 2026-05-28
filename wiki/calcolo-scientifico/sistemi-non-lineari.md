---
tags: [calcolo-scientifico, sistemi-non-lineari, newton, broyden]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/3 - Sistemi-non-Lin.pdf
  - raw/sisnonlin.pdf
  - raw/sisnonlin_gnss.pdf
---

# Sistemi Non Lineari

Estensione del problema di ricerca di radici a funzioni vettoriali $\mathbf{F}: \mathbb{R}^n \to \mathbb{R}^n$. Si cerca $\boldsymbol{\alpha} \in \mathbb{R}^n$ tale che $\mathbf{F}(\boldsymbol{\alpha}) = \mathbf{0}$.

## Punti chiave

- Il problema si formula come: trovare $\boldsymbol{\alpha} = [\alpha_1, \ldots, \alpha_n]^T$ tale che $f_i(\alpha_1, \ldots, \alpha_n) = 0$ per $i = 1, \ldots, n$.
- Il **metodo di Newton per sistemi** sostituisce $f'(x^{(k)})$ con la **matrice Jacobiana** $J_F(x^{(k)})$ e calcola lo step risolvendo il sistema lineare $J_F \mathbf{z} = -\mathbf{F}$.
- Non si calcola mai $J_F^{-1}$ esplicitamente: si risolve $Az = b$ con $A = J_F$, $b = -F$ (comando `\` in MATLAB).
- **Convergenza di Newton**: ordine 2 se $\alpha$ è radice semplice ($\det J_F(\alpha) \neq 0$); ordine 1 se multipla.
- Il **metodo di Broyden** è un'approssimazione quasi-Newton: aggiorna $B_k \approx J_F(x^{(k)})$ senza ricalcolare la Jacobiana, con convergenza superlineare ($1 < p < 2$).
- La risoluzione del sistema lineare interno è il **collo di bottiglia** computazionale → vedi [[metodi-diretti-sl]].

## Formulazione del problema

Sistema di $n$ equazioni non lineari in $n$ incognite:
$$\begin{cases} f_1(x_1, x_2, \ldots, x_n) = 0 \\ f_2(x_1, x_2, \ldots, x_n) = 0 \\ \vdots \\ f_n(x_1, x_2, \ldots, x_n) = 0 \end{cases}$$

In forma compatta: trovare $\boldsymbol{\alpha} \in \mathbb{R}^n$ tale che $\mathbf{F}(\boldsymbol{\alpha}) = \mathbf{0}$, con $\mathbf{F}: \mathbb{R}^n \to \mathbb{R}^n$.

## Metodo di Newton per Sistemi

L'iterazione scalare $x^{(k+1)} = x^{(k)} - \frac{f(x^{(k)})}{f'(x^{(k)})}$ diventa:
$$\mathbf{x}^{(k+1)} = \mathbf{x}^{(k)} - J_F^{-1}(\mathbf{x}^{(k)}) \mathbf{F}(\mathbf{x}^{(k)}), \quad k \geq 0$$

dove la **matrice Jacobiana** è:
$$J_F(\mathbf{x}^{(k)}) = \begin{bmatrix} \frac{\partial f_1}{\partial x_1} & \cdots & \frac{\partial f_1}{\partial x_n} \\ \vdots & \ddots & \vdots \\ \frac{\partial f_n}{\partial x_1} & \cdots & \frac{\partial f_n}{\partial x_n} \end{bmatrix}_{\mathbf{x}=\mathbf{x}^{(k)}}$$

**Implementazione pratica** (ad ogni iterazione $k$):
1. Valuta $A = J_F(\mathbf{x}^{(k)})$
2. Valuta $\mathbf{b} = -\mathbf{F}(\mathbf{x}^{(k)})$
3. Risolvi $A\mathbf{z} = \mathbf{b}$ → ottieni $\mathbf{z}$ (mai calcolare $A^{-1}$ esplicitamente)
4. Aggiorna $\mathbf{x}^{(k+1)} = \mathbf{x}^{(k)} + \mathbf{z}$
5. Calcola err $= \|\mathbf{z}\|$; se err $< $ tol, fermati

**Pseudocodice** (`newtonsys.m`): input $f, J_f, x_0, tol, k_{max}$; output zero, $f$(zero), n. iterazioni.

**Teorema di convergenza**: se $\mathbf{F} \in C^2$, $J_F(\boldsymbol{\alpha})$ non singolare, e $\mathbf{x}^{(0)}$ sufficientemente vicino ad $\boldsymbol{\alpha}$, allora Newton converge con ordine 2 (radice semplice) o 1 (multipla).

## Metodo di Broyden

Alternativa che evita il ricalcolo della Jacobiana ad ogni passo:
$$\mathbf{x}^{(k+1)} = \mathbf{x}^{(k)} - B_k^{-1} \mathbf{F}(\mathbf{x}^{(k)}), \quad k \geq 0$$

con $B_0$ assegnata (es. $B_0 = I$) e aggiornamento:
$$B_{k+1} = B_k + \frac{\mathbf{F}(\mathbf{x}^{(k+1)})(\mathbf{x}^{(k+1)} - \mathbf{x}^{(k)})^T}{\|\mathbf{x}^{(k+1)} - \mathbf{x}^{(k)}\|^2}$$

$B_k$ approssima $J_F(\mathbf{x}^{(k)})$ nel senso che $B_k(\mathbf{x}^{(k)} - \boldsymbol{\alpha})$ è una buona approssimazione di $J_F(\mathbf{x}^{(k)})(\mathbf{x}^{(k)} - \boldsymbol{\alpha})$.

**Convergenza**: locale e superlineare ($1 < p < 2$); richiede $\mathbf{x}^{(0)}$ vicino ad $\boldsymbol{\alpha}$.

## Articoli correlati

- [[equazioni-non-lineari]]
- [[metodi-diretti-sl]]
- [[sistemi-lineari-intro]]

## Fonti

- `raw/3 - Sistemi-non-Lin.pdf` — slide del corso (Prof.ssa Gervasio, UniBS)
- `raw/sisnonlin.pdf`, `raw/sisnonlin_gnss.pdf` — materiale integrativo ed esempi applicativi
