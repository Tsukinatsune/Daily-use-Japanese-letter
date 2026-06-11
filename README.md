# Daily-use-Japanese-letter
# StrokesBase27lib v3 — Usage Guide

> **Library CDN**
> ```
> https://cdn.jsdelivr.net/gh/Tsukinatsune/CDN-Javascript-project@main/StrokesBase27lib%20v3.js
> ```
>
> **Data repository**
> [github.com/Tsukinatsune/Daily-use-Japanese-letter](https://github.com/Tsukinatsune/Daily-use-Japanese-letter)

---

## Quick start (browser)

```html
<script src="https://cdn.jsdelivr.net/gh/Tsukinatsune/CDN-Javascript-project@main/StrokesBase27lib%20v3.js"></script>
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
const kanjiLib = require('./StrokesBase27lib v3.js');

const { kanji, strokes } = await kanjiLib.loadBothFromCdn();
console.log(kanji.length);
console.log(Object.keys(strokes).length);
```

For Node 16 or older, polyfill `fetch` first:

```js
const fetch = require('node-fetch');
globalThis.fetch = fetch;

const kanjiLib = require('./StrokesBase27lib v3.js');
const { kanji, strokes } = await kanjiLib.loadBothFromCdn();
```

`loadKanjiFromFile` and `loadStrokesFromFile` use the browser `FileReader` API and are not available in Node. Use `fs` instead:

```js
const fs = require('fs');

const raw = JSON.parse(fs.readFileSync('./Kanji_v3.json', 'utf8'));
const kanji = Array.isArray(raw) ? raw : [raw];
```

---

## API reference

### `kanjiLib.loadAllFromCdn()`

Fetches all data sources in parallel: kanji, strokes, accent, NZ codes, braille, and background.

```js
const { kanji, strokes, accent, nz, braille, background } = await kanjiLib.loadAllFromCdn();
```

---

### `kanjiLib.loadBothFromCdn()`

Fetches only `Kanji_v3.json` and `Stroke.json` in parallel. Recommended for most projects.

```js
const { kanji, strokes } = await kanjiLib.loadBothFromCdn();
```

---

### `kanjiLib.fetchKanjiData()`

Fetches only `Kanji_v3.json`.

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

### `kanjiLib.fetchAccentData()`

Fetches pitch accent and part-of-speech data.

```js
const accent = await kanjiLib.fetchAccentData();
```

---

### `kanjiLib.fetchKvgData()`

Fetches and merges all KVG stroke-order chunks (4 parts). Includes component, radical, and textbook data.

```js
const kvg = await kanjiLib.fetchKvgData();
```

---

### `kanjiLib.fetchNzData()`

Fetches the NZ code map used to look up paleography files.

```js
const nz = await kanjiLib.fetchNzData();
```

---

### `kanjiLib.fetchBrailleData()`

Fetches the Louis Braille character map.

```js
const braille = await kanjiLib.fetchBrailleData();
```

---

### `kanjiLib.fetchBackgroundData()`

Fetches background/paleography metadata.

```js
const background = await kanjiLib.fetchBackgroundData();
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

### `kanjiLib.loadAccentFromFile(file)`

Loads accent/part-of-speech data from a local file input.

```js
const accent = await kanjiLib.loadAccentFromFile(input.files[0]);
```

---

### `kanjiLib.loadKvgFromFiles(files)`

Loads and merges KVG data from multiple local files (accepts a `FileList` or array).

```js
const input = document.getElementById('kvgFileInput');
input.addEventListener('change', async () => {
  const kvg = await kanjiLib.loadKvgFromFiles(input.files);
});
```

---

## Paleography

### `kanjiLib.fetchPaleographyForKanji(nzMap, kanji)`

Fetches paleography data for a single kanji using its NZ code.

```js
const nz = await kanjiLib.fetchNzData();
const paleo = await kanjiLib.fetchPaleographyForKanji(nz, '日');
```

---

### `kanjiLib.fetchPaleographyBatch(nzMap, kanjiList)`

Fetches paleography data for multiple kanji in parallel.

```js
const nz = await kanjiLib.fetchNzData();
const results = await kanjiLib.fetchPaleographyBatch(nz, ['日', '月', '山']);
// results['日'], results['月'], results['山']
```

---

### `kanjiLib.fetchPaleographyByNzCode(nzCode)`

Fetches paleography data directly by NZ code string.

```js
const paleo = await kanjiLib.fetchPaleographyByNzCode('04e16');
```

---

### `kanjiLib.getNzCode(nzMap, kanji)`

Looks up the NZ code for a kanji character.

```js
const nz = await kanjiLib.fetchNzData();
const code = kanjiLib.getNzCode(nz, '日'); // e.g. '04e16'
```

---

### `kanjiLib.buildKanjiToNzMap(nzMap)`

Builds a `Map` from kanji → NZ code for efficient repeated lookups.

```js
const nz = await kanjiLib.fetchNzData();
const map = kanjiLib.buildKanjiToNzMap(nz);
const code = map.get('日');
```

---

## Paleography iframe embed

You can embed a paleography viewer for any kanji directly in a web page using an `<iframe>`. Two URLs are supported:

| Host | URL pattern |
|---|---|
| **nichizenbu** | `https://nichizenbu.pages.dev/kanji/{nz_code}` |
| **jouyou** | `https://jouyou.pages.dev/kanji/{nz_code}` |

Replace `{nz_code}` with the NZ code for the kanji (e.g. `04e16` for 日).

### Static embed

```html
<!-- Example: 日 (NZ code 04e16) -->
<iframe
  src="https://nichizenbu.pages.dev/kanji/04e16"
  width="600"
  height="400"
  style="border: none;"
  loading="lazy"
  title="Paleography viewer — 日">
</iframe>
```

Or using the alternate host:

```html
<iframe
  src="https://jouyou.pages.dev/kanji/04e16"
  width="600"
  height="400"
  style="border: none;"
  loading="lazy"
  title="Paleography viewer — 日">
</iframe>
```

---

### Dynamic embed with the library

Look up the NZ code at runtime and inject the iframe:

```html
<script src="https://cdn.jsdelivr.net/gh/Tsukinatsune/CDN-Javascript-project@main/StrokesBase27lib%20v3.js"></script>

<div id="paleo-viewer"></div>

<script>
  async function showPaleography(kanji) {
    const nz = await kanjiLib.fetchNzData();
    const code = kanjiLib.getNzCode(nz, kanji);
    if (!code) { console.warn('NZ code not found for', kanji); return; }

    const iframe = document.createElement('iframe');
    iframe.src = `https://nichizenbu.pages.dev/kanji/${code}`;
    iframe.width  = 600;
    iframe.height = 400;
    iframe.style.border = 'none';
    iframe.loading = 'lazy';
    iframe.title = `Paleography viewer — ${kanji}`;

    document.getElementById('paleo-viewer').replaceChildren(iframe);
  }

  showPaleography('日');
