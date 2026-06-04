---
tags: [digital-image-processing, colore, spazi-colore, cie, hdr]
data_creazione: 2026-06-04
data_aggiornamento: 2026-06-04
fonti:
  - raw/01-HumanVisualSystem_COMPILED.pdf
---

# Colore e Spazi di Colore

Il colore percepito nasce dall'interazione tra spettro fisico della luce e risposta dei coni del [[sistema-visivo-umano]]. Gli spazi di colore (CIE RGB, XYZ, xy, Lab) formalizzano questa mappatura per misurare, riprodurre e gestire il colore in modo dispositivo-indipendente.

## Punti chiave
- La **teoria tricromatica**: 3 tipi di coni (S/M/L ≈ B/G/R) → ogni colore percepito è somma di tre primarie; colori fisici diversi possono dare le stesse primarie (**metamerismo**).
- Lo spazio **CIE 1931 XYZ** usa color matching function sempre ≥ 0; Y coincide con l'efficienza luminosa fotopica (luminanza).
- Il **diagramma di cromaticità xy** separa un colore in luminosità (Y) e cromaticità (x,y), con x+y+z=1.
- Lo spazio **CIE Lab** è color-opponent (L = lightness, a,b = dimensioni opponenti), basato sulla teoria del processo opponente.
- Immagini **LDR** (8 bpc, output-referred, es. JPEG/TIFF) vs **HDR** (32 bpc float, scene-referred, es. OpenEXR/RGBE).

## Definizioni fotometriche
- **LIGHT**: radiazione elettromagnetica che eccita la retina (spettro visibile ~380–770 nm); unità di energia luminosa = lumen·secondo.
- **Luminous flux**: potenza visibile (energia luminosa/tempo), misurata in lumen.
- **Luminous intensity**: flusso luminoso per angolo solido da un punto.
- **Luminance**: intensità luminosa per unità di area proiettata in una direzione (luce **riflessa**).
- **Illuminance**: flusso luminoso incidente su una superficie per unità di area (luce **incidente**).
- **Brightness**: attributo soggettivo (percepito, non misurato).
- **Lightness** (value/tone): proprietà di un colore che riflette la brightness percepita.

## Teoria tricromatica
- Tre tipi di coni R, G, B (S, M, L). Lo spettro dell'illuminante e la risposta semplificata dei coni determinano la percezione.
- I colori percepiti si riproducono come "addizione" di tre valori di primarie (SML, RGB...).
- **Metamerismo**: spettri fisici diversi producono gli stessi valori di primarie.

## Spazi CIE
- **CIE RGB (1931)**: R, G, B come integrali dello spettro I(λ) pesato per le color matching function r̄, ḡ, b̄ (che possono assumere valori negativi).
- **CIE XYZ (1931)**: X, Y, Z con color matching function x̄, ȳ, z̄ sempre ≥ 0 (semplificazione computazionale); ȳ(λ) = efficienza luminosa fotopica.
- **Cromaticità xy**: x = X/(X+Y+Z), y = Y/(X+Y+Z), z = 1−x−y; il colore = brightness Y + cromaticità (x,y).
- **CIE Lab**: spazio color-opponent derivato da XYZ; L = lightness, a e b = dimensioni colore opponente. Il **processo opponente** afferma che l'HVS percepisce il colore tramite elaborazione antagonista dell'informazione dei coni (contrapposto al modello tricromatico additivo).

## Dispositivi, gamut e gestione del colore
- Il **dynamic range** dei dispositivi varia: occhio >> proiettore > LCD > CRT (scala log cd/m²).
- **Color management (CMM)**: conversione tra spazi di riferimento. sRGB (gamut limitato, basso dinamico, livello driver) vs XYZ (profili colore, livello OS). Il **gamut mismatch** limita i colori riproducibili tra dispositivi (es. RGB vs CMYK).
- **Output-referred**: immagini destinate a un dispositivo (~sRGB, CMYK). **Scene-referred**: descrivono precisamente la scena (XYZ).

## Formazione dell'immagine digitale
Pipeline di una camera digitale: **Sensore** → readout/ISO → **interpolazione Bayer** ([[demosaicing]]) → white balance/contrasto/saturazione/sharpness → compressione JPEG. Il file **RAW** bypassa l'elaborazione e salva i dati grezzi del sensore con pattern **Bayer CFA**.

## Articoli correlati
- [[sistema-visivo-umano]]
- [[demosaicing]]
- [[quantizzazione-immagini]]

## Fonti
- `raw/01-HumanVisualSystem_COMPILED.pdf` — Prof. N. Adami, Digital Image Processing, "Human Visual System" (UniBS).
