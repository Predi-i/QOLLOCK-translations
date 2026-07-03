# QOLLOCK translation workflow

Practical cheat sheet for how a translated string travels from the web workbench
into the game — and what you actually have to run by hand. Companion to
[`README.md`](./README.md) (which covers the file format / bridge scripts).

## The three repos

| Repo | Visibility | Role |
| --- | --- | --- |
| `civo7/QOLLOCK` | private | The mod. **Source of truth** = the `SETTINGS_XX_TEXT` maps in `panorama/scripts/ql_settings.js`. These compile into the VPK; Panorama can't read JSON at runtime. |
| `Predi-i/QOLLOCK-translations` | public | This repo. The mirror the workbench reads and writes. `locales/<lang>/translation.json`. |
| `grimoire-translate` (Cloudflare) | — | The web workbench. Translators sign in with GitHub, edit strings, and it opens PRs **here**. |

## The loop

```
ql_settings.js maps ──[export]──▶ QOLLOCK-translations/locales/<lang> ──▶ workbench (D1 drafts)
        ▲                                                                          │
        │                                                                    translators edit
        │                                                                          │
        └────────────[import]──────── merged PR in this repo ◀──[Open PR]──────────┘
```

- **export** = private → public. Push newly-added English/source strings out so
  translators can see them. Runs **automatically daily** (CI), or by hand via
  `scripts/export_locales.bat`.
- Translators work in the workbench → it opens a PR here → **you review & merge it**.
- **import** = public → private. Pull this repo and merge finished translations
  back into the maps. **Always manual.**

## What YOU run, and when

### To ship finished translations into the mod (the common one)
From the `QOLLOCK` repo, in order:

```sh
# 1. pull this repo so you have the merged PRs, then merge them into the maps
node scripts/import_locales_json.js ../QOLLOCK-translations/locales
# 2. always re-validate
node --check panorama/scripts/ql_settings.js
# 3. repack the VPK (your normal build step)
```

Or just double-click `scripts/import_locales.bat` (it pulls the mirror first).

Import is **safe**: non-empty values override, blanks/missing keys leave existing
work untouched, key order preserved, brand-new keys appended sorted. Turkish
(`tr`) and any language not yet wired into `ql_settings.js` are skipped with a
warning — that's expected, not an error.

### To push new source strings out to translators
Usually you don't — **CI does it daily**. To force it now: run the
**Sync translations to public repo** workflow in `civo7/QOLLOCK` → Actions, or
double-click `scripts/export_locales.bat` locally.

## Order matters (one rule)

When doing a full round-trip by hand, **import before you export**. Import pulls
community work into the maps; export then pushes the maps out. Do it the other
way and a stale map could revert a fresh translation.

The overwrite-safe export makes this hard to get wrong now — it *merges* into the
mirror (keeps existing values, only adds missing keys) rather than overwriting —
but the habit is still: **import first, export second.**

## Adding a new language to the mod

A language shows up in this repo (via the workbench) **before** it exists in the
game. To wire it into `ql_settings.js` you touch four points + two scripts —
see the "add Korean and Italian" commit in `QOLLOCK` for the exact pattern:

1. `const SETTINGS_LANGUAGE_XX = <next index>;`
2. an entry in `SETTINGS_LANGUAGE_OPTIONS` (the in-game picker)
3. an empty `const SETTINGS_XX_TEXT = {};` map (import fills it)
4. a branch in `LocalizeSettingsText` (Latin scripts → `NormalizeLatinSettingsText`;
   CJK/other → return as-is, like `ja`/`ko`/`zh`)
5. add `{ code, mapVar }` to `LANGUAGES` in `scripts/export_locales_json.js`
6. add `code: mapVar` to `CODE_TO_MAPVAR` in `scripts/import_locales_json.js`

Then run the import to populate the new map, and `node --check`.

> ⚠️ Non-Latin scripts (Korean, Chinese, …) need a Panorama font that covers
> them. If a new language renders as boxes in-game, that's a font/CSS issue, not
> a translation one.

## Secrets (who needs what)

| Secret | Lives in | Used by | Grants |
| --- | --- | --- | --- |
| `PUBLIC_REPO_PAT` | `civo7/QOLLOCK` | `sync-translations.yml` (daily export) | Contents: **read+write** on `Predi-i/QOLLOCK-translations` |
| `QOLLOCK_GITHUB_TOKEN` | `grimoire-translate` | `update-context.yml` | Contents: **read** on `civo7/QOLLOCK` |
| `PR_GITHUB_TOKEN` | `grimoire-translate` deploy | the Worker (opens PRs here) | Contents+PR: **write** on `Predi-i/QOLLOCK-translations` |

### Creating `PUBLIC_REPO_PAT`

1. https://github.com/settings/personal-access-tokens/new (fine-grained).
2. Name: `qollock-sync-public`, no expiration (or your preference).
3. Resource owner: **Predi-i**. Repository access → **Only select repositories**
   → `Predi-i/QOLLOCK-translations`.
4. Permissions → Repository → **Contents: Read and write**.
5. Generate, copy the token.
6. In `civo7/QOLLOCK` → Settings → Secrets and variables → Actions → **New
   repository secret** → name `PUBLIC_REPO_PAT`, paste the value.

Until this secret exists the sync workflow fails with
`Error: Input required and not supplied: token`.
