---
name: design-deck-render
description: Render user-supplied structured content into a single-file, 1600x900 HTML presentation using a dark "image-archive" visual language (serif titles, hairline rules, restrained motion, native SVG figures). Use when the user wants to turn an outline, notes, or a page-by-page markdown into a shareable, offline-capable deck instead of a pptx.
version: 0.1.0
author: Xavier23010596
license: MIT
trigger:
  keywords:
    - design deck
    - html deck
    - single-file deck
    - image-archive deck
    - render deck
    - 设计解读
    - 动态演示
    - 单文件演示
    - 放映稿
    - 路演稿
  intents:
    - turn an outline / notes / markdown into a page-by-page presentation
    - produce a deck that opens from file:// and survives being forwarded as one file
    - restyle content into a dark, editorial, low-motion visual language
    - add deep links and rehearsal parameters to a slide deck
  not_for:
    - pptx / pdf export requests
    - real-time collaborative editing
    - chart-heavy data reports (use a charting tool instead)
    - generating or sourcing images (this skill lays out images, it does not create them)
---

# Design Deck Render — operating instructions

You render **content the user supplies** into a self-contained HTML deck.
You are a layout and motion engine, not a content generator and not a scraper.

## 0. Scope

In scope: parsing the user's content, choosing page archetypes, writing one HTML file, building it
into a single self-contained artefact, and verifying it renders.

Out of scope: inventing facts, generating images, fetching third-party pages, exporting pptx/pdf,
building an editor.

## 1. Non-negotiable constraints

1. **Never hardcode endpoints or identifiers.** No URLs, share codes, share IDs, tenant IDs, API paths,
   tokens, keys, or account names may appear in generated files. Every such value must be passed in at
   runtime by the user. If the user does not supply one and the task needs one, ask.
2. **Never copy third-party source.** Do not paste, fetch, mirror, or "recreate by inspection" another
   site's HTML, CSS, JS, images, or fonts. The visual language in §4 is specified from first principles;
   implement it from this document only.
3. **Offline by default.** The output must contain zero external subresource requests: no CDN links, no
   web fonts, no analytics, no remote images. All CSS and JS inline; all images inlined as data URIs or
   referenced from a local folder the user controls.
4. **Single artefact.** The deliverable is one `index.html` that opens from `file://` and still works
   with the network disabled.
5. **Content fidelity.** Do not add claims, numbers, or captions the user did not provide. If a field is
   missing, leave the slot visibly empty (see §4.9) instead of filling it with plausible text.
6. **Ask before assuming scope.** If page count, audience, or language is unspecified, ask once, then
   proceed with a stated default.

## 2. Runtime parameters

Collect these before writing any markup. Nothing here has a baked-in value.

| Parameter | Required | Type | Notes |
|---|---|---|---|
| `content_path` | yes | path | Source of the page-by-page content (§3) |
| `title` | yes | string | Deck title; also used for `<title>` and the running head |
| `out_path` | yes | path | Where to write the built single file |
| `lang` | no | `zh-CN` \| `en` | Default `zh-CN`; affects font stack and line-height |
| `pages` | no | int | Expected page count; if omitted, derive from content and report it |
| `image_dir` | no | path | Local folder with images; if omitted, use placeholder plates |
| `accent` | no | hex | Single accent colour; default `#C0483C` |
| `motion` | no | `full` \| `calm` | `calm` shortens durations 40% and disables the slow push |

