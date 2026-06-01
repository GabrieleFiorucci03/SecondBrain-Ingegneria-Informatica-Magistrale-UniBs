---
tags: [hci, macchina, dispositivi, input, output, display]
data_creazione: 2026-05-29
data_aggiornamento: 2026-05-29
fonti:
  - raw/6-La macchina_COMPILED.pdf
---

# La macchina

Dal punto di vista dell'HCI, il "lato macchina" comprende tutti i dispositivi di input/output con cui l'utente interagisce fisicamente. La scelta dei dispositivi influenza profondamente gli stili di interazione possibili e i requisiti di design.

## Punti chiave

- I dispositivi di input traducono azioni fisiche dell'utente in segnali digitali; i dispositivi di output traducono segnali digitali in stimoli percepibili dall'utente.
- La qualità del display (risoluzione, dimensione, contrasto) influenza direttamente la leggibilità e l'usabilità.
- I dispositivi di puntamento (mouse, trackpad, touch) hanno caratteristiche diverse in termini di precisione e naturalezza.
- VR e AR introducono nuove sfide progettuali: immersione, cybersickness, interazione nello spazio 3D.
- Il design deve tenere conto delle caratteristiche fisiche dei dispositivi usati dagli utenti target.

## Dispositivi di input

| Tipo | Esempi | Caratteristiche |
|---|---|---|
| **Tastiera** | QWERTY, ergonomica | Alta velocità per testo; dipendente dalla memorizzazione |
| **Mouse** | Ottico, laser | Precisione; adatto al paradigma WIMP |
| **Trackball** | Kensington | Come il mouse ma stazionario; utile per disabilità motorie |
| **Joystick / Gamepad** | Xbox controller | Adatto per giochi, controllo di veicoli |
| **Touchscreen** | Smartphone, tablet | Diretto; multi-touch; impreciso rispetto al mouse |
| **Penna ottica / Stylus** | Apple Pencil | Alta precisione; adatto per disegno |
| **Microfono** | Riconoscimento vocale | Input in linguaggio naturale |
| **Webcam / Sensori** | Kinect, eye tracker | Input implicito: gesti, sguardo, postura |

## Dispositivi di output

**Display**:
- **CRT** (Cathode Ray Tube): obsoleto; emetteva radiazioni.
- **LCD** (Liquid Crystal Display): piatto, basso consumo, alta qualità.
- **OLED**: contrast ratio elevatissimo; ogni pixel emette luce propria.

Parametri chiave:
- **Risoluzione**: numero di pixel (es. 1920×1080 Full HD).
- **DPI/PPI**: pixel per pollice; valori alti (Retina display) rendono il testo più nitido.
- **Refresh rate**: Hz; valori alti (120Hz) riducono la motion blur.

**Altri output**:
- **Altoparlanti**: feedback sonoro, sintesi vocale.
- **Stampanti**: output fisico.
- **Feedback aptico**: vibrazione (smartphone), force feedback (gamepad).

## VR e AR

**VR** (Virtual Reality): immersione totale in un ambiente virtuale tramite visore (es. Meta Quest). Sfide: cybersickness (nausea da discrepanza tra movimento percepito e visivo), interazione nello spazio 3D, isolamento dall'ambiente fisico.

**AR** (Augmented Reality): overlay di contenuti digitali sul mondo reale (es. Microsoft HoloLens, ARKit). Sfide: calibrazione, occlusion, progettazione per ambienti variabili.

## Articoli correlati

- [[stili-di-interazione]]
- [[progettazione-fisica]]
- [[accessibilita]]

## Fonti

- raw/6-La macchina_COMPILED.pdf
