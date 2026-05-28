---
tags: [calcolo-scientifico, approssimazione, minimi-quadrati, interpolazione, regressione]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/7 - Sistemi-sovradeterminati.pdf
  - raw/8 - Approssimazione-dati-fun.pdf
  - raw/minquad.pdf
  - raw/spline.pdf
  - raw/interpolazione1.pdf
  - raw/interpolazione2.pdf
  - raw/interpolazione3.pdf
  - raw/interpolazione_immagini.pdf
  - raw/9 - interpolazione-esempi.pdf
---

# Minimi Quadrati e Interpolazione

Due approcci per approssimare dati o funzioni con polinomi: i **minimi quadrati** (approssimazione, dati rumorosi) e l'**interpolazione** (il polinomio passa esattamente per i nodi). Entrambi si riducono a un sistema lineare.

## Punti chiave

- **Minimi quadrati**: dati $m$ punti $(x_i, y_i)$, cerca il polinomio $p_n$ di grado $n < m$ che minimizza $E(\mathbf{a}) = \sum_{i=1}^m (p_n(x_i) - y_i)^2$.
- La soluzione è il vettore dei coefficienti $\mathbf{a}^*$ che risolve le **equazioni normali**: $X^T X \mathbf{a} = X^T \mathbf{y}$ (con $X$ matrice di Vandermonde).
- **Interpolazione polinomiale**: dati $n+1$ punti distinti $(x_i, y_i)$, $i = 0, \ldots, n$, esiste un **unico** polinomio $p_n \in \mathbb{P}_n$ tale che $p_n(x_i) = y_i$.
- I **polinomi di Lagrange** $L_i(x) = \prod_{j \neq i} \frac{x - x_j}{x_i - x_j}$ forniscono la formula esplicita di interpolazione.
- **Fenomeno di Runge**: con nodi equidistanti e grado alto, l'interpolante può oscillare violentemente (male condizionato); si usa la **spline cubica** come alternativa robusta.
- La **spline cubica** è un polinomio a tratti di grado 3, continuo con derivate prime e seconde continue: migliore approssimazione locale, nessuna oscillazione.
- Sistemi sovradeterminati ($m \gg n$): $X^T X$ è simmetrica definita positiva → sistema normale ben posto.

## Approssimazione nel senso dei Minimi Quadrati

### Retta di regressione
Dati $(x_i, y_i)$, $i = 1, \ldots, m$, cerco $p_1(x) = a_1 x + a_2$ tale che:
$$E(\mathbf{a}) = \sum_{i=1}^m (a_1 x_i + a_2 - y_i)^2 = \min$$

$E(\mathbf{a})$ è un paraboloide convesso; il minimo si trova annullando il gradiente $\nabla E(\mathbf{a}^*) = \mathbf{0}$, ottenendo il sistema $2 \times 2$:
$$\begin{bmatrix} \sum x_i^2 & \sum x_i \\ \sum x_i & m \end{bmatrix} \begin{bmatrix} a_1 \\ a_2 \end{bmatrix} = \begin{bmatrix} \sum y_i x_i \\ \sum y_i \end{bmatrix}$$

### Caso generale: polinomio di grado $n$
Cerco $p_n(x) = a_1 x^n + a_2 x^{n-1} + \ldots + a_{n+1}$ che minimizza $E(\mathbf{a}) = \sum_i (p_n(x_i) - y_i)^2$.

Costruisco la **matrice di Vandermonde** $X \in \mathbb{R}^{m \times (n+1)}$ con $X_{ij} = x_i^{n+1-j}$. Le **equazioni normali** sono:
$$X^T X \mathbf{a} = X^T \mathbf{y}$$

$X^T X$ è simmetrica e definita positiva (se le $x_i$ sono distinte). In MATLAB si può usare la fattorizzazione QR su $X$.

## Interpolazione Polinomiale

Dati $n+1$ nodi distinti $x_0 < x_1 < \ldots < x_n$ e valori $y_0, \ldots, y_n$: cerco $p_n \in \mathbb{P}_n$ tale che $p_n(x_i) = y_i$ per $i = 0, \ldots, n$.

**Teorema**: esiste un unico polinomio interpolante di grado $\leq n$.

### Formula di Lagrange
$$p_n(x) = \sum_{i=0}^n y_i L_i(x), \quad L_i(x) = \prod_{j=0, j\neq i}^n \frac{x - x_j}{x_i - x_j}$$

I polinomi $L_i$ sono la base di Lagrange: $L_i(x_j) = \delta_{ij}$.

### Errore di interpolazione
Se $f \in C^{n+1}([a,b])$:
$$f(x) - p_n(x) = \frac{f^{(n+1)}(\xi)}{(n+1)!} \prod_{i=0}^n (x - x_i), \quad \xi \in (a,b)$$

### Fenomeno di Runge
Con nodi equidistanti e $n$ grande, l'errore può crescere (oscillazioni agli estremi). Rimedi:
- Usare **nodi di Chebyshev** (concentrati agli estremi)
- Usare la **spline cubica**

## Spline Cubica

Una spline cubica su $n+1$ nodi $x_0 < \ldots < x_n$ è una funzione $S(x) \in C^2([x_0, x_n])$ tale che $S|_{[x_i, x_{i+1}]} \in \mathbb{P}_3$ per ogni $i = 0, \ldots, n-1$.

Interpola i dati ($S(x_i) = y_i$) con continuità delle derivate prime e seconde. Richiede 2 condizioni aggiuntive (es. spline naturale: $S''(x_0) = S''(x_n) = 0$).

Ottimale per approssimazione smooth: evita oscillazioni, costo $O(n)$.

## Articoli correlati

- [[sistemi-lineari-intro]]
- [[metodi-diretti-sl]]
- [[quadratura-numerica]]

## Fonti

- `raw/7 - Sistemi-sovradeterminati.pdf`, `raw/8 - Approssimazione-dati-fun.pdf` — slide del corso
- `raw/minquad.pdf` — minimi quadrati
- `raw/interpolazione1.pdf`, `raw/interpolazione2.pdf`, `raw/interpolazione3.pdf` — interpolazione polinomiale
- `raw/spline.pdf` — spline cubiche
- `raw/interpolazione_immagini.pdf`, `raw/9 - interpolazione-esempi.pdf` — applicazioni ed esempi
