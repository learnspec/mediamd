# Changelog

All notable changes to the MediaMD specification are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).
Versioning follows [Semantic Versioning](https://semver.org/).

---

## [0.2] — 2026-08-25

### Added
- `legend` descriptive field: structured key → label legend for annotated diagrams (numbered/lettered callouts), one `<key>. <label>` item per line, preserved verbatim. Never a summary of the image — that is `description`.
- Animation Fields section: `bindings` (`name → {shapes: [id, …], callout: n}` addressing contract) and `animation` (default AnimMD step-reveal script), the MediaMD side of the AnimMD asset embedding (AnimMD spec §Embedding in Host Formats).
- Lenient-mode validation rules for the three new fields (type checks; embedded `animation` script checked against the AnimMD grammar).

### Compatibility
- Purely additive: a v0.1 reader ignores the new fields in lenient mode; writers may keep emitting `spec_version: "0.1"` for entries that use none of them.

## [0.1] — 2026-05-10

### Added
- Initial draft of the MediaMD specification as part of the LearnSpec suite.
