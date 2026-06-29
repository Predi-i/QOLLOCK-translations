# QOLLOCK Translations

Help translate **[QOLLOCK](https://github.com/civo7/QOLLOCK)** — a quality-of-life HUD mod for Deadlock — into your language.

You don't need to code, install anything, or clone this repo. Everything happens in the browser.

## 👉 Translate here

### **[qollock-translate.predi.workers.dev](https://qollock-translate.predi.workers.dev)**

1. Open the site and **sign in with GitHub** (any GitHub account works — no card, no setup).
2. **Pick your language** — or add a new one if it's not listed yet.
3. **Translate the strings.** The English text is on one side, you type your translation on the other.
4. When you're happy, click **Submit / PR** — the site opens a pull request for you automatically. A maintainer reviews it and it ships in the next mod update.

That's it. No git, no JSON, no terminal.

## What the site helps you with

- **Progress filters** — jump straight to what's *to fill*, *flagged*, *needs review*, or *done*, so you always know what's left.
- **Glossary** — a per-language dictionary of recurring terms (e.g. `Profile → Профиль`). Agree on a term once and the site reuses it everywhere: it suggests the translation as a one-click insert, pre-fills boxes that exactly match a glossary term (just press Enter), and warns when something is translated inconsistently. Some terms are **locked** (file names and acronyms like `.vpk`, `gameinfo.gi`, `HUD`, `MMR`) — keep those unchanged.
- **Placeholder safety** — text like `{{count}}` must stay intact; the site won't let you submit a translation that drops or mangles a placeholder.
- **Autosave** — your drafts are kept as you go.

## Languages

Currently translated: 🇬🇧 English (source), 🇷🇺 Russian, 🇺🇦 Ukrainian, 🇵🇱 Polish, 🇧🇬 Bulgarian, 🇧🇾 Belarusian, 🇯🇵 Japanese, 🇨🇳 Chinese (Simplified), 🇫🇷 French, 🇵🇹 Portuguese, 🇧🇷 Brazilian Portuguese, 🇪🇸 Spanish.

---

<details>
<summary>For maintainers — how this repo stays in sync</summary>

This repository is a **public mirror** of the translation catalogs from the private QOLLOCK mod repo. It contains only the UI strings — no mod code — so translators can contribute without access to the private repository.

- The canonical source is the JavaScript maps in `ql_settings.js` (private repo). These JSON files are a public interchange format; Source 2 Panorama can't read JSON at runtime, so the maps are compiled into the VPK.
- A GitHub Action in the private repo pushes `locales/` here whenever strings change, keeping this mirror current.
- Merged translation PRs are folded back into `ql_settings.js` via `scripts/import_locales_json.js`, then the VPK is repacked.

</details>