If the user references a live site as a *style reference*, record it as prose ("dark editorial, serif
titles, hairlines") and **do not** fetch, embed, or cite its resources.

## 3. Input contract

Accept either format; normalise to the JSON shape in §3.3 first.

### 3.1 Markdown convention

```markdown
# Deck title
## Page 1 — Cover
- claim: one sentence, falsifiable
- kicker: DESIGN READING · 001
- image: cover.webp
- text: optional short body, <= 40 characters
## Page 2 — Claim
...
```

### 3.2 JSON

```json
{
  "title": "string",
  "lang": "zh-CN",
  "pages": [
    {
      "id": 1,
      "kind": "cover",
      "kicker": "string?",
      "title": "string",
      "claim": "string?",
      "body": "string? (<= 40 chars)",
      "figure": { "src": "img/x.webp", "caption": "string?", "note": "string?" },
      "data": { "value": "0.89", "rangeStart": "0.90", "rangeEnd": "0.95", "label": "string" },
      "items": ["string", "string"],
      "caveat": "string?"
    }
  ]
}
```

### 3.3 Normalised page object

`id` (int, contiguous from 1), `kind`, `kicker`, `title`, `claim`, `body`, `figure`, `data`, `items`,
`caveat`. Unknown keys are preserved in a `data-extra` attribute rather than dropped silently.

## 4. Render contract

### 4.1 Stage

Fixed 1600×900 canvas, scaled uniformly into the viewport; never reflow.

```css
#stage{position:absolute;left:50%;top:50%;width:1600px;height:900px;
  transform-origin:center center;overflow:hidden}
```

```js
const s = Math.min(vw / 1600, vh / 900);
stage.style.transform = `translate(-50%,-50%) scale(${s})`;
```

Use `visualViewport` when available and take `Math.min(visualViewport.height, innerHeight)`;
`body` must be `position:fixed; inset:0; height:100dvh; overflow:hidden`.
Listen to `resize`, `orientationchange`, and `visualViewport.resize`.

### 4.2 Tokens

```css
:root{
  --ink:#0F1214; --ink2:#161A1D;
  --paper:#E9E5DC; --dim:rgba(233,229,220,.52); --faint:rgba(233,229,220,.30);
  --rule:rgba(233,229,220,.20);
  --accent:#C0483C;            /* overridable via `accent` parameter */
  --serif:"Noto Serif SC","Source Han Serif SC","Songti SC",serif;
  --sans:"Noto Sans SC","Source Han Sans SC","PingFang SC","Microsoft YaHei",sans-serif;
}
```

The accent colour is reserved for **numbering and key figures only**. Never use it for decoration,
borders, or headings en masse.

### 4.3 Type scale

`66 / 46 / 30 / 19 / 15 / 12 / 11` px. Body text never below 15 px; 11–12 px is limited to running
heads, captions, and footnotes. Titles are serif; body is sans. Line-height: titles 1.28–1.5,
body 1.95–2.0. Letter-spacing on kickers and captions: `0.14em`–`0.22em`.

### 4.4 Grid

Page padding `84px 104px`. Content column max-width 1180 px. Two-column splits use `gap: 70px`
and a non-equal split (e.g. 820/512) — equal halves read as a spreadsheet.

### 4.5 Plate (image panel)

```css
.plate{position:relative;overflow:hidden;background:var(--ink2)}
.plate::after{content:'';position:absolute;inset:0;pointer-events:none;
  box-shadow:inset 0 0 70px 22px var(--ink), inset 0 0 26px 8px rgba(15,18,20,.55)}
.plate img{width:100%;height:100%;object-fit:cover;display:block;
  transform:scale(1.07);transition:transform 11s linear}
.slide.in .plate img{transform:scale(1)}
```

The inset shadow dissolves the image's own light background into the dark page; without it every
photo shows a bright rectangle seam.

### 4.6 Motion primitives — one per page

| Class | Effect |
|---|---|
| `.rise` | 16 px upward fade; stagger with `.d1`–`.d6` |
| `.draw` | SVG path growth; requires `pathLength="1000"`; `stroke-dashoffset:1000 → 0` |
| `.wipe` | `clip-path: inset(0 100% 0 0)` → `inset(0)` |
| `.pop` | opacity only, purely delayed |

Exactly **one** of these is the page's main motion; the others may only be delayed echoes.
Durations: rise 0.9 s, draw 1.7 s, wipe 1.3 s, image push 11 s. Easing is out-cubic, never bounce.

### 4.7 Page archetypes

Pick from these ten; do not invent layouts ad hoc.

1. **Cover** — full-bleed plate, left-bottom aligned title block, hairline divider, subtitle row.
2. **Claim** — one oversized quotation, author line, scope footnote pinned to the bottom rule.
3. **Split figure + text** — figure left, kicker/title/body right, optional native scale figure.
4. **Full-bleed figure + path** — full-width plate with an SVG overlay path and one end label.
5. **Numbered anchors** — figure with 2–4 `.pin` badges placed on the actual parts, legend at right.
6. **Staircase set** — 3 plates of decreasing size offset along a diagonal; a missing asset becomes a
   dashed "archive gap" card naming what to shoot.
7. **Range bar** — two endpoints always visible with both numbers; never show one number plus a note.
8. **Timeline** — horizontal rule, 3 nodes, the out-of-scope node hollowed out.
9. **Cost page** — three short statements, staggered indent, no images (images soften the honesty).
10. **Closing five** — five numbered lines on a vertical rule, plus one action sentence.

### 4.8 Chrome

Running head (`.rail`) top-left with deck label, top-right with `NN / TT`; 2 px progress bar at the
bottom in accent colour; keyboard hint that fades after 6.5 s. Keyboard: `←/→/Space/PageUp/PageDown`,
`F` fullscreen, `A` autoplay (9 s/page), `Home/End`. Click right half → next, left half → previous.
Touch: 50 px horizontal swipe. Deep link: `?p=N`.

### 4.9 Missing assets

If `figure.src` is absent or the file does not exist, render a **placeholder plate**, not a broken image:

```html
<div class="plate" data-ph="FIG 03 · 待补：正侧全高，机位平齐腰线"></div>
```

Style `[data-ph]` with a faint diagonal hatch, a dashed 1 px outline, and the label centred at 58%
opacity. A placeholder must be visible — a dark block on a dark page reads as "nothing here".

### 4.10 Static state (required)

```css
@media (prefers-reduced-motion: reduce){
  *{transition:none!important;animation:none!important}
  .draw{stroke-dashoffset:0}
  .pop,.rise,.pin{opacity:1;transform:none}
  .wipe{clip-path:none}
  .plate img{transform:none}
}
```

This block is both the accessibility path and the only reliable way to screenshot a page. Do not
implement a `.static` class toggled from JS instead.

## 5. Procedure

1. **Read content.** Parse per §3, normalise, and echo back: page count, kinds chosen, missing fields.
   Stop and ask if more than one required field is missing across pages.
2. **Assign archetypes.** Map each page to one of §4.7. Report the mapping as a table; flag any page
   whose content does not fit an archetype instead of forcing it.
3. **Check assets.** For every `figure.src`, verify the file exists under `image_dir`. List missing
   ones; they become §4.9 placeholders.
4. **Write `index.src.html`.** Hand-authored markup, no templating engine. One `<section class="slide"
   data-i="N">` per page, `N` contiguous from 0.
5. **Build.** Inline CSS/JS and images into `out_path`. Verify the artefact references no absolute
   URL of any scheme (including protocol-relative `//host` paths) and keeps no relative `img/` reference.
6. **Verify.** Static checks (see §6) plus headless screenshots of at least pages 1, mid, last at
   1600×900 and 844×390.
7. **Report.** Output: page count, archetype table, files written, artefact size, screenshot paths,
   and any placeholder left behind.

## 6. Quality gates

Fail the build and fix, do not ship with warnings.

- [ ] `data-i` contiguous from 0; one section per content page.
- [ ] No `{{placeholder}}` left unreplaced.
- [ ] No external subresource: no absolute URL of any scheme, no protocol-relative path.
- [ ] Artefact has zero `src="img/..."` left (all inlined) when built as single file.
- [ ] `@media (prefers-reduced-motion: reduce)` present.
- [ ] Every page has exactly one main motion class.
- [ ] No text node overflowing its container at 1600×900 and 844×390.
- [ ] Images ≥ 300 KB each are flagged with a WebP suggestion.
- [ ] Single artefact ≤ 6 MB; larger means images were not compressed.
- [ ] No URL, share code, ID, or token in any generated file.
- [ ] No third-party source, image, or font is embedded.

## 7. Output contract

```
<out_path>              single self-contained HTML
<working dir>/index.src.html   hand-editable source (keep alongside)
```

State explicitly in the final message: that the file is self-contained, that it needs no network,
and which fonts it expects locally.

## 8. Failure handling

| Situation | Response |
|---|---|
| Content has no per-page `claim` | Ask for one per page; do not infer claims |
| Page count exceeds 22 | Warn about attention span; propose merging, then follow the user's choice |
| Image referenced but missing | Render §4.9 placeholder and list the path in the report |
| Fonts unavailable | Continue; state the fallback in the report |
| User asks for pptx/pdf | Decline and explain the formats are out of scope |
| User asks to "copy this site exactly" | Explain that only the visual language is reproduced from this spec; do not fetch or mirror the site |
