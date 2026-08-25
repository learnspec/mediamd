# MediaMD — Format Specification v0.2

> Part of the **LearnSpec** suite  
> Status: Draft — August 25, 2026

---

## Core Principle

MediaMD is the **visual resource catalogue format** of the LearnSpec suite. It centralises the metadata, sources, and licence information for each media asset, allowing other formats (LearnMD, QuizMD, FlashMD) to reference images by a symbolic identifier without managing licensing themselves.

MediaMD is a **leaf format**: it references and imports no other LearnSpec format. It is always consumed via a `!ref` directive, never included via `!import`.

| Principle | Description |
|---|---|
| **Markdown-first** | A `.media.md` file is valid Markdown readable in any editor |
| **File-native** | All metadata lives in the file — no database required |
| **Graceful degradation** | Each entry displays its thumbnail in any standard Markdown reader |
| **License-aware** | Each entry explicitly documents its licence, author, and source |
| **AI-native** | Generatable and consumable by an LLM without specific tooling |

---

## Format Levels

| Level | Mechanism | Purpose |
|---|---|---|
| 0 | ` ```media ` fenced block with minimal fields | Minimal catalogue, readable everywhere |
| 1 | YAML frontmatter + full fields | File metadata, language, version |
| 2 | Optional enriched fields | Dimensions, MIME type, context URLs |

Each level is a strict superset of the previous one.

---

## MediaMD File Structure

### Frontmatter (Level 1)

```yaml
---
title: "Media — F-22 Raptor"          # optional — inferred from first # H1
lang: en                               # REQUIRED — BCP-47 code
spec_version: "0.2"                    # optional — targeted spec version
author: Jane Smith                     # optional — author of the file (not of the media assets)
tags: [aviation, military]             # optional
created: 2026-05-10                    # optional — ISO 8601
updated: 2026-05-10                    # optional — ISO 8601
---
```

### Media Entry (Level 0)

Each entry consists of **two contiguous elements** in this order:

1. **A Markdown image line** — renders the thumbnail visibly in any standard reader
2. **A `media` fenced block** — contains all structured metadata

```markdown
![Alt text](thumb_url "media:slug")

