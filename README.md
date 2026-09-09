# sb-agenda-ping

Sveglia pianificata: una volta al giorno lancia un workflow su un altro repository.

Esiste perché lo scheduler `cron` di GitHub Actions ritarda o salta i run sui
repository privati poco attivi, mentre sui pubblici è puntuale.

Non contiene dati né logica applicativa. La destinazione e il token stanno nei
Secrets (`TARGET_REPO`, `TARGET_TOKEN`); al token servono `Actions` = Read and
write e `Metadata` = Read-only, sul solo repository di destinazione.
