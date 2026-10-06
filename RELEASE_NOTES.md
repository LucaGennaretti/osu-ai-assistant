# osu-ai-kb — Windows x64 preview

Distribuzione portabile sperimentale per osu!lazer. L'applicazione raccoglie gli score locali in sola lettura, li conserva in SQLite, analizza i replay osu!standard e sincronizza una knowledge base personale su Google Sheets. L'arricchimento osu! API è facoltativo.

## Download e installazione

Scarica l'allegato `osu-ai-kb-win-x64.zip`, estrailo in una cartella scrivibile e segui il README incluso. La versione self-contained non richiede un runtime .NET installato.

Crea `config/appsettings.json` dal modello incluso e imposta il tuo UserId. Configura Google e osu! API tramite le guide e gli helper del pacchetto, poi avvia `start-osu-ai-kb.cmd`.

## Correzioni incluse

- Timeout Google di 120 secondi e retry per i 404 transitori dopo il redirect del Content Service.
- Arricchimento mappe con operazioni sul foglio per batch e verifica dell'identità della mappa.
- Precedenza in coda a rollover e score, mantenendo l'ordine necessario agli snapshot.

## Preparazione per la distribuzione

- Documentazione aggiornata e anonimizzata: nessun ID dell'account di prova o percorso della sua installazione.
- Licenze, attribuzioni e notice delle dipendenze dirette, transitive e native incluse nel pacchetto.
- Inventario delle dipendenze e checksum SHA-256 inclusi.
- Eseguibile e codice Apps Script conservati identici alla build verificata del 5 ottobre 2026.

## Verifiche e limiti

Il rapporto della build riporta 26 test .NET e 10 test Apps Script superati, oltre al recupero osservato della coda Google. Queste sono verifiche storiche: la preparazione dell'archivio non riesegue i test funzionali o gli invii live.

La release resta una **prerelease**: mancano alcune prove di guasto, ACK perduto, score falliti, mappe custom/modificate e Windows pulito. Le metriche replay sono limitate a osu!standard e al formato legacy di input. Sono documentati tutti i limiti nel rapporto di accettazione incluso.

L'archivio non include configurazione personale, database, backup, log, collegamenti al foglio personale o screenshot del deployment. Ogni utilizzatore deve configurare il proprio account e le proprie credenziali.
