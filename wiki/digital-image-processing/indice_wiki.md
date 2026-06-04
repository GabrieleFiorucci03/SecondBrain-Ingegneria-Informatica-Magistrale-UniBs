# Digital Image Processing

Corso UniBS (Prof. Nicola Adami / Riccardo Leonardi) sull'elaborazione digitale delle immagini: percezione visiva e colore, trasformazioni geometriche, enhancement, filtraggio spaziale e in frequenza, morfologia, quantizzazione e demosaicing. Copre fondamenti teorici e implementazioni pratiche (convoluzione, FFT, OpenCV).

## Articoli

- [[sistema-visivo-umano]] — HVS: anatomia dell'occhio, fotorecettori, brightness adaptation, Weber ratio, contrasto simultaneo.
- [[colore-e-spazi-colore]] — Definizioni fotometriche, teoria tricromatica, metamerismo, spazi CIE (RGB/XYZ/xy/Lab), gamut, HDR/LDR.
- [[trasformazioni-geometriche-e-interpolazione]] — Trasformazioni lineari/affini, coordinate omogenee, inverse mapping, nearest neighbour e bilineare.
- [[equalizzazione-istogramma]] — Istogramma come distribuzione di probabilità, cumulativa, algoritmo di equalizzazione per il contrasto.
- [[filtraggio-spaziale]] — Filtri LSI e convoluzione, kernel, separabilità, padding, esempi (mean, sharpen, Sobel, LoG), mediano.
- [[filtraggio-in-frequenza]] — 2D-DFT/DSFT, convoluzione↔prodotto, FFT, zero padding, fftshift, filtri ideali low/high pass e fan.
- [[template-matching]] — Localizzazione di un pattern tramite correlazione, realizzazione in frequenza (F*·H).
- [[operatori-morfologici]] — Morfologia su insiemi, structuring element, erosione, dilatazione, opening, closing, contour, skeleton.
- [[quantizzazione-immagini]] — Quantizzazione scalare e vettoriale, errore/MSE, Lloyd-Max, LBG, SLIC superpixel.
- [[demosaicing]] — Bayer CFA, interpolazione bilineare, metodi gradient-based, interpolazione lineare Malvar-He-Cutler.
