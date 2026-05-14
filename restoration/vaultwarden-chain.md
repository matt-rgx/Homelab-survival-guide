# Recovery Chain — Vaultwarden

Vaultwarden is the central vault. This page describes how to regain access even in the event of a total failure.

## Principle

ProtonMail is the only service accessible **without** Vaultwarden, thanks to an offline passphrase stored physically off-server. It is the entry point for the entire recovery chain.

The critical services (ProtonMail, email alias provider, Vaultwarden, cloud/VPS provider) use a phone-based TOTP app, not Vaultwarden — eliminating any circular dependency.

## Physical emergency kit

The minimum viable credential set that lives entirely outside any digital vault:

| What | Format | Purpose |
|---|---|---|
| ProtonMail passphrase | Paper (offline) | Entry point — access ProtonMail without phone |
| 2FA recovery code — email alias provider | Paper (offline) | Bypass TOTP if phone is unavailable |
| 2FA recovery code — Vaultwarden | Paper (offline) | Bypass TOTP if phone is unavailable |
| 2FA recovery code — cloud/VPS provider | Paper (offline) | Bypass TOTP if phone is unavailable |
| Vaultwarden master password | Memorized | Decrypt the weekly export |

The Vaultwarden master password is the only credential kept solely in memory. Everything else in the vault can be reconstructed from the weekly export once decrypted.

**Note:** Using the ProtonMail passphrase to log in automatically disables 2FA on the account (per Proton's own documentation). 2FA must be re-enabled after recovery. This means a single paper credential is sufficient to regain ProtonMail access even without a phone.

**Why the passphrase matters more than a phone number:** Proton offers multiple account recovery methods, but only the passphrase recovers both account access *and* existing data. SMS/phone recovery resets the password but leaves all existing emails unreadable — they are encrypted with a key derived from the old password. Without the passphrase, you could log back in but the weekly Vaultwarden export sitting in your inbox would be inaccessible.

## Step 1 — Access ProtonMail without Vaultwarden

Use the offline passphrase (stored physically). Per Proton's design, this bypasses and disables 2FA — no recovery code needed.

If the phone is available: use Proton Authenticator (TOTP) as normal.

## Step 2 — Retrieve the Vaultwarden export

An encrypted export is sent weekly to ProtonMail.

Search for the most recent email with the `encrypted_json` export attached.

## Step 3 — Restore Vaultwarden

1. Deploy a fresh Vaultwarden instance
2. Import the encrypted export (`encrypted_json` format)
3. Use the master password to decrypt

## Step 4 — Retrieve all secrets

Once Vaultwarden is restored:
- SSH keys → reconnect to server / Git / cloud providers
- rclone key → access Borg backup on Hetzner
- 2FA for DNS, cloud, Git providers → access to external services
- Infisical bootstrap credentials → log into Infisical UI, generate a new client secret for the local admin machine identity, recreate `/home/<user>/.config/infisical-bootstrap` (chmod 600) on the server — required for local admin tooling and `manual_xyops_update.sh`

## ⚠️ Critical points

- Never store ProtonMail 2FA **only** in Vaultwarden — it would create a circular dependency
- Regularly test ProtonMail access without Vaultwarden
- Verify that the weekly export email is arriving (check every Sunday)
- The weekly export is only as useful as the master password — keep it memorized
