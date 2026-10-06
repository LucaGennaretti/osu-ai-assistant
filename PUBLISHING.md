# Pubblicazione della prerelease

La cartella `github-release` è la radice pronta per il repository di distribuzione. Contiene README, note della release, licenze e inventario. Non contiene il progetto .NET né i dati della tua installazione.

Gli allegati pronti sono nella sottocartella `release-assets/`:

- `osu-ai-kb-win-x64.zip`: pacchetto portabile con documentazione anonimizzata e notice complete per i componenti distribuiti.
- `SHA256SUMS.txt`: checksum SHA-256 dello ZIP.

Questa sottocartella è esclusa da Git: i due file vanno allegati alla GitHub Release. Il vecchio ZIP e la cartella dell'installazione portabile, esterni a `github-release`, non sono quelli preparati per questa pubblicazione.

## Repository e push

Crea un repository GitHub vuoto per la distribuzione. Per seguire i comandi sotto, non inizializzarlo sul sito con un README, una licenza o un `.gitignore`.

Apri PowerShell nella cartella `github-release`. Sostituisci `TUO_ACCOUNT/TUO_REPOSITORY` con i dati del repository appena creato:

```powershell
$repositoryUrl = 'https://github.com/TUO_ACCOUNT/TUO_REPOSITORY.git'
git init -b main
git add .gitignore README.md RELEASE_NOTES.md PUBLISHING.md RELEASE_VERIFICATION.md THIRD_PARTY_NOTICES.md THIRD_PARTY_INVENTORY.json licenses/
git status --short
git diff --cached --stat
```

Controlla che l'elenco contenga soltanto questi documenti e le licenze, senza `release-assets/`, configurazioni personali, database o log. Poi esegui:

```powershell
git commit -m "Prepare Windows portable prerelease"
git remote add origin $repositoryUrl
git push -u origin main
```

Questi comandi sono istruzioni per la pubblicazione manuale: non sono stati eseguiti durante la preparazione del pacchetto.

## GitHub Release

Nel repository su GitHub:

1. Apri **Releases → Draft a new release**.
2. Crea il tag proposto **`v1.0.0-preview.1`**, usando come target il commit appena caricato su `main`. Il tag è una proposta per questa prima distribuzione e non è stato creato localmente.
3. Imposta il titolo **osu-ai-kb v1.0.0-preview.1 — Windows x64**.
4. Copia il contenuto di `RELEASE_NOTES.md` nella descrizione.
5. Allega `release-assets/osu-ai-kb-win-x64.zip` e `release-assets/SHA256SUMS.txt`.
6. Seleziona **This is a pre-release**. Le prove di accettazione ancora mancanti sono descritte nelle note e nel pacchetto.
7. Controlla la bozza e pubblicala quando sei pronto.

I file **Source code (zip)** e **Source code (tar.gz)** generati automaticamente da GitHub contengono i documenti del repository. Il programma da scaricare è l'allegato `osu-ai-kb-win-x64.zip`.

## Verifica del download

Dopo il download, dalla cartella che contiene entrambi gli allegati:

```powershell
$expectedHash = ((Get-Content .\SHA256SUMS.txt -Raw).Trim() -split '\s+')[0]
$actualHash = (Get-FileHash .\osu-ai-kb-win-x64.zip -Algorithm SHA256).Hash
if ($actualHash -ine $expectedHash) { throw 'Checksum ZIP non corrispondente' }
Write-Host 'Checksum ZIP verificato.'
```

La preparazione del pacchetto conserva l'eseguibile e il codice Apps Script della build precedente: non aggiunge una nuova verifica funzionale o una firma digitale. Le verifiche svolte sono riportate in `RELEASE_VERIFICATION.md`.

Fonti: [gestione delle GitHub Release](https://docs.github.com/en/repositories/releasing-projects-on-github/managing-releases-in-a-repository), [descrizione delle Release e degli archivi automatici](https://docs.github.com/en/repositories/releasing-projects-on-github/about-releases).
