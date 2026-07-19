# s2u — Claude Skill

A [Claude Agent Skill](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview)
for the [Share2Us](https://share2.us) CLI (`s2u`): share files, text, or command
output as opaque, expiring, access-controlled links from the terminal — with
client-side encryption and offline LAN/P2P transfer.

The skill teaches Claude when and how to use `s2u`, with a confirm-first,
never-upload-secrets safety flow.

## Install

Copy the skill into your Claude skills directory:

```bash
# Claude Code (project or personal):
git clone https://github.com/share2us/s2u-skill ~/.claude/skills/s2u
```

Or install it (and the matching agent tools) with the CLI:

```bash
s2u install-agent-tools --agent claude-code
```

Then install the CLI itself:

```bash
npm install -g @share2us/cli        # or:  curl -fsSL https://share2.us/install.sh | sh
s2u login
```

## Contents

- **`SKILL.md`** — the skill: when to use `s2u`, the safety rules, and the core
  workflows (share, download, QR, live, device/contact E2E, LAN/P2P, manage).
- **`reference.md`** — the full command, flag, and environment reference.

## Links

- Website: https://share2.us
- CLI: https://github.com/share2us/cli · `npm i -g @share2us/cli`

## License

MIT © Share2Us — see [LICENSE](LICENSE).
