---
tags: [calcolo-scientifico, floating-point, errori-numerici]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/1 - Aritmetica-di-macchina.pdf
  - raw/aritmetica_macchina1.pdf
  - raw/cancellazione.pdf
---

# Aritmetica di Macchina

L'aritmetica di macchina studia come i numeri reali vengono rappresentati e manipolati in un calcolatore, e quali errori ne conseguono. Poiché l'insieme dei numeri floating point è finito e limitato, ogni operazione introduce potenziali errori di arrotondamento che si propagano nel calcolo.

## Punti chiave

- Un numero reale è rappresentato in **notazione scientifica (floating point)**: $x = (-1)^s \cdot 0.a_1 a_2 \ldots a_t \cdot \beta^e$, con $s \in \{0,1\}$, $a_1 \neq 0$, $L \leq e \leq U$.
- Il **sistema floating point** $\mathbb{F}(\beta, t, L, U)$ è simmetrico rispetto a 0, limitato, finito e non equispaziato (più denso vicino allo 0).
- Lo standard **IEEE 754**: singola precisione = 4 byte ($\beta=2$, 1 bit segno, 8 esponente, 23 mantissa); doppia precisione = 8 byte (1+11+52 bit, $t=53$).
- La **precisione di macchina** $\varepsilon_M = \beta^{1-t}$ è la distanza tra 1 e il successivo numero FP. Per MATLAB (doppia prec.): $\varepsilon_M \approx 2.22 \cdot 10^{-16}$.
- L'**unità di roundoff** $u = \frac{1}{2}\varepsilon_M$: l'errore relativo di rappresentazione soddisfa $\frac{|x - fl_t(x)|}{|x|} \leq u$.
- **Proprietà che cadono in $\mathbb{F}$**: non vale la proprietà associativa di $+$ e $\cdot$; lo zero neutro della somma non è unico.
- **Errori di cancellazione**: nella somma di numeri quasi opposti ($x \approx -y$) il coefficiente di amplificazione dell'errore $C_{x,y} = \frac{|x|+|y|}{|x+y|}$ può diventare enorme → la somma è un'operazione **instabile**.
- Il **prodotto** è un'operazione stabile: l'errore relativo è $\leq C \cdot u$ con $C \sim 3$, indipendente dai dati.

## Rappresentazione floating point

Un numero $x \in \mathbb{R}$ è rappresentato come:
$$x = (-1)^s \cdot 0.a_1 a_2 \ldots a_t \cdot \beta^e$$
- $\beta$: base (tipicamente 2)
- $t$: numero di cifre della mantissa (determina la precisione)
- $e$: esponente ($L \leq e \leq U$, determina il range)
- $a_1 \neq 0$ (normalizzazione)

MATLAB usa $\mathbb{F}(2, 53, -1021, 1024)$: $x_{min} \approx 2 \cdot 10^{-308}$, $x_{max} \approx 1.7 \cdot 10^{308}$.

Al di fuori del range: **overflow** (risultato $\pm\infty$ o NaN) o **underflow** (numeri troppo piccoli, arrotondati a 0).

## Precisione di macchina ed errore di arrotondamento

$$\varepsilon_M = \beta^{1-t}$$

è la distanza tra 1 e il più piccolo numero FP maggiore di 1. Se $x \notin \mathbb{F}$, viene approssimato con $fl_t(x)$:
$$\frac{|x - fl_t(x)|}{|x|} \leq \frac{1}{2}\varepsilon_M = u$$

Per MATLAB (doppia prec.): $\varepsilon_M \approx 2.22 \cdot 10^{-16}$, $u \approx 1.11 \cdot 10^{-16}$.

## Propagazione degli errori di arrotondamento

### Somma
Dati $\bar{x} = fl_t(x)$, $\bar{y} = fl_t(y)$, il risultato numerico è $fl_t(\bar{x}+\bar{y})$. L'errore relativo soddisfa:
$$\frac{|(x+y) - fl_t(\bar{x}+\bar{y})|}{|x+y|} \leq \left(\frac{|x|+|y|}{|x+y|} + 1\right) \cdot u$$

Se $x \approx -y$: $|x+y| \approx 0$ ma $|x|+|y| \gg 0$ → **errore di cancellazione**, il coefficiente $C_{x,y}$ può essere $\gg 1$. La somma è **instabile**.

### Prodotto
$$\frac{|(x \cdot y) - fl_t(\bar{x} \cdot \bar{y})|}{|x \cdot y|} \leq C \cdot u, \quad C \sim 3$$

Il prodotto è **stabile**: l'errore relativo è sempre piccolo, indipendentemente dai valori di $x$ e $y$.

## Proprietà che non valgono in $\mathbb{F}$

1. **Associatività** di $+$ e $\cdot$: $(a+b)+c \neq a+(b+c)$ in $\mathbb{F}$ (esempio: $a=b=10^{308}$, $c=-4\cdot10^{307}$ in doppia precisione).
2. **Unicità dello zero neutro**: in $\mathbb{F}(10,4,-5,6)$ con $x=1$ e $y=10^{-4}$: $x+y = x$ perché $y$ è troppo piccolo per influenzare la mantissa di $x$.

## Articoli correlati

- [[equazioni-non-lineari]]
- [[metodi-diretti-sl]]
- [[equazioni-differenziali-ordinarie]]

## Fonti

- `raw/1 - Aritmetica-di-macchina.pdf` — appunti lezione 17/09/24 e 24/09/24
- `raw/aritmetica_macchina1.pdf` — materiale integrativo
- `raw/cancellazione.pdf` — approfondimento errori di cancellazione