</script>
```

---

### Switching hosts

Both `nichizenbu.pages.dev` and `jouyou.pages.dev` serve the same viewer. You can make the host configurable:

```js
const PALEO_HOST = 'jouyou.pages.dev'; // or 'nichizenbu.pages.dev'

function paleographyUrl(nzCode) {
  return `https://${PALEO_HOST}/kanji/${nzCode}`;
}

async function showPaleography(kanji) {
  const nz  = await kanjiLib.fetchNzData();
  const code = kanjiLib.getNzCode(nz, kanji);
  if (!code) return;

  document.getElementById('paleo-viewer').innerHTML =
    `<iframe src="${paleographyUrl(code)}" width="600" height="400"
             style="border:none;" loading="lazy"
             title="Paleography viewer — ${kanji}"></iframe>`;
}
```

---

## KVG helpers

```js
const kvg = await kanjiLib.fetchKvgData();

kanjiLib.getKvgEntry(kvg, '日');           // full entry object or null
kanjiLib.getKvgStrokes(kvg, '日');         // array of stroke path objects
kanjiLib.getKvgRadicals(kvg, '日');        // array of radicals
kanjiLib.getKvgComponents(kvg, '日');      // array of components
kanjiLib.getKvgTxtBooks(kvg, '日');        // textbook list
kanjiLib.getKvgTextbookSearch(kvg, '日');  // textbook search entries
kanjiLib.getKvgRadNameFile(kvg, '日');     // radical name file or null
kanjiLib.getKvgKodansha(kvg, '日');        // Kodansha reference or null
kanjiLib.getKvgNelson(kvg, '日');          // Classic Nelson reference or null
```

---

### `kanjiLib.mergeKvgIntoEntry(entry, kvgData, kanji)`

Merges KVG data into a single kanji entry object, preferring existing values.

```js
const merged = kanjiLib.mergeKvgIntoEntry(kanjiEntry, kvg, '日');
```

---

### `kanjiLib.mergeKvgIntoDb(db, kvgData)`

Merges KVG data into an entire kanji database object.

```js
const enriched = kanjiLib.mergeKvgIntoDb(kanjiDb, kvg);
```

---

## Accent / morphology helpers

```js
const accent = await kanjiLib.fetchAccentData();

