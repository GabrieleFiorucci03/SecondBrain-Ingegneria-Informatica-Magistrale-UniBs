---
tags: [calcolo-scientifico, integrazione-numerica, quadratura]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/10 - FormuleQuadratura.pdf
  - raw/11 - Differenze_finite.pdf
  - raw/quadratura1.pdf
  - raw/fdq-LG.pdf
---

# Quadratura Numerica

La quadratura numerica approssima l'integrale definito $\int_a^b f(x)\, dx$ quando $f$ non è integrabile analiticamente o è nota solo in un insieme finito di punti. Le formule di quadratura sono combinazioni lineari di valori di $f$ in opportuni nodi.

## Punti chiave

- Una formula di quadratura ha la forma $I_n(f) = \sum_{i=0}^n w_i f(x_i) \approx \int_a^b f(x)\, dx$, con $w_i$ pesi e $x_i$ nodi.
- Le formule di **Newton-Cotes** usano nodi equidistanti: rettangoli (ordine 1), trapezi (ordine 2), Simpson (ordine 4).
- Il **metodo dei trapezi composito** divide $[a,b]$ in $n$ sottointervalli e applica la regola dei trapezi su ciascuno: errore $O(h^2)$ con $h = (b-a)/n$.
- Il **metodo di Simpson composito**: usa parabole su coppie di intervalli, errore $O(h^4)$.
- Le formule di **Gauss-Legendre** (GQ) scelgono ottimalmente nodi e pesi per massimizzare il grado di esattezza: con $n+1$ nodi si integra esattamente polinomi di grado $\leq 2n+1$.
- Il **grado di esattezza** di una formula è il massimo grado del polinomio integrato esattamente.
- Le **differenze finite** permettono di approssimare derivate: $f'(x) \approx \frac{f(x+h)-f(x)}{h}$ (ordine 1) o $\frac{f(x+h)-f(x-h)}{2h}$ (ordine 2).

## Formule di Newton-Cotes

Basate su nodi equidistanti $x_i = a + ih$, $h = (b-a)/n$.

### Regola dei rettangoli (punto medio)
$$\int_a^b f(x)\, dx \approx (b-a) f\!\left(\frac{a+b}{2}\right)$$
Errore: $O(h^2)$ (grado di esattezza 1).

### Regola dei trapezi
$$\int_a^b f(x)\, dx \approx \frac{b-a}{2}[f(a) + f(b)]$$
Errore: $-\frac{(b-a)^3}{12} f''(\xi)$ per qualche $\xi \in (a,b)$.

### Regola di Simpson
$$\int_a^b f(x)\, dx \approx \frac{b-a}{6}\left[f(a) + 4f\!\left(\frac{a+b}{2}\right) + f(b)\right]$$
Errore: $O(h^5)$; grado di esattezza 3.

## Formule Composite

Per aumentare l'accuratezza si divide $[a,b]$ in $n$ sottointervalli di ampiezza $h = (b-a)/n$ e si applica la formula elementare su ciascuno.

**Trapezi composito**:
$$\int_a^b f(x)\, dx \approx \frac{h}{2}\left[f(x_0) + 2\sum_{i=1}^{n-1} f(x_i) + f(x_n)\right]$$
Errore globale: $O(h^2)$.

**Simpson composito** (n pari):
$$\int_a^b f(x)\, dx \approx \frac{h}{3}\left[f(x_0) + 4f(x_1) + 2f(x_2) + 4f(x_3) + \ldots + f(x_n)\right]$$
Errore globale: $O(h^4)$.

## Quadratura di Gauss-Legendre

Scelgono nodi $x_0, \ldots, x_n$ e pesi $w_0, \ldots, w_n$ in modo da integrare esattamente tutti i polinomi di grado $\leq 2n+1$. I nodi sono le radici del polinomio di Legendre di grado $n+1$.

Con $n+1$ nodi: grado di esattezza $2n+1$ (contro $n$ delle Newton-Cotes con stessi nodi).

## Differenze Finite

Approssimazioni delle derivate tramite valori di $f$ in punti vicini:

| Formula | Approssimazione | Errore |
|---------|----------------|--------|
| Differenza in avanti | $f'(x) \approx \frac{f(x+h)-f(x)}{h}$ | $O(h)$ |
| Differenza all'indietro | $f'(x) \approx \frac{f(x)-f(x-h)}{h}$ | $O(h)$ |
| Differenza centrata | $f'(x) \approx \frac{f(x+h)-f(x-h)}{2h}$ | $O(h^2)$ |
| Seconda derivata | $f''(x) \approx \frac{f(x+h)-2f(x)+f(x-h)}{h^2}$ | $O(h^2)$ |

Le differenze finite sono usate anche per discretizzare equazioni differenziali → [[equazioni-differenziali-ordinarie]].

## Articoli correlati

- [[minimi-quadrati]]
- [[equazioni-differenziali-ordinarie]]
- [[aritmetica-di-macchina]]

## Fonti

- `raw/10 - FormuleQuadratura.pdf` — formule di quadratura
- `raw/11 - Differenze_finite.pdf` — differenze finite
- `raw/quadratura1.pdf` — materiale integrativo
- `raw/fdq-LG.pdf` — quadratura di Gauss-Legendre
