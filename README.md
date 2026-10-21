# Polyglot Keyboard — layout repository

Push the **contents of this folder** to the root of a public GitHub repo (e.g.
`polyglot-keyboard-layouts`). The Android app downloads from it at runtime.

```
manifest.json          # catalog of every available language
layouts/
  en.json              # one file per language, named <id>.json
  es.json
  fr.json
  ...                  # 221 languages generated
```

## Point the app at your repo

In the app: `app/src/main/res/values/strings.xml` →

```xml
<string name="layout_repo_base_url">https://raw.githubusercontent.com/zaidfa/polyglot-keyboard-layouts/main/</string>
```

The app expects:

- `<base>/manifest.json`
- `<base>/layouts/<id>.json`

## Add or change a language

1. Add/edit `layouts/<id>.json` (schema below).
2. Add a matching row to `manifest.json`:
   ```json
   { "id": "sw", "name": "Swahili", "native": "Kiswahili", "script": "Latin" }
   ```
3. Commit & push. In the app: **Keyboard Languages → Update language list from
   GitHub**, then tap the new language to download it. No app rebuild needed.

## Layout schema

`rows` holds **only the letter rows** — the app adds the number row, shift,
space, enter, symbols, globe, emoji (and handwriting for CJK) automatically.

```json
{
  "id": "fr",
  "name": "French",
  "native": "Français",
  "script": "Latin",
  "rtl": false,
  "numberRow": true,
  "pinyin": false,
  "handwriting": false,
  "rows": [
    [ {"c":"a","s":"A","p":"àâæ"}, {"c":"z","s":"Z"} ],
    [ {"c":"q","s":"Q"} ],
    [ {"c":"w","s":"W"} ]
  ]
}
```

Per key: `c` = base output (required), `s` = shifted/uppercase (defaults to
`c.toUpperCase()`), `p` = long-press popups (optional), `w` = width weight
(optional). For case-less scripts (Arabic, Devanagari, Thai…) set `s` = `c`, and
set `rtl: true` for right-to-left scripts.

Top-level composition flags (optional, mutually exclusive): `pinyin` (Chinese —
Latin keys produce Hanzi candidates), `hangul` (Korean — keys emit jamo that are
composed into syllables), `kana` (Japanese — romaji converted to kana), and
`handwriting` (show the CJK handwriting-pad key).

## Regenerating

The whole set is produced by `tools/generate-layouts.js` in the app project:

```
node tools/generate-layouts.js
```

It rewrites `manifest.json` + `layouts/*.json` and copies the core subset
(en, es, fr, de, pt, it, ru, ar, hi, zh, ko, ja) into the app's `assets/layouts/`.
Korean (`ko`, `hangul:true`) and Japanese (`ja`, `kana:true`) are composed by the
in-app `HangulComposer` / `KanaConverter` engines.
