---
tags: [hci, design, fisico, layout, tipografia, colore, fitts, goms]
data_creazione: 2026-05-29
data_aggiornamento: 2026-05-29
fonti:
  - raw/18-Progettazione-fisica-prima parte_COMPILED.pdf
  - raw/19-Progettazione-fisica-seconda parte_COMPILED.pdf
---

# Progettazione fisica

La progettazione fisica (physical/detailed design) definisce l'aspetto concreto dell'interfaccia: layout, tipografia, colori, icone, controlli interattivi. È la fase più visibile del design ma deve essere preceduta e guidata dal design concettuale.

## Punti chiave

- **Legge di Fitts**: il tempo per raggiungere un target dipende dalla distanza e dalla dimensione del target. Oggetti grandi e vicini sono più facili da cliccare.
- **Legge di Hick**: il tempo di scelta cresce logaritmicamente con il numero di opzioni. Meno opzioni → scelta più veloce.
- I **principi Gestalt** guidano il layout visivo: prossimità, similarità, continuità, chiusura.
- Il colore comunica significato ma non deve essere l'unico mezzo (per accessibilità ai daltonici).
- Il design deve rispettare le linee guida della piattaforma (HIG per Apple, Material Design per Google/Android).

## Legge di Fitts

> T = a + b · log₂(1 + D/W)

Dove D = distanza dal target, W = larghezza del target. Implicazioni:
- I controlli frequentemente usati devono essere grandi e vicini al cursore.
- Gli angoli e i bordi dello schermo sono target "infinitamente grandi" (il cursore si ferma al bordo) → ottimi per menu e bottoni frequenti (es. menu Apple in alto a sinistra).
- I target piccoli e distanti sono costosi: evitarli per azioni frequenti.

## Legge di Hick

> T = b · log₂(n + 1)

Dove n = numero di opzioni equivalenti. Implicazioni:
- Ridurre il numero di opzioni nei menu.
- Usare strutture gerarchiche per organizzare molte opzioni.
- Gli utenti esperti possono gestire più opzioni; i novizi no.

## Layout

- Usa **griglie** (grid system) per allineare e organizzare gli elementi.
- **Gerarchia visiva**: gli elementi più importanti devono emergere (dimensione, peso, colore, posizione).
- **Bilanciamento**: simmetrico (formale, stabile) vs asimmetrico (dinamico, moderno).
- **Spazio negativo** (white space): aiuta la leggibilità e riduce il carico cognitivo.
- **Principio F-pattern** per il testo: gli utenti leggono in un pattern a F; le informazioni importanti vanno in alto a sinistra.

## Tipografia

- Massimo 2-3 famiglie di caratteri per interfaccia.
- **Leggibilità**: dimensione minima 16px per il corpo del testo su schermo.
- **Interlinea**: almeno 1.5x per testi lunghi.
- Evitare testo tutto maiuscolo per paragrafi (difficile da leggere).
- Contrasto testo/sfondo: rapporto minimo 4.5:1 (WCAG AA).

## Colore

- Il colore guida l'attenzione e comunica stato/significato.
- Non usare il colore come unico mezzo di comunicazione (daltonismo).
- Palette: 60% colore dominante, 30% secondario, 10% accento.
- Feedback di stato: verde=successo, giallo=avviso, rosso=errore (convenzione consolidata).

## Icone

- Riconoscibili a colpo d'occhio; usare metafore visive consolidate.
- Accompagnare con etichette testuali quando non ovvie.
- Consistenti per dimensione e stile nell'intera interfaccia.

## Modelli predittivi: GOMS e KLM

**GOMS** (Goals, Operators, Methods, Selection rules): modello per prevedere il tempo di esecuzione di task da parte di utenti esperti. Scompone un task in obiettivi, operatori (azioni elementari), metodi e regole di selezione.

**KLM** (Keystroke-Level Model): versione semplificata di GOMS. Conta le azioni elementari (keystroke, puntamento, clic, attesa mentale) e ne somma i tempi tipici per stimare il tempo di un task.

Utili per confrontare alternative di design prima di costruire prototipi.

## Responsive Design

Il design deve adattarsi a dispositivi diversi (desktop, tablet, mobile):
- **Breakpoints**: soglie di larghezza schermo a cui il layout cambia.
- **Mobile-first**: progettare prima per schermi piccoli, poi espandere.
- **Touch targets**: almeno 44×44px per target toccabili su touchscreen.

## Articoli correlati

- [[progettazione-concettuale]]
- [[stili-di-interazione]]
- [[accessibilita]]
- [[design-pattern-hci]]

## Fonti

- raw/18-Progettazione-fisica-prima parte_COMPILED.pdf
- raw/19-Progettazione-fisica-seconda parte_COMPILED.pdf
