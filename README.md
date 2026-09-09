# sb-agenda-ping

Sveglia pianificata per il workflow `agenda` di un repository privato.

## A cosa serve

Lo scheduler `cron` di GitHub Actions **salta o rimanda spesso i run sui repository
privati** con poca attività. Sui repository pubblici gli schedule sono molto più
affidabili, quindi questo repo esiste solo per svegliare l'altro: una volta al
giorno controlla se il workflow di destinazione è già partito e, in caso contrario,
gli manda un `repository_dispatch`.

Non contiene dati, logica applicativa o riferimenti in chiaro alla destinazione:
sia il repository di destinazione sia il token stanno nei Secrets.

## Configurazione

Servono due Secrets (Settings → Secrets and variables → Actions):

| Secret | Valore |
| --- | --- |
| `TARGET_REPO` | `proprietario/repository` di destinazione |
| `TARGET_TOKEN` | Personal Access Token per il repository di destinazione (vedi sotto) |

Per il token, se usi il tipo *fine-grained*:

- **Repository access**: `Only select repositories`, con il repository di destinazione
  selezionato. Il valore predefinito della schermata e' `Public repositories`, che **non
  basta** se la destinazione e' privata: le chiamate rispondono `404 Not Found` invece di
  `403`, perche' GitHub non rivela l'esistenza dei repository privati.
- **Permissions**: `Contents` = Read and write (serve per l'endpoint `dispatches`),
  `Actions` = Read-only (serve per il controllo anti-doppione), `Metadata` = Read-only.
- Se imposti una scadenza, segnatela: quando il token scade la sveglia smette di
  funzionare e il repository di destinazione resta senza run.

Il workflow di destinazione deve accettare il trigger:

```yaml
on:
  repository_dispatch:
    types: [run-agenda]
```

## Livelli di riserva

La sveglia è l'anello centrale di tre, ognuno dei quali si tira indietro se
trova un run già partito per la giornata:

1. il `cron` interno del repository di destinazione (04:23 UTC) — inaffidabile;
2. questa sveglia (04:31 UTC) — indipendente dal computer di casa;
3. un `LaunchAgent` sul Mac (06:40 locali) — funziona solo a Mac acceso.
