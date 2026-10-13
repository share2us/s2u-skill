# s2u — command reference

Binary: `s2u` (alias `share2us`). `s2u help` prints this; `s2u version` prints the build.

## Commands

| Command | Purpose |
| --- | --- |
| `s2u <file>` | Upload a file (or quoted text, or stdin) and print a link. See flags below. |
| `s2u get\|-g <url#k> [--key KEY] [--output PATH]` | Download and decrypt a shared link. |
| `s2u pull <url-or-id> [--output PATH]` / `pull --all [--output DIR]` | Pull device-inbox shares sent to you. |
| `s2u receive [--watch] [--out PATH]` | Wait for a direct/offline transfer (LAN/Tailscale/WireGuard). |
| `s2u <file> --dest=<ip>` | Send a file directly to a waiting receiver. |
| `s2u <file> --serve` | Serve a file/folder over HTTP to any browser/curl on the network. |
| `s2u ls [--json]` | List your shares. |
| `s2u stats <serial\|id>` | Views/downloads for a share. |
| `s2u revoke <serial\|id>\|--all [--yes]` / `revoke-all` | Kill a link (or all). |
| `s2u pause\|resume <serial\|id>` | Temporarily disable / re-enable a share. |
| `s2u rm\|delete <serial\|id>... [--yes]` | Delete shares. |
| `s2u <file>.mdx` | Share an `.mdx` as a fillable interactive form. |
| `s2u forms responses <formId>` | List who submitted a form and when. |
| `s2u forms response <formId> <responseId>` | Decrypt and print one response. |
| `s2u login [--host URL] [--no-browser] [--no-input]` | Authenticate. |
| `s2u whoami` / `s2u logout` | Show identity / sign out this device. |
| `s2u devices` / `s2u signout <device>` | List / sign out a device. |
| `s2u config show\|defaults\|set-default\|unset-default\|set-base-url\|set-host` | Configuration. |
| `s2u contacts` / `trust\|block\|require-approval <email>` / `untrust\|unblock <email>` | Contact policy. |
| `s2u inbound [disallowed\|approvals\|auto]` / `incoming [approve\|reject <id>]` | Inbound-share policy + review. |
| `s2u mcp serve` / `s2u mcp token [--staging] [--json]` | Run the MCP server / mint an MCP token. |
| `s2u install-agent-tools [--agent codex\|claude-code\|gemini-cli]` | Install agent integration files. |
| `s2u tui` | Interactive terminal UI. |
| `s2u update [--host URL] [--version VERSION]` | Self-update the binary. |

## Upload flags (`s2u <file> [flags]`)

| Flag | Meaning |
| --- | --- |
| `--expires DUR` | Expiry, e.g. `30m`, `24h`, `7d` (default 7d; agents should use 24h). |
| `--name NAME` | Override the share/file name. |
| `--to EMAIL` / `--email EMAIL` | Recipient-restricted link (repeat/comma-separate); a contact is delivered E2E to their device. |
| `--password` | Prompt for a passphrase gate. |
| `--one-time` | Single-use; dies after one download (overrides `--max-views`). |
| `--max-views N` | Stop after N page views (downloads don't count). |
| `--encrypt` | Force client-side encryption. |
| `--device\|-d ALIAS` | Send to one of your own devices, E2E (needs interactive login). |
| `--contact EMAIL` | Force E2E to another user's device(s) (fails if not a contact). |
| `--restrict` / `--unrestrict` | Forbid / allow recipients resharing a private share (default: restrict). |
| `--allow-domain` / `--deny-domain DOMAIN` | Restrict which email domains can open it. |
| `--live\|-l` / `--watch\|-w` | Stream text updates to the link (`--watch` also flushes durably). |
| `--qr` / `--qrl` | QR of the content (offline) / QR of the link (after upload). |
| `--allow-secrets\|--force` / `--no-scan` | Proceed past / skip the local secret scan. Use only on explicit user consent. |
| `--new\|--fresh` | Force a fresh share instead of reusing an existing one. |
| `--json` | Machine-readable output. |

## Environment

| Var | Meaning |
| --- | --- |
| `SHARE2US_API_TOKEN` | Personal access token for non-interactive auth (CI/agents). Cannot do device/contact E2E sends. |
| `SHARE2US_BASE_URL` | Apex domain; `api.`/`s.` are derived (default `share2.us`). |
| `SHARE2US_DEFAULT_EXPIRY` | Default upload expiry (default `7d`). |
| `SHARE2US_API_BASE` / `SHARE2US_SHARE_BASE_URL` | Advanced overrides for the API / display-link base. |

## Notes

- The decryption key is in the URL fragment (`#k=…`); the full link is the secret and the server stores only ciphertext.
- Footgun options (`--password`, `--one-time`, recipients, visibility, `--allow-secrets`, device/contact) are intentionally **not** settable as standing defaults.
- `s2u --serve` refuses to publish the home directory or credential stores (`~/.ssh`, `~/.aws`, `~/.config`, `/etc`, …); serve a specific project folder.