kanjiLib.getMorphology(accent, '日');              // morphology entries or null
kanjiLib.getMorphologyByPos(accent, '日', 'noun'); // filter by part of speech
kanjiLib.getWaniKaniLevel(accent, '日');            // WaniKani level or null
kanjiLib.getYear(accent, '日');                    // year data or null
```

---

## Braille & background helpers

```js
const braille    = await kanjiLib.fetchBrailleData();
const background = await kanjiLib.fetchBackgroundData();

kanjiLib.getBraille(braille, '日');        // braille representation or null
kanjiLib.getBackground(background, '日');  // background data or null
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

<script src="https://cdn.jsdelivr.net/gh/Tsukinatsune/CDN-Javascript-project@main/StrokesBase27lib%20v3.js"></script>
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
| `loadAllFromCdn()` | CDN | `{ kanji, strokes, accent, nz, braille, background }` |
| `loadBothFromCdn()` | CDN | `{ kanji, strokes }` |
| `fetchKanjiData()` | CDN | `kanji[]` |
| `fetchStrokeData()` | CDN | `strokes{}` |
| `fetchAccentData()` | CDN | `accent{}` |
| `fetchKvgData()` | CDN | `kvg{}` |
| `fetchNzData()` | CDN | `nz{}` |
| `fetchBrailleData()` | CDN | `braille{}` |
| `fetchBackgroundData()` | CDN | `background{}` |
| `fetchPaleographyByNzCode(nzCode)` | CDN | `paleo{}` |
| `fetchPaleographyForKanji(nzMap, kanji)` | CDN | `paleo{}` |
| `fetchPaleographyBatch(nzMap, kanjiList)` | CDN | `{ kanji: paleo{} }` |
| `getNzCode(nzMap, kanji)` | — | `string \| null` |
| `buildKanjiToNzMap(nzMap)` | — | `Map` |
| `loadKanjiFromFile(file)` | Local file | `kanji[]` |
| `loadStrokesFromFile(file)` | Local file | `strokes{}` |
| `loadAccentFromFile(file)` | Local file | `accent{}` |
| `loadKvgFromFiles(files)` | Local file | `kvg{}` |
| `loadNzFromFile(file)` | Local file | `nz{}` |
| `loadBrailleFromFile(file)` | Local file | `braille{}` |
| `loadBackgroundFromFile(file)` | Local file | `background{}` |
| `loadPaleographyFromFile(file, kanji?)` | Local file | `{ nzCode, kanji, data }` |
| `getKvgEntry(kvg, kanji)` | — | `entry \| null` |
| `getKvgStrokes(kvg, kanji)` | — | `stroke[]` |
| `getKvgRadicals(kvg, kanji)` | — | `string[]` |
| `getKvgComponents(kvg, kanji)` | — | `string[]` |
| `getKvgTxtBooks(kvg, kanji)` | — | `any[]` |
| `getKvgTextbookSearch(kvg, kanji)` | — | `any[]` |
| `getKvgRadNameFile(kvg, kanji)` | — | `string \| null` |
| `getKvgKodansha(kvg, kanji)` | — | `string \| null` |
| `getKvgNelson(kvg, kanji)` | — | `string \| null` |
| `mergeKvgIntoEntry(entry, kvg, kanji)` | — | `entry{}` |
| `mergeKvgIntoDb(db, kvg)` | — | `db{}` |
| `getMorphology(accent, kanji)` | — | `entry[] \| null` |
| `getMorphologyByPos(accent, kanji, pos)` | — | `entry[]` |
| `getWaniKaniLevel(accent, kanji)` | — | `number \| null` |
| `getYear(accent, kanji)` | — | `any \| null` |
| `getBraille(braille, kanji)` | — | `any \| null` |
| `getBackground(background, kanji)` | — | `any \| null` |

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

— [Tsukinatsune](https://github.com/Tsukinatsune)
