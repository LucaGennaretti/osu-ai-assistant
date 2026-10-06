# osu-ai-kb — Windows portable

Collector personale per osu!lazer: raccoglie gli score locali, analizza i replay osu!standard e sincronizza i dati con una knowledge base su Google Sheets, se configurata.

Questo repository distribuisce la versione portabile per **Windows x64**. L'applicazione legge lo storage del gioco in sola lettura e conserva i propri dati in un database SQLite separato.

**Stato: versione sperimentale.** Raccolta locale, analisi replay, arricchimento tramite osu! API, sincronizzazione Google e rollover giornaliero sono stati verificati con dati reali. Restano scenari di guasto e compatibilità da completare; i dettagli sono nel rapporto di accettazione incluso nel pacchetto.

## Download

Apri la sezione **Releases** di questo repository e scarica l'allegato **`osu-ai-kb-win-x64.zip`** della versione desiderata.

Estrai l'intero ZIP in una cartella scrivibile. Non eseguire il programma direttamente dall'archivio.

Il pacchetto include l'eseguibile, gli script di avvio e configurazione, il modello di configurazione, il codice Apps Script e le guide. Non richiede l'installazione di .NET, Node.js, Python o Git per l'utilizzo.

## Requisiti

- Windows x64 con PowerShell.
- Un'installazione di osu!lazer con storage locale accessibile.
- L'ID numerico del tuo account osu!.
- Per la sincronizzazione Google: un account Google, un foglio personale e un progetto Apps Script associato.
- Per l'arricchimento online: le credenziali di una tua applicazione osu! API v2.

Google Sheets e osu! API sono facoltativi. Senza Google configurato, gli eventi restano nella coda SQLite locale.

## Primo avvio

1. Nella cartella estratta, copia `config/appsettings.example.json` in `config/appsettings.json`.
2. Apri il nuovo file e imposta `UserId` con l'ID numerico del tuo account osu!. Il valore iniziale `0` deve essere sostituito.
3. Lascia `LazerPath` a `null` per il rilevamento tramite `%APPDATA%\osu\storage.ini`. Se necessario, imposta il percorso della directory che contiene `client.realm`.
4. Lascia `GoogleWebAppUrl` a `null` per iniziare con la sola raccolta locale.
5. Apri PowerShell nella cartella che contiene `osu-ai-kb.exe` ed esegui:

   ```powershell
   .\osu-ai-kb.exe test-lazer --config .\config\appsettings.json
   .\osu-ai-kb.exe run --dry-run --config .\config\appsettings.json
   ```

   Questi comandi verificano la lettura del gioco. Il dry run non acquisisce dati nel database del collector e non invia eventi a Google.

6. Fai doppio clic su **`start-osu-ai-kb.cmd`** per avviare la raccolta. La finestra resta aperta; premi **Ctrl+C** per arrestare il programma.

Non avviare più copie del collector sulla stessa installazione.

## Configurazione

| Campo | Uso |
| --- | --- |
| `UserId` | ID numerico dell'account di cui raccogliere gli score locali. |
| `LazerPath` | Directory dello storage osu!lazer; `null` abilita il rilevamento automatico. |
| `DatabasePath` | Database privato del collector; predefinito: `data/osu-ai-kb.db`. I percorsi relativi dipendono dalla cartella di avvio. |
| `TimeZoneId` | Fuso per la giornata e il rollover; predefinito: `Europe/Rome`. |
| `ReconciliationSeconds` | Intervallo di riconciliazione, in secondi; predefinito: `5`. |
| `GoogleWebAppUrl` | URL della tua Web App Apps Script; `null` disabilita l'invio a Google. |

Gli script di avvio inclusi impostano la cartella estratta come directory di lavoro.

## Google Sheets

Segui `docs/google-setup.md` nella cartella estratta. La guida spiega come creare il foglio, copiare `apps-script/Code.gs`, inizializzare le schede e distribuire la Web App.

