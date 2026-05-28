---
tags: [calcolo-scientifico, ODE, equazioni-differenziali, eulero, crank-nicolson, runge-kutta]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/12 - Eqz_diff_ordinarie.pdf
  - raw/13 - Assoluta_stab.pdf
  - raw/14 - Metodi-adattivi.pdf
  - raw/15 - high_order.pdf
  - raw/eqdiff1.pdf
  - raw/eqdiff2.pdf
  - raw/eqdiff3.pdf
  - raw/eqdiff4.pdf
  - raw/eqdiff_adapt_es.pdf
---

# Equazioni Differenziali Ordinarie

L'obiettivo è approssimare numericamente la soluzione $y(t)$ del **problema di Cauchy** del 1° ordine. Si discretizza l'intervallo temporale e si costruisce una successione $\{u_m\} \approx \{y(t_m)\}$ tramite metodi a un passo o multi-passo.

## Punti chiave

- **Problema di Cauchy**: $y'(t) = f(t, y(t))$, $t \in (t_0, T)$, $y(t_0) = y_0$. Esiste un'unica soluzione se $f$ è continua e Lipschitziana in $y$.
- **Discretizzazione**: si fissano $N_h$ nodi $t_m = t_0 + mh$ con passo $h > 0$; si cerca $u_m \approx y(t_m)$.
- **Eulero in avanti** (esplicito): $u_{m+1} = u_m + h f(t_m, u_m)$. Semplice, ma condizionatamente stabile ($h$ deve essere piccolo).
- **Eulero all'indietro** (implicito): $u_{m+1} = u_m + h f(t_{m+1}, u_{m+1})$. Richiede la soluzione di un'equazione (non lineare se $f$ non è lineare); incondizionatamente stabile.
- **Crank-Nicolson**: media tra Eulero avanti e indietro; ordine 2, incondizionatamente stabile.
- **Stabilità assoluta**: un metodo è A-stabile se converge per ogni $h > 0$ sull'equazione test $y' = \lambda y$ con $\text{Re}(\lambda) < 0$.
- **Metodi adattivi**: cambiano $h$ durante l'integrazione per controllare l'errore locale con un numero minimo di valutazioni di $f$.
- **Metodi di Runge-Kutta**: metodi a un passo di ordine elevato (es. RK4: ordine 4, 4 valutazioni di $f$ per passo).

## Problema di Cauchy

$$\begin{cases} y'(t) = f(t, y(t)), & t \in (t_0, T) \\ y(t_0) = y_0 \end{cases}$$

**Teorema (esistenza e unicità)**: se $f$ è continua e $\exists L > 0$: $|f(t,y_1) - f(t,y_2)| \leq L|y_1 - y_2|$ (condizione di Lipschitz), allora $\exists!$ soluzione $y \in C^1(I)$.

**Obiettivo numerico**: costruire $\{u_0, u_1, \ldots, u_{N_h}\}$ con $u_m \approx y(t_m)$.

## Metodo di Eulero in Avanti (EE, esplicito)

Approssimazione della derivata con differenza finita in avanti:
$$y'(t_m) \approx \frac{y(t_{m+1}) - y(t_m)}{h}$$

Schema:
$$\begin{cases} u_{m+1} = u_m + h\, f(t_m, u_m), & m \geq 0 \\ u_0 = y_0 \end{cases}$$

**Errore di troncamento locale**: $O(h)$ (metodo del 1° ordine).
**Stabilità**: condizionata. Per l'equazione test $y' = \lambda y$ ($\text{Re}(\lambda) < 0$): stabile se $|1 + h\lambda| < 1$, cioè $h < \frac{2}{|\lambda|}$.

## Metodo di Eulero all'Indietro (EI, implicito)

Differenza finita all'indietro:
$$\begin{cases} u_{m+1} = u_m + h\, f(t_{m+1}, u_{m+1}), & m \geq 0 \\ u_0 = y_0 \end{cases}$$

Il valore $u_{m+1}$ compare a destra → equazione (non lineare in generale) da risolvere ad ogni passo con punto fisso o Newton/secanti.

**Ordine**: 1. **Stabilità**: **A-stabile** (incondizionatamente stabile per equazione test).

## Metodo di Crank-Nicolson (CN)

Media tra EE e EI (regola dei trapezi applicata a $f$):
$$u_{m+1} = u_m + \frac{h}{2}\left[f(t_m, u_m) + f(t_{m+1}, u_{m+1})\right]$$

**Ordine**: 2. **Stabilità**: A-stabile. È il bilanciamento ottimale tra accuratezza e stabilità per molte applicazioni.

Derivazione: integrazione del teorema fondamentale del calcolo su $(t_m, t_{m+1})$ e approssimazione con la regola dei trapezi.

## Stabilità Assoluta

Equazione test: $y' = \lambda y$ con $\lambda \in \mathbb{C}$, $\text{Re}(\lambda) < 0$. La soluzione esatta $y(t) = y_0 e^{\lambda t} \to 0$.

Un metodo è **A-stabile** se la sua soluzione numerica $u_m \to 0$ per ogni $h > 0$ e ogni $\lambda$ con $\text{Re}(\lambda) < 0$.

| Metodo | Stabilità |
|--------|-----------|
| Eulero avanti (EE) | Condizionata: $h|\lambda| < 2$ |
| Eulero indietro (EI) | A-stabile |
| Crank-Nicolson | A-stabile |
| RK4 | Condizionata |

## Metodi Adattivi

Il passo $h$ viene variato automaticamente per mantenere l'errore locale entro una tolleranza $\varepsilon_{tol}$ data. Strategia:
1. Calcola la soluzione con passo $h$ e con passo $h/2$
2. Stima l'errore locale dal confronto
3. Se errore $> \varepsilon_{tol}$: riduci $h$; se errore $\ll \varepsilon_{tol}$: aumenta $h$

Vantaggio: adatta automaticamente la risoluzione in zone critiche (soluzioni a variazione rapida).

## Metodi di Runge-Kutta

Metodi a un passo di ordine elevato che valutano $f$ in più punti intermedi. Il classico **RK4** (4 stadi, ordine 4):
$$\begin{aligned}
k_1 &= f(t_m, u_m) \\
k_2 &= f(t_m + h/2,\, u_m + hk_1/2) \\
k_3 &= f(t_m + h/2,\, u_m + hk_2/2) \\
k_4 &= f(t_m + h,\, u_m + hk_3) \\
u_{m+1} &= u_m + \frac{h}{6}(k_1 + 2k_2 + 2k_3 + k_4)
\end{aligned}$$

Errore globale: $O(h^4)$; costo: 4 valutazioni di $f$ per passo.

## Articoli correlati

- [[aritmetica-di-macchina]]
- [[equazioni-non-lineari]]
- [[quadratura-numerica]]
- [[sistemi-lineari-intro]]

## Fonti

- `raw/12 - Eqz_diff_ordinarie.pdf` — problema di Cauchy, Eulero, Crank-Nicolson
- `raw/13 - Assoluta_stab.pdf` — stabilità assoluta
- `raw/14 - Metodi-adattivi.pdf` — metodi adattivi
- `raw/15 - high_order.pdf` — metodi di ordine elevato (Runge-Kutta)
- `raw/eqdiff1.pdf` – `raw/eqdiff4.pdf` — materiale integrativo
- `raw/eqdiff_adapt_es.pdf` — esempi metodi adattivi
