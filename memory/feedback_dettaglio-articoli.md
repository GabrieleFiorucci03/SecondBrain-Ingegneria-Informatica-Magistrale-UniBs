---
name: feedback-dettaglio-articoli
description: Gli articoli wiki devono avere alta granularità e buon livello di dettaglio per la comprensione da parte di agenti AI
metadata:
  type: feedback
---

Gli articoli della wiki del Second Brain devono mantenere **alta granularità e un buon livello di dettaglio** (densi, completi, non sintetici).

**Why:** L'utente vuole che le letture future da parte di agenti AI permettano di comprendere a pieno il contesto. Un articolo troppo sintetico costringerebbe l'agente a risalire alle fonti grezze, vanificando lo scopo della KB ("ogni articolo comprensibile senza dover risalire alle fonti", come da CLAUDE.md).

**How to apply:** Quando si compila materiale (workflow COMPILE), preferire articoli ricchi e dettagliati. Per moduli/PDF molto ampi, splittare in più articoli focalizzati su un singolo concetto e collegarli con `[[wikilink]]`, invece di comprimere tutto o di sacrificare il dettaglio. Confermato dopo aver prodotto articoli densi per i moduli Server e ServerLinux. Collegato a [[feedback-workflow-compile]].
