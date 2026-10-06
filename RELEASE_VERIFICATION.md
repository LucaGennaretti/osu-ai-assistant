# Verifica del pacchetto per la pubblicazione

Preparazione e verifica: **6 ottobre 2026**.

- Integrità ZIP/CRC e checksum SHA-256 verificati: **42 file** nell'archivio.
- Eseguibile, Apps Script, helper e configurazione di esempio identici byte per byte a quelli dello ZIP originale. Il programma non è stato ricompilato.
- ZIP originale ed eseguibile dell'installazione locale conservati; la preparazione ha scritto una copia separata.
- Nessuna configurazione personale, database, backup, log, collegamento al foglio o screenshot nell'archivio.
- Documentazione controllata per i valori personali configurati e per pattern di token/endpoint reali. Nessuna corrispondenza trovata. I valori dei secret configurati sono stati cercati anche nel binario, in UTF-8 e UTF-16 LE, senza stamparli.
- Modello di configurazione verificato: `UserId=0`, `LazerPath=null`, `GoogleWebAppUrl=null`.
- Inventario ricavato dal bundle effettivo: **38 pacchetti NuGet**, oltre al runtime .NET **10.0.12**. Gli hash di **39 asset NuGet** nel bundle corrispondono ai pacchetti locali.
- **22 testi di licenza/notice** inclusi e verificati tramite hash; ogni pacchetto ha un riferimento ai testi applicabili. Le attribuzioni incorporate SharpCompress e Realm sono incluse.
- Collegamenti Markdown locali del pacchetto e delimitatori dei blocchi di codice verificati.
- `.gitignore` esclude `release-assets/` dal repository di documentazione.
- Nessun repository Git inizializzato, nessuno staging, commit, tag, push o pubblicazione remota effettuato.

## Hash

ZIP pronto da allegare:

`06908a96d3f2e18f9f41fed9a1b8bd35a9a6303b9cd25ced685fc76cfc2a27fb`

Eseguibile invariato:

`69b00012989b3ed6e028a328ab420bbc2b1d526559376aad2831539378195c88`

## Ambito della verifica

Queste verifiche riguardano il contenuto e l'integrità del pacchetto. Non sono stati rieseguiti build, test funzionali, letture live del gioco o invii a Google/osu! API. Le prove storiche della build sono riportate nel rapporto di accettazione incluso nello ZIP. La release resta una prerelease con i limiti descritti nelle note.
