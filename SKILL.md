---
name: s2u
description: >-
  Share files, text, or command output as opaque, expiring, access-controlled
  links from the terminal with the Share2Us CLI (s2u). Use when the user asks to
  share, upload, publish, send, or transfer a file or text; generate a download
  link or QR code; download/receive a shared link; or move a file directly
  between machines over LAN or peer-to-peer.
---

# Share2Us CLI (s2u)

`s2u` turns a file, text, or piped output into one opaque link that expires and
can be access-controlled. Content is encrypted client-side (the decryption key
lives only in the URL fragment `#k=…` and is never sent to the server). It can
also transfer files directly between machines with no account, over LAN or P2P.

The binary is `s2u` (alias `share2us`). Home: https://share2.us.

## Safety rules — apply before every upload

1. **Confirm before uploading.** State the exact path, filename, size, expiry,
   and that the resulting link is reachable by anyone who has it (unless
   restricted). Upload only after the user agrees. No silent uploads.
2. **Never upload secrets.** Refuse to upload `.env`/env dumps, private or SSH
   keys, cloud service-account JSON, kubeconfig, password databases, raw
   production DB exports, or any file with detected credentials. Offer a
   redacted alternative instead. `s2u` runs a local secret scan and blocks by
   default — do **not** pass `--allow-secrets`/`--no-scan` to bypass it unless
   the user explicitly accepts the risk.
3. **Default to a short expiry** for agent-created shares: `--expires=24h`
   unless the user asks for another.
4. **Don't reduce protection silently.** Links are private-to-recipient only
   when you set `--to`, `--password`, `--one-time`, or `--max-views`.

## Setup

Install (once):

```bash
npm install -g @share2us/cli        # or:  curl -fsSL https://share2.us/install.sh | sh
```

Authenticate:

- Interactive: `s2u login` (opens a browser; `--no-browser` prints a code).
- Non-interactive (CI/agents): set `SHARE2US_API_TOKEN` to a personal access
  token. Note: a PAT cannot do device/contact end-to-end sends.

Check state with `s2u whoami`. Set standing upload defaults with
`s2u config set-default <key> <value>` (keys: `expires`, `encrypt`, `max-views`,
`no-scan`, `allow-domains`, `deny-domains`).

## Core workflows

**Share a file → link**

```bash
s2u report.pdf --expires=24h
# -> https://s.share2.us/7Kf9aQ2m#k=<key>
```

**Share text or command output**

```bash
s2u "release notes: v1.2 ships Tuesday" --expires=1h   # quoted text
kubectl logs pod-x | s2u --name=pod-x.log              # piped stdin
```

**Access controls** (combine as needed)

```bash
s2u secret.pdf --to=alex@acme.dev        # recipient-restricted link (email)
s2u data.csv --password                  # prompts for a passphrase
s2u invite.txt --one-time                # dies after one download
s2u deck.pdf --max-views=5               # stops after 5 views
```

**Download / receive a shared link**

```bash
s2u get "https://s.share2.us/7Kf9aQ2m#k=<key>" --output=report.pdf
```

**QR code** (a phone camera reads it)

```bash
s2u notes.txt --qr     # QR of the CONTENT, fully offline, nothing uploaded (<~1 KB text)
s2u report.pdf --qrl   # upload as usual, then also show a QR of the LINK
```

**Live text stream** (log/tail that updates the link until Ctrl-C)

```bash
s2u app.log --live     # push updates every 3s;  --watch also flushes durably
```

**Direct device / contact end-to-end** (needs an interactive login, not a PAT)

```bash
s2u secret.env --device laptop          # to your own logged-in device, E2E
s2u file.zip --contact alex@acme.dev    # to another user's device, E2E
```

**Offline direct transfer — no account, no cloud** (both machines present)

```bash
# receiver:
s2u receive                             # prints its IP + a passphrase, then waits
# sender:
s2u photos.zip --dest=192.168.1.5       # LAN / Tailscale / WireGuard
s2u file.zip --serve                    # serve over HTTP to any browser/curl
```

**Manage shares**

```bash
s2u ls                       # list your shares
s2u stats <serial|id>        # views/downloads
s2u revoke <serial|id>       # kill one link   (revoke --all for everything)
s2u rm <serial|id> --yes     # delete
```

## Notes

- **End-to-end:** the key is in the URL fragment (`#k=`), so the full link is
  the secret — treat it like a password and never post it where you shouldn't.
  The server stores only ciphertext.
- **`--serve` exposure:** `s2u --serve` publishes a directory over the LAN; point
  it at a specific folder, never your home directory or a credential store
  (`s2u` refuses those).
- **MCP alternative:** `s2u mcp serve` exposes preview/upload/share-text tools
  over MCP; when connected, prefer `share2us_preview_file` →
  `share2us_upload_file` (with `confirm: true`) for the same confirm-first flow.

For the complete command and flag reference, see `reference.md`.
