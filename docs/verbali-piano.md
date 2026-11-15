# Verbali di assemblea – piano di integrazione in HonorFix

Punto di partenza: `verbali/Verbali.html`, copia del prototipo "Verbali Assemblea – prototipo"
(versione del 15/11/2026). Oggi usa dati di esempio scritti nel file, salva solo nel browser
(localStorage/IndexedDB) e chiama Claude e la dettatura tramite il runtime di claude.ai
(`window.claude.use('sample' | 'dictation' | 'downloads')`), che fuori da claude.ai non esiste.

## Cosa c'è già nel foglio "HonorFix Unificato"

| Serve ai verbali | Dove si trova | Note |
|---|---|---|
| Condominio (nome, indirizzo, CF) | `Condomini` A–D | ID `C-NNN`, già usato da tutti i moduli |
| Chiusura esercizio | `Condomini` S | es. `30/09` → data del rendiconto |
| Persone (nome, email, PEC, CF) | `Persone` | ID `P-NNN`, collegate a **un solo** condominio (col. D) |
| Dati dello Studio e firma | `Impostazioni` | Ragione sociale, Amministratore, indirizzo |

## Cosa manca (e il prototipo richiede)

1. **Unità immobiliari**: oggi in `Persone!K` c'è solo un testo libero ("Unità").
2. **Tabelle millesimali e millesimi per unità**: non presenti.
3. **Titolarità**: chi è proprietario, usufruttuario, nudo proprietario, conduttore di ciascuna unità,
   e chi la rappresenta in assemblea. Una persona con unità in più condomìni oggi compare una volta sola.
4. **Assemblee e verbali**: nessuna scheda.

## Schede nuove proposte

Stesse regole del foglio `Schema`: colonna A = ID, collegamenti per ID, colonne `(JSON)` scritte solo dall'app.

**Unita** — `ID` (U-NNNN) · `ID Condominio` · `Condominio` · `Descrizione` (App. 3, Box 1) · `Scala` · `Piano` · `Interno` · `Ex dal`

**Tabelle_Millesimali** — `ID` (T-NNN) · `ID Condominio` · `Codice` (A, B, C…) · `Nome` (Generale, Ascensore…) · `Totale` (1000)

**Millesimi** — `ID Unità` · `ID Tabella` · `Millesimi`

**Titolari** — `ID Unità` · `ID Persona` · `Diritto` (Proprietario / Usufruttuario / Nudo proprietario / Conduttore) · `Rappresentante` (Sì/No) · `Dal` · `Al`

**Assemblee** — `ID` (ASS-AAAA-NNN) · `ID Condominio` · `Condominio` · `Tipo` (Ordinaria/Straordinaria) · `Esercizio` · `N. Verbale` · `Tabella` · `1ª conv. data/ora/luogo` · `2ª conv. data/ora/luogo` · `Convocazione tenuta` (1/2) · `Videoconferenza` · `Presidente` · `Segretario` · `Ora appello` · `Ora chiusura` · `Stato` (Preparazione/Svolta/Chiusa) · `OdG (JSON)` · `Presenze (JSON)` · `Voti (JSON)` · `Allegati (JSON)` · `Testo verbale (HTML)` · `Link PDF` · `Storico (JSON)`

**Frasario** — `Chiave` · `Nome` · `Testo` (con i segnaposto `[esito]`, `[allegato]`, `[data rendiconto]`…)

Scelta: presenze, voti e ordine del giorno restano in colonne JSON dentro `Assemblee` (una riga per
assemblea), come già fatto per `Sinistri` e `Interventi`. È più semplice da salvare e non riempie il
foglio di righe; se servono statistiche si possono esporre in seguito.

## Passi

1. ✅ Prototipo nel repository (`verbali/Verbali.html`).
2. Codice dell'Apps Script nel repository (`apps-script/`) — serve il codice attuale.
3. Creare le schede sopra e decidere come popolare unità/millesimi/titolari (Danea o a mano).
4. Nuove azioni nell'Apps Script: `loadVerbali` (condomìni, unità, millesimi, titolari, assemblee, frasario),
   `saveAssemblea`, `saveFrasario`, `uploadAllegatoVerbale` (cartella Drive del condominio).
5. Nel prototipo: sostituire `ESEMPIO()` e il salvataggio locale con le chiamate a `GS_URL`.
6. Claude: chiamata passando dall'Apps Script (chiave nelle Proprietà dello script, mai nell'HTML);
   dettatura con la Web Speech API del browser; PDF già generato in locale con pdf-lib.
7. Prova completa su un condominio di test prima dell'uso reale.