```media id:slug
...metadata...
```
```

The `title` attribute of the image line (`"media:slug"`) links the rendered image to its metadata block. A LearnSpec player uses this attribute to resolve the reference; a standard reader simply displays the thumbnail with the slug as a tooltip.

**Rule**: if `thumb_url` is set, the Markdown image line is **mandatory**. If only `image_url` is available, the image line is recommended using `image_url` as the source.

---

## Complete Example

````markdown
---
title: "Media — F-22 Raptor"
lang: en
spec_version: "0.2"
tags: [aviation, military, usaf]
---

# Media — F-22 Raptor

![An F-22 Raptor in flight over Japan](https://upload.wikimedia.org/wikipedia/commons/thumb/1/1e/F-22_Raptor_edit1_%28cropped%29.jpg/500px-F-22_Raptor_edit1_%28cropped%29.jpg "media:f22-kadena")

```media id:f22-kadena
source: wikimedia
image_url: https://upload.wikimedia.org/wikipedia/commons/1/1e/F-22_Raptor_edit1_%28cropped%29.jpg
thumb_url: https://upload.wikimedia.org/wikipedia/commons/thumb/1/1e/F-22_Raptor_edit1_%28cropped%29.jpg/500px-F-22_Raptor_edit1_%28cropped%29.jpg
title: F-22 Raptor over Kadena
alt: An F-22 Raptor in flight over Japan
description: An F-22 Raptor flies over Kadena Air Base, Japan, on January 23, 2009, during a routine training mission.
license: "Public domain"
spdx: CC0-1.0
author: Master Sgt. Andy Dunaway
author_url: https://commons.wikimedia.org/wiki/File:F-22_Raptor.JPG
origin_url: https://commons.wikimedia.org/wiki/File:F-22_Raptor_edit1_(cropped).jpg
source_filename: File:F-22 Raptor edit1 (cropped).jpg
width: 1988
height: 1491
mime: image/jpeg
```

![F-22 Raptor bomb bay display at Reno Air Races](https://upload.wikimedia.org/wikipedia/commons/thumb/3/34/F_22_raptor_bomb_bay_display_2014_Reno_Air_Races_photo_D_Ramey_Logan.jpg/500px-F_22_raptor_bomb_bay_display_2014_Reno_Air_Races_photo_D_Ramey_Logan.jpg "media:f22-reno-bomb-bay")

```media id:f22-reno-bomb-bay
source: wikimedia
image_url: https://upload.wikimedia.org/wikipedia/commons/3/34/F_22_raptor_bomb_bay_display_2014_Reno_Air_Races_photo_D_Ramey_Logan.jpg
thumb_url: https://upload.wikimedia.org/wikipedia/commons/thumb/3/34/F_22_raptor_bomb_bay_display_2014_Reno_Air_Races_photo_D_Ramey_Logan.jpg/500px-F_22_raptor_bomb_bay_display_2014_Reno_Air_Races_photo_D_Ramey_Logan.jpg
title: F-22 Raptor Bomb Bay Display — Reno Air Races 2014
alt: Open bomb bay of an F-22 Raptor at an air show
description: F-22 Raptor bomb bay display at the 2014 Reno Air Races.
license: "CC BY-SA 4.0"
spdx: CC-BY-SA-4.0
license_url: https://creativecommons.org/licenses/by-sa/4.0
author: Don Ramey Logan
author_url: https://commons.wikimedia.org/wiki/Category:Photographs_by_Don_Ramey_Logan
origin_url: https://commons.wikimedia.org/wiki/File:F_22_raptor_bomb_bay_display_2014_Reno_Air_Races_photo_D_Ramey_Logan.jpg
width: 4235
height: 2829
mime: image/jpeg
```
````

---

## Field Reference

### Identification Fields

| Field | Status | Type | Description |
|---|---|---|---|
| `id` | **Required** | string | Unique slug within the file. Lowercase, hyphens, no spaces. Enables `media:slug` references from other formats. |
| `source` | Required | string | Source platform: `wikimedia`, `custom`, `unsplash`, etc. |

### Visual Fields

| Field | Status | Type | Description |
|---|---|---|---|
| `image_url` | **Required** | URL | Full-resolution image URL. |
| `thumb_url` | Recommended | URL | Thumbnail URL (500px recommended). If present, the Markdown image line is mandatory. |

### Descriptive Fields

| Field | Status | Type | Description |
|---|---|---|---|
| `title` | **Required** | string | Display title of the media. Used in captions. |
| `alt` | Recommended | string | Accessibility alt text. Distinct from `title` — describes the visual content of the image. |
| `description` | Optional | string | Long description of the image content and context. |
| `legend` | Optional | string (multi-line) | Structured key → label legend for annotated diagrams (numbered/lettered callouts, arrow/colour conventions). One item per line, `<key>. <label>`, preserved verbatim from the source. Never a summary of the image — that is `description`. |

#### Structured Legend

`legend` carries the asset's own key → label list, written as a YAML literal block so line breaks survive round-trips. Players MAY display it under the figure; pipelines MUST NOT fold it into `description` or summarise it — the keys printed on the asset are the contract.

```media id:human-heart-diagram
source: wikimedia
image_url: https://upload.wikimedia.org/wikipedia/commons/e/e0/Diagram_of_the_human_heart_%28cropped%29.svg
title: Diagram of the human heart
alt: Labelled cross-section of the human heart with numbered chambers and vessels
description: Cross-section of the human heart showing the chambers, valves and major vessels.
legend: |
  1. Right atrium
  2. Left atrium
  3. Superior vena cava
  4. Aorta
  5. Pulmonary artery
