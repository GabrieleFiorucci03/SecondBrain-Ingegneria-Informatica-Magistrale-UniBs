---
tags: [hci, ai, hcai, intelligenza-artificiale, responsible-ai]
data_creazione: 2026-05-29
data_aggiornamento: 2026-05-29
fonti:
  - raw/23-HCI-vs-AI_COMPILED.pdf
---

# HCI e AI

L'evoluzione dell'intelligenza artificiale sta trasformando sia il prodotto (i sistemi con cui gli utenti interagiscono) sia il processo (i metodi usati per progettarli). Il framework HCAI (Human-Centered AI) di Shneiderman è il principale riferimento per un'AI centrata sull'essere umano.

## Punti chiave

- **HCAI** (Human-Centered AI): l'AI deve amplificare, aumentare, potenziare ed empower gli utenti — non sostituirli (Shneiderman, 2020).
- Il framework bidimensionale HCAI mappa i sistemi su due assi: Human Control e Computer Automation. Il quadrante ottimale è alto-alto: alta automazione + alto controllo umano.
- L'AI entra nel processo HCD in tutte le fasi: ricerca utenti, progettazione, prototipazione, valutazione.
- **Synthetic evaluation**: usare AI generativa per simulare utenti — pratica emergente con pro e contro etici.
- I sistemi AI-based presentano nuove sfide per i golfi di esecuzione e valutazione.
- L'AI antropomorfica può generare **overtrust** e dipendenze non salutari.

## Tre cambi di prospettiva

1. AI tradizionale → **Human-Centered AI** (HCAI): l'umano al centro, non l'algoritmo.
2. Human-centered design con tecniche tradizionali → **HCD con l'aiuto dell'AI**.
3. Human-computer interaction → **Human-AI interaction**.

## Il framework HCAI bidimensionale (Shneiderman 2020)

Due dimensioni indipendenti — non un trade-off:
- **Human Control** (asse Y): quanto controllo hanno gli umani sul sistema?
- **Computer Automation** (asse X): quanto è automatizzato il sistema?

Il quadrante **alto Human Control + alta Computer Automation** = sistemi *reliable, safe & trustworthy*:
- **Reliable**: produce risposte corrette/attese.
- **Safe**: previene conseguenze catastrofiche; evita controllo umano o automazione eccessivi.
- **Trustworthy**: valutato da un ente di supervisione indipendente.

AI tradizionale mira solo all'alta automazione (sistemi autonomi). HCAI mira ad entrambi.

## AI nel processo di Human-Centered Design

| Fase HCD | Applicazioni AI |
|---|---|
| Comprendere il contesto | AI per analisi culturale, user behavior, definizione del problema |
| Specificare requisiti | Generazione automatica di personas e scenari (es. ChatGPT, PersonAI/Figma) |
| Produrre soluzioni | Prototipi low-fi (Figma Make), generazione di codice, storyboard (Dall-E) |
| Valutare | Generazione di test di compito; synthetic evaluation |

## Synthetic Evaluation

Usare AI generativa per simulare utenti nei test di usabilità:
- **Pro**: risparmio di tempo, scale maggiori, inclusione di profili difficili da reclutare, sicurezza per sistemi safety-critical.
- **Contro**: rischio di simulare solo utenti WEIRD (Western, Educated, Industrialized, Rich, Democratic), bias nei dati di training, AI non ha intuizione ed empatia reale.
- Raccomandazione: combinare con test tradizionali su utenti reali.

## Interazione con sistemi AI-based

I sistemi AI-based combinano:
- **Interazioni implicite**: localizzazione, espressioni facciali, dati biometrici.
- **Dati storici**: storia delle interazioni e preferenze.
- **Interazioni esplicite multi-modali**: comandi vocali, testo, disegni.

Per personalizzare i sistemi (**sistemi adattivi**). Trade-off: benefici della personalizzazione (apprendibilità, accessibilità) vs problemi sociali (filter bubbles, privacy).

I **golfi di Norman** si amplificano con l'AI:
- Golfo dell'esecuzione: capire cosa si può fare con il sistema AI; il termine "intelligenza" può creare modelli mentali errati.
- Golfo della valutazione: capire se il sistema ha completato correttamente il task; serve **AI literacy**.

## Implicazioni etiche e Responsible AI

- L'AI non può avere intuizione, empatia e comprensione degli utenti.
- **Antropomorfismo**: gli agenti AI che parlano in linguaggio naturale possono generare overtrust.
- Servono approcci **multi-metodo** e studi a grande scala.
- **Responsible AI**: basata su metodi HCI e UX lungo tutto il ciclo di vita, con prospettiva socio-tecnica.
- **Explainability**: spiegazioni dell'AI devono essere facili da comprendere (testo, grafica, immagini); progettate con approccio user-centered (Question-Driven Design Process, Liao et al. 2021).

## Articoli correlati

- [[introduzione-hci]]
- [[modelli-di-interazione]]
- [[ciclo-di-vita]]
- [[accessibilita]]

## Fonti

- raw/23-HCI-vs-AI_COMPILED.pdf
