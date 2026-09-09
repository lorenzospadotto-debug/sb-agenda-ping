# skinboost-agenda-ping

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
| `TARGET_TOKEN` | Personal Access Token con accesso in lettura alle Actions e in scrittura ai Contents del repository di destinazione |

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
