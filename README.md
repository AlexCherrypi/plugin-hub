# plugin-hub

Keeps the Claude Code plugin marketplaces current. The plugin repos and the
marketplaces need no secret and no workflow of their own.

Every 10 minutes [`sync-marketplaces`](.github/workflows/sync-marketplaces.yml)
reads each marketplace's `.claude-plugin/marketplace.json`, compares every
GitHub-sourced plugin's pinned commit (`source.sha`) with the HEAD of its repo,
and where one moved on commits the new pin to that marketplace
(`Plugin-Shas aktualisieren: …`). That push makes claude.ai re-sync the
marketplace – its webhook fires only on a push to the marketplace repo itself.
A new plugin is covered as soon as a marketplace lists it; a plugin in the
marketplace repo itself (a `./plugins/…` path) needs no pin.

| Marketplace | Secret here |
|---|---|
| `wemotion-gmbh/claude-plugins` | `MARKETPLACE_DISPATCH_TOKEN` |
| `AlexCherrypi/alexcherrypi-plugins` | `PERSONAL_MARKETPLACE_DISPATCH_TOKEN` |

Each secret is a fine-grained token on that marketplace repo with
**Contents: read and write**. A missing one skips that marketplace with a
warning on the run; an expired one fails the run.

- Run it now: Actions → Sync marketplaces → Run workflow (or
  `gh workflow run sync-marketplaces.yml -R AlexCherrypi/plugin-hub`).
- Another marketplace: add a line to the matrix and its secret.
- Plugin repos must be public: their HEAD is read without a token.
- Once a day it commits `.github/keepalive` if nothing was committed for 30
  days, so GitHub does not turn the schedule off.
