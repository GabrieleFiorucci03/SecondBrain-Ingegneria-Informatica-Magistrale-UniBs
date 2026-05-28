---
tags: [calcolo-scientifico, equazioni-non-lineari, metodi-iterativi]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/2 - Eqz-non-lineari.pdf
  - raw/eqnonlin1.pdf
  - raw/eqnonlin2.pdf
  - raw/eqnonlin3.pdf
  - raw/eqnonlin4.pdf
  - raw/eqnonlin5.pdf
  - raw/eqnonlin6.pdf
---

# Equazioni Non Lineari

Data $f: [a,b] \subset \mathbb{R} \to \mathbb{R}$ continua, si cerca $\alpha \in [a,b]$ tale che $f(\alpha) = 0$ (radice di $f$). I metodi principali sono: bisezione, Newton, secanti, punto fisso.

## Punti chiave

- **Bisezione**: richiede $f(a) \cdot f(b) < 0$, converge sempre con ordine 1 (lineare), numero di iterazioni prevedibile: $k \geq \log_2\!\left(\frac{b-a}{\varepsilon}\right) - 1$.
- **Newton**: $x^{(k+1)} = x^{(k)} - \frac{f(x^{(k)})}{f'(x^{(k)})}$; ordine 2 (quadratico) per radici semplici, ordine 1 per radici multiple (recuperabile moltiplicando per $m$).
- **Secanti**: sostituisce $f'(x^{(k)})$ con il rapporto incrementale; ordine $p \approx 1.618$ (superlineare), non richiede il calcolo della derivata.
- **Punto fisso**: $x^{(k+1)} = \varphi(x^{(k)})$; converge se $|\varphi'(\alpha)| < 1$.
- L'**ordine di convergenza** $p$ è definito da $|x^{(k+1)} - \alpha| \leq C |x^{(k)} - \alpha|^p$: maggiore è $p$, più veloce la convergenza.
- Una **radice è semplice** se $f(\alpha) = 0$ e $f'(\alpha) \neq 0$; **multipla** di molteplicità $m$ se $f(\alpha) = f'(\alpha) = \ldots = f^{(m-1)}(\alpha) = 0$ e $f^{(m)}(\alpha) \neq 0$.
- **Test d'arresto**: stimatore dell'errore tramite incremento $|x^{(k)} - x^{(k+1)}|$ o residuo $|f(x^{(k)})|$.

## Metodo di Bisezione

**Ipotesi**: $f$ continua su $[a,b]$, $f(a) \cdot f(b) < 0$ (per il teorema degli zeri esiste $\alpha \in (a,b)$).

**Algoritmo**: costruisce una successione di intervalli $I_0 \supset I_1 \supset I_2 \supset \ldots$ contenenti $\alpha$; i punti medi $c_k = \frac{a_k + b_k}{2}$ approssimano $\alpha$.

**Stima dell'errore**: $|c_k - \alpha| \leq \frac{b_k - a_k}{2} = \frac{b-a}{2^{k+1}}$

**Numero di iterazioni** per garantire $|c_k - \alpha| < \varepsilon$:
$$k \geq \log_2\!\left(\frac{b-a}{\varepsilon}\right) - 1$$

Convergenza lineare ($p=1$), robusta ma lenta.

## Metodo di Newton

**Formula**: dato $x^{(0)}$,
$$x^{(k+1)} = x^{(k)} - \frac{f(x^{(k)})}{f'(x^{(k)})}, \quad k \geq 0$$

Interpretazione geometrica: $x^{(k+1)}$ è lo zero della retta tangente a $f$ in $x^{(k)}$.

**Teorema di convergenza**: se $f \in C^2(I)$, $f'(x) \neq 0 \; \forall x \in I \setminus \{\alpha\}$, e $x^{(0)}$ sufficientemente vicino ad $\alpha$, allora Newton converge:
- con ordine 2 (**quadratica**) se $\alpha$ è radice semplice
- con ordine 1 (**lineare**) se $\alpha$ è radice multipla

**Variante per radici multiple**: moltiplicare per $m$ (molteplicità nota):
$$x^{(k+1)} = x^{(k)} - m \cdot \frac{f(x^{(k)})}{f'(x^{(k)})}$$
recupera la convergenza quadratica.

**Scelta di $x^{(0)}$**: deve soddisfare $f'(x) \neq 0$ nell'intorno $I_\delta(\alpha)$ scelto. Un $x^{(0)}$ lontano da $\alpha$ può portare divergenza.

## Metodo delle Secanti

Sostituisce la derivata $f'(x^{(k)})$ con il rapporto incrementale tra $x^{(k)}$ e $x^{(k-1)}$:
$$x^{(k+1)} = x^{(k)} - f(x^{(k)}) \cdot \frac{x^{(k)} - x^{(k-1)}}{f(x^{(k)}) - f(x^{(k-1)})}$$

Richiede due punti iniziali $x^{(0)}, x^{(1)}$. Ordine $p = \frac{1+\sqrt{5}}{2} \approx 1.618$. È estensibile a sistemi → vedi [[sistemi-non-lineari]].

## Metodo del Punto Fisso

Riscrivere $f(x) = 0$ come $x = \varphi(x)$. La successione $x^{(k+1)} = \varphi(x^{(k)})$ converge se $|\varphi'(\alpha)| < 1$.

L'ordine di convergenza dipende da $\varphi'(\alpha)$: se $\varphi'(\alpha) = 0$ la convergenza è almeno quadratica. Newton è un caso particolare con $\varphi_N(x) = x - \frac{f(x)}{f'(x)}$, per cui $\varphi_N'(\alpha) = 0$.

## Ordine di Convergenza

Sia $\{x^{(k)}\}$ con $\lim_{k\to\infty} x^{(k)} = \alpha$. Si dice che $x^{(k)}$ converge con **ordine $p$** ad $\alpha$ se $\exists C > 0, p \in \mathbb{R}^+$:
$$|x^{(k+1)} - \alpha| \leq C |x^{(k)} - \alpha|^p \quad \forall k \geq \bar{k}$$

| $p$ | Nome | Caratteristica |
|-----|------|----------------|
| 1 | Lineare | $C \in (0,1)$ richiesto per convergenza |
| $\approx 1.618$ | Superlineare | Metodo delle secanti |
| 2 | Quadratica | Newton su radici semplici; errore si quadra |

## Test d'arresto

Poiché $\alpha$ è incognita, si usano **stimatori dell'errore**:
1. **Incremento**: fermarsi quando $|x^{(k)} - x^{(k+1)}| < \varepsilon$. Funziona perché $(x^{(k)} - \alpha) \approx (x^{(k)} - x^{(k+1)})$ quando $C \approx 1$.
2. **Residuo**: fermarsi quando $|f(x^{(k)})| < \varepsilon$.

## Articoli correlati

- [[aritmetica-di-macchina]]
- [[sistemi-non-lineari]]
- [[metodi-diretti-sl]]

## Fonti

- `raw/2 - Eqz-non-lineari.pdf` — appunti lezioni 24/09/24 e 1/10/24
- `raw/eqnonlin1.pdf` – `raw/eqnonlin6.pdf` — materiale integrativo