license: "CC BY-SA 3.0"
spdx: CC-BY-SA-3.0
author: Wapcaplet
origin_url: https://commons.wikimedia.org/wiki/File:Diagram_of_the_human_heart_(cropped).svg
mime: image/svg+xml
```

### Licence Fields

| Field | Status | Type | Description |
|---|---|---|---|
| `license` | **Required** | string | Free-form — licence as provided by the source (`"Public domain"`, `"CC BY-SA 4.0"`, etc.). |
| `spdx` | Recommended | string | Normalised SPDX identifier (`CC0-1.0`, `CC-BY-4.0`, `CC-BY-SA-4.0`…). Enables automated licence checking. |
| `license_url` | Conditional | URL | URL to the licence text. **Required** if `spdx` is absent or set to `custom`. |

#### Common SPDX Identifiers for Media

| Licence | SPDX |
|---|---|
| Creative Commons Zero (public domain waiver) | `CC0-1.0` |
| Creative Commons Attribution 4.0 | `CC-BY-4.0` |
| Creative Commons Attribution-ShareAlike 4.0 | `CC-BY-SA-4.0` |
| Creative Commons Attribution-ShareAlike 2.0 | `CC-BY-SA-2.0` |
| Creative Commons Attribution-NoDerivs 4.0 | `CC-BY-ND-4.0` |
| Public domain (US Government work, expired copyright…) | `LicenseRef-PublicDomain` |
| Licence not covered by SPDX | `custom` + `license_url` required |

> **Note**: `"Public domain"` in Wikimedia metadata may refer either to `CC0-1.0` (explicit waiver) or `LicenseRef-PublicDomain` (public domain by expiry or law). The MediaMD file author selects the appropriate SPDX identifier for each case.

### Attribution Fields

| Field | Status | Type | Description |
|---|---|---|---|
| `author` | Recommended | string | Name of the author or creating organisation. |
| `author_url` | Optional | URL | Author's profile or portfolio page. |

### Traceability Fields

| Field | Status | Type | Description |
|---|---|---|---|
| `origin_url` | Recommended | URL | Media page on the source platform (e.g. the Wikimedia Commons file page). |
| `context_url` | Optional | URL | Page where this media was found or used (e.g. a Wikipedia article). |
| `source_filename` | Optional | string | Original filename on the source platform (e.g. `File:F-22 Raptor edit1 (cropped).jpg`). |

### Technical Fields

| Field | Status | Type | Description |
|---|---|---|---|
| `width` | Optional | integer | Full-resolution image width in pixels. |
| `height` | Optional | integer | Full-resolution image height in pixels. |
| `mime` | Optional | string | MIME type (`image/jpeg`, `image/png`, `image/svg+xml`…). |

### Animation Fields

Two optional fields form the [AnimMD](https://github.com/learnspec/animmd) asset embedding (AnimMD spec §Embedding in Host Formats): the asset owns the addressing, the content owns the reference. They only make sense for vector assets (`image/svg+xml`).

| Field | Status | Type | Description |
|---|---|---|---|
| `bindings` | Optional | mapping | `name → {shapes: [id, …], callout: n}` — the addressing contract between author-chosen names and the asset's stamped shape ids (and legend callout numbers, when the asset has a numbered `legend`). Verifiable at import: every listed shape id must exist in the asset. |
| `animation` | Optional | string (multi-line) | A default AnimMD step-reveal script shipped with the asset, written as a YAML literal block. Its `bind:` entries may name `bindings` keys directly. A broken script degrades to the static asset (AnimMD §Graceful Degradation); it never invalidates the entry. |

```yaml
bindings:
  atrium-right: {shapes: [p12, p13], callout: 1}
  atrium-left: {shapes: [p14], callout: 2}
animation: |
  ---
  bind:
    atria: {label: "atrium"}
  ---

  ## The atria
  show: atria

  Blood enters the heart through the two atria.
```

---

## Referencing a MediaMD from Another Format

### Declaration

A MediaMD file is declared via the `!ref` directive at the top of the consuming document:

```markdown
!ref ./media-f22.media.md
!ref https://github.com/neuroneo/commons/blob/main/media/aviation.media.md
```

Multiple `!ref` directives may coexist in the same document. The slugs from all referenced files share the same namespace — `id` values must therefore be unique across all MediaMD files referenced in a given document.

### Default Reference Convention — `stock.media.md`

A collection MAY ship a single canonical media catalogue at its root named **`stock.media.md`**. When such a file exists, every other file in the same collection **implicitly references it** — no explicit `!ref ./stock.media.md` (nor `!ref ../../stock.media.md` from a sub-directory) is required for `media:slug` lookups to resolve.

```
collection-root/
├── stock.media.md          ← implicitly referenced by every file below
├── index.track.md
└── lessons/
    └── module-1/
        ├── intro.learn.md  ← `media:slug` resolves against /stock.media.md
        └── quiz.quiz.md    ← same
