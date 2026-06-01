# Daily-use-Japanese-letter
# StrokesBase27lib — Usage Guide

> **Library CDN**
> ```
> https://cdn.jsdelivr.net/gh/Tsukinatsune/CDN-Javascript-project@main/StrokesBase27lib.js
> ```
>
> **Data repository**
> [github.com/Tsukinatsune/Daily-use-Japanese-letter](https://github.com/Tsukinatsune/Daily-use-Japanese-letter)

---

## Quick start (browser)

```html
<script src="https://cdn.jsdelivr.net/gh/Tsukinatsune/CDN-Javascript-project@main/StrokesBase27lib.js"></script>
<script>
  kanjiLib.loadBothFromCdn().then(({ kanji, strokes }) => {
    console.log(kanji.length);
    console.log(Object.keys(strokes).length);
  });
</script>
```

---

## Quick start (Node.js 18+)

`fetch` is built in from Node 18. CDN functions work with no changes.

```js
const kanjiLib = require('./StrokesBase27lib.js');

const { kanji, strokes } = await kanjiLib.loadBothFromCdn();
console.log(kanji.length);
console.log(Object.keys(strokes).length);
```

For Node 16 or older, polyfill `fetch` first:

```js
const fetch = require('node-fetch');
globalThis.fetch = fetch;

const kanjiLib = require('./StrokesBase27lib.js');
const { kanji, strokes } = await kanjiLib.loadBothFromCdn();
```

`loadKanjiFromFile` and `loadStrokesFromFile` use the browser `FileReader` API and are not available in Node. Use `fs` instead:

```js
const fs = require('fs');

const raw = JSON.parse(fs.readFileSync('./Kanji.json', 'utf8'));
const kanji = Array.isArray(raw) ? raw : [raw];
```

---

## API reference

### `kanjiLib.loadBothFromCdn()`

Fetches both `Kanji.json` and `Stroke.json` in parallel. Recommended for most projects.

```js
const { kanji, strokes } = await kanjiLib.loadBothFromCdn();
```

---

### `kanjiLib.fetchKanjiData()`

Fetches only `Kanji.json`.

```js
const kanji = await kanjiLib.fetchKanjiData();
```

---

### `kanjiLib.fetchStrokeData()`

Fetches and decodes only `Stroke.json`.

```js
const strokes = await kanjiLib.fetchStrokeData();
```

---

### `kanjiLib.loadKanjiFromFile(file)`

Loads kanji data from a local file input.

```js
const input = document.getElementById('myFileInput');
input.addEventListener('change', async () => {
  const kanji = await kanjiLib.loadKanjiFromFile(input.files[0]);
});
```

---

### `kanjiLib.loadStrokesFromFile(file)`

Loads and decodes stroke data from a local file input.

```js
const strokes = await kanjiLib.loadStrokesFromFile(input.files[0]);
```

---

## Drawing strokes on a `<canvas>`

```js
const { strokes } = await kanjiLib.loadBothFromCdn();
const canvas = document.getElementById('myCanvas');
const ctx = canvas.getContext('2d');
const SCALE = canvas.width / 109;

(strokes['日'] ?? []).forEach(d => {
  ctx.save();
  ctx.scale(SCALE, SCALE);
  ctx.strokeStyle = '#1a1208';
  ctx.lineWidth = 4;
  ctx.lineCap = 'round';
  ctx.lineJoin = 'round';
  ctx.stroke(new Path2D(d));
  ctx.restore();
});
```

---

## Animated stroke-by-stroke

```js
async function animateKanji(char, canvas) {
  const { strokes } = await kanjiLib.loadBothFromCdn();
  const paths = strokes[char] ?? [];
  const ctx = canvas.getContext('2d');
  const SCALE = canvas.width / 109;

  for (const d of paths) {
    ctx.save();
    ctx.scale(SCALE, SCALE);
    ctx.strokeStyle = '#1a1208';
    ctx.lineWidth = 4;
    ctx.lineCap = 'round';
    ctx.lineJoin = 'round';
    ctx.stroke(new Path2D(d));
    ctx.restore();
    await new Promise(r => setTimeout(r, 400));
  }
}

animateKanji('日', document.getElementById('myCanvas'));
```

---

## Filtering kanji

```js
const { kanji, strokes } = await kanjiLib.loadBothFromCdn();

const n5       = kanji.filter(k => k.jlpt_new === 5);
const grade1   = kanji.filter(k => k.grade === 1);
const withData = kanji.filter(k => strokes[k.kanji]);
const sun      = kanji.filter(k => (k.meanings ?? []).some(m => m.includes('sun')));
```

---

## Local file usage (browser)

```html
<input type="file" id="kanjiFile" accept=".json">
<input type="file" id="strokeFile" accept=".json">

<script src="https://cdn.jsdelivr.net/gh/Tsukinatsune/CDN-Javascript-project@main/StrokesBase27lib.js"></script>
<script>
  document.getElementById('kanjiFile').addEventListener('change', async (e) => {
    const kanji = await kanjiLib.loadKanjiFromFile(e.target.files[0]);
  });

  document.getElementById('strokeFile').addEventListener('change', async (e) => {
    const strokes = await kanjiLib.loadStrokesFromFile(e.target.files[0]);
  });
</script>
```



---

## Error handling

```js
try {
  const { kanji, strokes } = await kanjiLib.loadBothFromCdn();
} catch (err) {
  console.error(err.message);
}
```

---

## All functions

| Function | Source | Returns |
|---|---|---|
| `loadBothFromCdn()` | CDN | `{ kanji, strokes }` |
| `fetchKanjiData()` | CDN | `kanji[]` |
| `fetchStrokeData()` | CDN | `strokes{}` |
| `loadKanjiFromFile(file)` | Local file | `kanji[]` |
| `loadStrokesFromFile(file)` | Local file | `strokes{}` |

---

## Acknowledgements

**Created by [Tsukinatsune](https://github.com/Tsukinatsune) | [Artoriasphere](https://github.com/ArtoriasphereOrg) (organization)**

---

**AI Assistants**
- **[Claude AI](https://claude.ai)** — Used for writing documentation, and designing diagrams and algorithms
- **[Gemini](https://gemini.google.com)** — Used for discovering and researching data sources
- **[Grok](https://grok.com)** — Used for finding word definitions and linguistic data

**Stroke Order Data**
- **[KanjiVG](https://kanjivg.tagaini.net)** — SVG-based kanji stroke order data

**Kanji & Character Data**
- **[Jisho](https://jisho.org)** — Japanese dictionary and kanji reference
- **[Unihan Database](https://unicode.org/charts/unihan.html)** — Unicode Han character data including readings and meanings
- **[davidluzgouveia/kanji-data](https://github.com/davidluzgouveia/kanji-data)** — Used `kanji-jouyou.json` as the Jōyō kanji character list only
- **[kanjidatabase.com](https://kanjidatabase.com)** — Kanji reference and classification data
- **[zi.tools](https://zi.tools)** — Archived images of kanji and Chinese (Hanzi) characters
- **[Home in Mists](https://homeinmists.ilotus.org/)** — Supplementary kanji and character data
- **[Kanji Alive](https://kanjialive.com)** — Kanji readings, meanings, and usage examples
- **[kanji.reader.bz](https://kanji.reader.bz)** — Kanji reading and lookup reference
- **[hc.jsecs.org](https://hc.jsecs.org)** — Historical and classical CJK character data
- **[moji.or.jp](https://moji.or.jp)** — Japanese character standard and encoding data
- **[dict.variants.moe.edu.tw](https://dict.variants.moe.edu.tw)** — Traditional Chinese variant character dictionary from Taiwan's Ministry of Education
- **[Wikipedia](https://wikipedia.org)** — General reference for kanji history and linguistic context
- **[GitHub](https://github.com)** — Hosting and access to open-source kanji datasets

**Pitch Accent**
- **[kanjium](https://github.com/mifunetoshiro/kanjium)** — Pitch accent and kanji vocabulary data
- **[Kanshudo](https://www.kanshudo.com/howto/pitch)** — Used as a reference to verify pitch accent data

**Audio / Text-to-Speech**
- **Microsoft Text-to-Speech** — Japanese audio readings using the following voices:
  - Microsoft Ayumi
  - Microsoft Haruka
  - Microsoft Ichiro
  - Microsoft Sayaka
  - Microsoft 圭太

— [Tsukinatsune](https://github.com/Tsukinatsune)