Usa **`configure-google-secret.cmd`** per generare o riutilizzare il secret Google. Lo script lo salva nelle variabili dell'utente Windows e lo copia negli appunti. Incollalo nella proprietà Apps Script **`OSU_KB_INGEST_SECRET`**, poi svuota gli appunti copiando un altro testo.

Imposta l'URL della Web App nel campo `GoogleWebAppUrl` della configurazione locale. Per verificare il collegamento, esegui dalla cartella estratta:

```powershell
powershell.exe -NoProfile -ExecutionPolicy RemoteSigned -File .\run-osu-ai-kb.ps1 test-google
```

Il test invia un heartbeat autenticato al foglio. Gli eventi del collector conservano un request ID stabile durante i tentativi di invio.

Il rollover giornaliero aggrega i dati precedenti e rimuove le relative righe dalle schede del giorno. Usa un foglio dedicato al collector; gli score locali restano nel database SQLite.

## osu! API

Segui `docs/osu-api-setup.md` nella cartella estratta per registrare la tua applicazione API. Esegui **`configure-osu-api.cmd`** e inserisci Client ID e Client Secret della stessa applicazione.

Gli helper salvano le credenziali nelle variabili dell'utente Windows. Per verificare la configurazione:

```powershell
powershell.exe -NoProfile -ExecutionPolicy RemoteSigned -File .\run-osu-ai-kb.ps1 test-osu-api
```

Il collector usa le API per arricchire i dati locali. La rilevazione degli score avviene nello storage di osu!lazer.

## Stato e backup

Esegui i comandi dalla cartella estratta:

```powershell
powershell.exe -NoProfile -ExecutionPolicy RemoteSigned -File .\run-osu-ai-kb.ps1 status
.\osu-ai-kb.exe backup --config .\config\appsettings.json
```

Con la configurazione predefinita, il database si trova in `data/`, i backup in `data/backups/` e i log in `logs/`. Per percorsi personalizzati, il backup viene creato nella sottocartella `backups` accanto al database.

Prima di aggiornare, crea un backup e arresta il collector con Ctrl+C. Conserva `config/appsettings.json`, `data/` e gli eventuali percorsi personalizzati; sostituisci i file del programma con quelli della nuova release. Se la release aggiorna Apps Script, segui anche la procedura di aggiornamento in `docs/google-setup.md`.

## Credenziali e dati personali

| Variabile | Scopo |
| --- | --- |
| `OSU_KB_INGEST_SECRET` | Autenticazione degli invii alla tua Web App Google. |
| `OSU_CLIENT_ID` | Identificativo della tua applicazione osu! API. |
| `OSU_CLIENT_SECRET` | Secret della tua applicazione osu! API. |

Gli script di avvio caricano queste variabili dall'utente Windows anche quando la finestra corrente non le ha ancora ereditate. I secret non vanno inseriti nel JSON, nel codice Apps Script, nel repository o nello ZIP distribuito.

La configurazione personale contiene il tuo UserId, i percorsi locali e l'eventuale endpoint Google. Database, backup e log possono contenere dati personali: conserva questi file nella tua installazione e controllane il contenuto prima di condividerli.

## Limiti e documentazione

- L'analisi replay attuale è limitata a osu!standard e al formato legacy di input. Le metriche riguardano input e cursore; non vengono inventati giudizi o hit error.
- Gli aggiornamenti di osu!lazer possono richiedere una nuova verifica di compatibilità dello storage.
- Alcuni scenari di guasto, score falliti, mappe custom o modificate e l'installazione su Windows pulito restano da verificare completamente.

Le guide complete sono nella cartella `docs/` del pacchetto: `windows-install.md`, `google-setup.md`, `osu-api-setup.md`, `troubleshooting.md`, `osu-lazer-compatibility.md` e `acceptance-report.md`.

Le licenze, le attribuzioni e le notice delle dipendenze distribuite sono incluse in `licenses/`, con l'inventario in `THIRD_PARTY_NOTICES.md` e `THIRD_PARTY_INVENTORY.json`.
