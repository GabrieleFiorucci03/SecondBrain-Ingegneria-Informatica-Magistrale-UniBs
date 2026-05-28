---
name: feedback-workflow-compile
description: Durante il workflow COMPILE elaborare un file alla volta, completamente, prima di passare al successivo
metadata:
  type: feedback
---

Quando si esegue il workflow `compile` su più file in `raw/`, processare **un file alla volta in modo completo**: leggere il file, scrivere/aggiornare gli articoli wiki, citare la fonte, aggiornare gli indici e rinominare `_COMPILED`, e solo allora passare al file successivo.

**Why:** L'utente preferisce mantenere un contesto più focalizzato per ogni file, così da estrarre più informazioni e produrre articoli più ricchi. Evitare di leggere tutti i file in blocco e poi scrivere.

**How to apply:** Vale per il progetto Second Brain (`SecondBrainUnibsIngegneriaInformaticaMagistrale`). Non leggere in anticipo l'intero batch di `raw/`; chiudere il ciclo lettura→scrittura→indici→rename per ogni singolo file prima di iniziare il prossimo.
