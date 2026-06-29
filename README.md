# QOLLOCK Translations

**Public mirror of translation files from the private [QOLLOCK](https://github.com/civo7/QOLLOCK) mod.**

QOLLOCK is a Deadlock HUD customization mod. This repository contains **only** the UI translation catalogs — no mod code. It exists so community translators can contribute through the [qollock-translate](https://github.com/Predi-i/qollock-translate) web workbench without needing access to the private repository.

## What's here

```
locales/
├── en/translation.json           # English source (identity map)
├── ru/translation.json           # Russian
├── uk/translation.json           # Ukrainian
├── pl/translation.json           # Polish
├── bg/translation.json           # Bulgarian
├── be/translation.json           # Belarusian
├── ja/translation.json           # Japanese
├── zh/translation.json           # Chinese (Simplified)
├── fr/translation.json           # French
├── pt/translation.json           # Portuguese
├── pt-BR/translation.json        # Brazilian Portuguese
└── es/translation.json           # Spanish
```

830 translatable strings, ~100% coverage across all languages.

## How translations work

```
Private QOLLOCK repo              This public repo              Workbench (qollock-translate)
  ql_settings.js (maps)  ──sync──►  locales/*.json  ◄──PR──  translators edit in browser
        ▲                                │
        └─────── manual import ◄──────────┘ (maintainer merges PR)
```

1. **Translators** use the [qollock-translate workbench](https://github.com/Predi-i/qollock-translate) (web UI) to edit translations. The workbench reads the English source from `locales/en/translation.json` in **this** repo.
2. When a translator clicks **PR**, the workbench opens a pull request here with their changes.
3. The **maintainer** reviews and merges the PR.
4. The maintainer runs `scripts/import_locales_json.js` in the private QOLLOCK repo to fold the merged translations into the canonical JavaScript maps (`ql_settings.js`), then repacks the VPK.
5. A **GitHub Action** in the private repo automatically pushes `locales/` back here when changes are made, keeping this mirror up to date.

## The source of truth

The **JavaScript maps in `ql_settings.js`** (in the private repo) are the canonical source. These JSON files are a public interchange format for the workbench — the mod itself compiles the JS maps into the VPK (Source 2 Panorama cannot read JSON at runtime).

## Contributing

Use the [qollock-translate workbench](https://github.com/Predi-i/qollock-translate) to submit translations. Direct PRs to this repo are welcome too, but the workbench handles validation (placeholder mismatches, glossary terms) and makes it easier for non-developers.

## License

Translations are contributed by the community and fall under the same license as the QOLLOCK mod itself.
