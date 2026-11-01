# AGENTS.md

Personal Foundry VTT repo for Steve Herron. The module packages the Cosmere RPG adventure *Law and Obligation* as compendia for Foundry VTT 13 (`verified` 13.351). It requires the `cosmere-rpg` system (`verified` 2.1.0). It is a module, not a game system.

## Standing rules

- Personal project. Never add Dawson County Schools (DCS) work content, data, names, or branding.
- Open pull requests only. Never merge, force-push, or deploy. Steve approves every merge.
- Never commit secrets, tokens, credentials, or Foundry license keys, and never paste them into logs, chat, or PR text.
- Steve works on Windows with PowerShell 7. Give commands as PowerShell, not bash or Command Prompt.
- Explain risky commands before running them, and wait for Steve. That includes deletes, history rewrites, force operations, and anything that writes into Foundry user data or a live world.

## Repository

`main` is the default branch and currently contains only `README.md`.

The module scaffold is on `compendium-module-scaffold` and is not merged:

- `module.json` — manifest. `id` is `law-and-obligation-foundry`, `version` is `1.0.0`, and `esmodules` is `scripts/init.js`. Pack `name`, `label`, `path`, and `type` are declared here.
- `scripts/init.js` — browser ES module. On Foundry's `init` hook it logs `Law and Obligation | Module initialized`. No bundler and no package dependencies.
- `packs/actors` — Actor pack `law-obligation-actors`
- `packs/items` — Item pack `law-obligation-items`
- `packs/journals` — JournalEntry pack `law-obligation-journals`
- `packs/scenes` — Scene pack `law-obligation-scenes`
- `packs/macros` — Macro pack `law-obligation-macros`
- `packs/tables` — RollTable pack `law-obligation-tables`
- `packs/playlists` — Playlist pack `law-obligation-playlists`

Each pack directory contains only a `.gitkeep`. Foundry writes pack documents as LevelDB once content exists. There is no `package.json`, lockfile, CI config, or test runner. Keep each pack `path` aligned with its directory.

## Checks

There is no build step and no automated test suite. From the repo root, once the scaffold files are on the branch you are editing:

```powershell
Get-Content -Raw .\module.json | ConvertFrom-Json | Out-Null
if (Get-Command node -ErrorAction SilentlyContinue) { node --check .\scripts\init.js }
```

`ConvertFrom-Json` throws when `module.json` is invalid. `node --check` only parses `scripts/init.js`. Node is optional; Foundry supplies `Hooks` at runtime.

Linking the repo into Foundry writes outside this tree. Explain the command and confirm the data path in Foundry Setup first (usually `$env:LOCALAPPDATA\FoundryVTT\Data`), then:

```powershell
New-Item -ItemType Junction -Path "$env:LOCALAPPDATA\FoundryVTT\Data\modules\law-and-obligation-foundry" -Target (Get-Location).Path
```

Enable **Law and Obligation** in a world that uses the Cosmere RPG system and reload. Leave world data in Foundry's user-data folder.

## Conventions

- Commit subjects are short and imperative, in the style of `Add module.json for Law and Obligation RPG`.
- Keep the manifest `id` and each pack `name` stable. Change `label` when only the display name changes.
- Put each document in the pack of its type. Keep module behavior in `scripts/`. Create compendium documents in Foundry, then commit that pack's LevelDB data when the task is to ship it.
- Leave Foundry runtime files (`LOCK`, `LOG`, `LOG.old`) and any world data untracked.
- Change only the files the task names. Branch from `main`, open a pull request into `main`, and leave `compendium-module-scaffold` alone unless the task names it. Steve merges.