```

Rationale: a collection-level stock is a *registry*, not an additional media bundle — declaring it from each sub-file by relative path (`../../stock.media.md`) is brittle, repetitive, and obscures intent. Reserve `!ref` for *additional* MediaMD catalogues (shared commons, external libraries, alternate domains).

A LearnSpec player MUST resolve `media:slug` by searching, in order:

1. Files declared via explicit `!ref` directives in the current document.
2. The collection's `stock.media.md` if present at the collection root.

Lookups stop at the first match. If `id` values overlap between an explicit `!ref` and the implicit `stock.media.md`, the explicit `!ref` wins (allowing local override).

This convention is **opt-in by file naming**: any name other than `stock.media.md` at the collection root behaves like a regular MediaMD file and must still be declared with `!ref` to participate in slug resolution.

### Usage in Content

Once a MediaMD is declared via `!ref`, its assets are referenceable in the document body:

```markdown
![An F-22 Raptor in flight](media:f22-kadena "https://upload.wikimedia.org/.../500px-F-22.jpg")
```

- **`media:f22-kadena`**: slug resolved via the referenced MediaMD → full-resolution image + licence metadata
- **`"https://..."`**: fallback URL (thumbnail) — displayed in standard readers that don't know LearnSpec

A LearnSpec player may automatically display the caption, attribution, and licence below the image using the MediaMD metadata.

---

## Graceful Degradation

| Element | Standard reader | LearnSpec player |
|---|---|---|
| Image line `![alt](thumb_url "media:slug")` | Thumbnail displayed, slug as tooltip | Ignored (replaced by full-resolution image) |
| ` ```media ` block | Readable YAML code block | Parsed and resolved |
| `media:slug` reference in another format | Image displayed via fallback URL | Resolved via MediaMD — full resolution + licence |

A complete MediaMD file is therefore a **visually navigable image catalogue** in GitHub, Obsidian, or VS Code: thumbnails are visible, metadata is readable in the code blocks.

---

## Validation

### Lenient Mode (default)

| Condition | Level |
|---|---|
| `lang` absent from frontmatter | Warning |
| `id` missing on a `media` block | Error |
| Duplicate `id` within the file | Error |
| `image_url` missing | Error |
| `title` missing | Warning |
| `alt` missing | Warning |
| `license` missing | Error |
| `spdx` absent and `license_url` absent | Warning |
| `spdx: custom` without `license_url` | Error |
| `thumb_url` present without a Markdown image line | Warning |
| Image line without a following `media` block | Warning |
| Image line `id` matching no `media` block | Warning |
| `id` conflict across multiple MediaMD files referenced in the same document | Warning |
| `media:slug` reference with no matching `id` in any explicit `!ref` *and* no `stock.media.md` at the collection root | Warning |
| `media:slug` reference resolved via the implicit `stock.media.md` (no explicit `!ref`) | Info / no diagnostic |
| `legend` present but not a string | Warning |
| `bindings` present but not a mapping of name → mapping | Warning |
| `animation` present but not a string, or failing AnimMD grammar validation | Warning |

### Strict Mode (`--strict`)

All warnings are promoted to errors.

---

## Migration from the Proto Format

The proto format uses multi-document YAML (`---...---...`). Migration to MediaMD v0.1 follows this mapping:

| Proto field | MediaMD v0.1 field | Notes |
|---|---|---|
| *(absent)* | `id` | **To add** — unique slug per entry |
| `type: image` | *(removed)* | Implicit in the `media` fenced block |
| `source` | `source` | Unchanged |
| `image_url` | `image_url` | Unchanged |
| `thumb_url` | `thumb_url` | Unchanged + Markdown image line to add |
| `title` | `title` | Unchanged |
| *(absent)* | `alt` | **To add** |
| `description` | `description` | Unchanged |
| `license_id` | `license` + `spdx` | `license_id` → `license` (free-form); normalise to `spdx` |
| `license_short` | *(removed)* | Redundant with `spdx` |
| `license_url` | `license_url` | Unchanged |
| `author` | `author` | Unchanged |
| `author_url` | `author_url` | Unchanged |
| `origin_url` | `origin_url` | Unchanged |
| `source_url` | `context_url` | Renamed |
| `file_title` | `source_filename` | Renamed, optional |
| `width` | `width` | Unchanged |
| `height` | `height` | Unchanged |
| `mime` | `mime` | Unchanged |

---

*Released under the MIT License. Copyright © 2024-present LearnSpec Contributors*
