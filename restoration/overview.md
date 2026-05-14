# Restoration — Where to start?

## Identify your scenario

### Scenario 1 — Rollback after a bad update or system mistake
→ [Timeshift restoration](system-restore.md#timeshift)

### Scenario 2 — Loss of application data (Immich, OpenCloud, etc.)
→ [Borg restoration](data-restore.md)

### Scenario 3 — Dead system drive
1. Reinstall Debian
2. Restore system config files from the configs repo → [Config restoration](configs-restore.md)
3. Restore application data from Borg → [Borg restoration](data-restore.md)
4. Recreate `/home/<user>/.config/infisical-bootstrap` (chmod 600) from Vaultwarden — machine identity credentials for local admin tooling
5. Bring Docker containers back up via CI/CD (Forgejo Actions; containers inject secrets at runtime through the shared bootstrap helper) → [Docker restoration](docker-restore.md)

### Scenario 4 — Server completely unrecoverable (theft, fire, etc.)
1. New hardware + reinstall Debian
2. **Regain access to Vaultwarden** → [Recovery chain](vaultwarden-chain.md)
3. Retrieve rclone encryption key from Vaultwarden
4. Restore Borg repo from Hetzner → [Data restoration](data-restore.md#from-hetzner-offsite-backup)
5. Restore system configs from the configs repo → [Config restoration](configs-restore.md)
6. Recreate `/home/<user>/.config/infisical-bootstrap` (chmod 600) from Vaultwarden — machine identity credentials for local admin tooling
7. Bring Docker containers back up via CI/CD (Forgejo Actions; containers inject secrets at runtime through the shared bootstrap helper) → [Docker restoration](docker-restore.md)

### Scenario 5 — Lost access to Vaultwarden only
→ [Vaultwarden recovery chain](vaultwarden-chain.md)

## Recovery priority

1. ProtonMail (entry point for everything)
2. Vaultwarden (holds all other secrets)
3. Secrets manager (application secrets)
4. Docker services
