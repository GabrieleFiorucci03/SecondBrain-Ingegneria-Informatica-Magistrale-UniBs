---
tags: [hci, design, concettuale, metafora, modello-concettuale]
data_creazione: 2026-05-29
data_aggiornamento: 2026-05-29
fonti:
  - raw/16-Progettazione-concettuale_COMPILED.pdf
---

# Progettazione concettuale

La progettazione concettuale (conceptual design) definisce *cosa* farà il sistema e *come* si comporterà ad alto livello, senza ancora specificare l'aspetto fisico dell'interfaccia. È il ponte tra i requisiti utente e le soluzioni di design fisico.

## Punti chiave

- Il modello concettuale descrive il sistema dal punto di vista dell'utente: quali concetti esistono, come sono correlati, come si comportano.
- Le **metafore** d'interfaccia collegano concetti familiari all'utente con funzionalità del sistema (es. desktop, cestino, carrello della spesa).
- Una buona metafora riduce il tempo di apprendimento sfruttando conoscenze pregresse.
- Il modello concettuale deve matchare il modello mentale degli utenti target.
- Prima di passare al design fisico, il modello concettuale deve essere validato con utenti.

## Modello concettuale

Descrive:
- Gli **oggetti** (entità) con cui l'utente interagisce.
- Gli **attributi** di ciascun oggetto.
- Le **relazioni** tra oggetti.
- Le **azioni** che l'utente può compiere sugli oggetti.
- Il **comportamento** del sistema in risposta alle azioni.

Approcci per costruire il modello concettuale:
- **Activity-based**: il design è organizzato intorno alle attività degli utenti.
- **Object-based**: il design è organizzato intorno agli oggetti del dominio (simile all'OOP).
- **OOUX** (Object-Oriented UX): prima si identificano gli oggetti del dominio, poi le azioni su di essi.

## Metafore d'interfaccia

Una metafora di interfaccia usa la conoscenza che l'utente ha di un dominio familiare (fonte) per comprendere un sistema informatico (target).

**Esempi**:
- Desktop: scrivania con documenti, cartelle, cestino.
- Carrello della spesa: per gli e-commerce.
- Busta / Posta in arrivo: per le e-mail.

**Limiti delle metafore**: una metafora porta anche elementi indesiderati (es. il "desktop" non ha la finzione di un cestino infinito come quello reale). Le metafore possono creare aspettative errate se la similarità è parziale.

## Interaction paradigms

I paradigmi di interazione definiscono il contesto d'uso fondamentale del sistema:
- **Desktop computing**: paradigma WIMP classico.
- **Ubiquitous computing**: sistema embedded nell'ambiente fisico.
- **Mobile computing**: vincoli di schermo piccolo e uso in movimento.
- **Tangible interfaces**: oggetti fisici come controlli (es. LEGO Mindstorms).
- **Social computing**: interazione mediata e in gruppo.

## Analisi funzionale

Prima di progettare concettualmente, si identificano le funzionalità necessarie attraverso:
- Analisi dei requisiti utente.
- Scenari d'uso.
- **CRC cards** (Class-Responsibility-Collaborator): tecnica per identificare oggetti, le loro responsabilità e le loro relazioni; usata anche in HCI per il conceptual design.

## Articoli correlati

- [[ciclo-di-vita]]
- [[progettazione-fisica]]
- [[design-pattern-hci]]
- [[utente]]

## Fonti

- raw/16-Progettazione-concettuale_COMPILED.pdf
