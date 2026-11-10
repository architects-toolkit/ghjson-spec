# Changelog

All notable changes to the GhJSON specification are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

### Changed

- Connection endpoint `id` now accepts an `instanceGuid` string as an alternative to the integer `id` for canvas-bound references (§4.3/§4.4): the integer `id` remains the preferred, compact shape and producers should emit it whenever the target is declared in the same document. Integers encoded as strings are coerced during parsing.

### Added

- Initial GhJSON format specification.
- JSON Schema for GhJSON documents (`ghjson.schema.json`).
- Component, connection, group, and metadata serialization rules.
- Prefixed string formats for geometric and special data types.
- Component-specific formats for Number Slider, Panel, Value List, Script components (C#, Python, IronPython, VB), old GhPython, and Scribble.
- GhPatch format specification for diffs and partial edits.
- JSON Schema for GhPatch documents (`ghpatch.schema.json`).
- Pagination support for partial documents:
  - `metadata.pagination` with `page`, `pageSize`, and `totalPages`
  - `connection.boundary` flag for cross-page connections

### Changed

- **GhPatch add operations no longer accept `instanceGuid`.**
  - `patch.components.add` and `patch.groups.add` entries must not specify `instanceGuid`; the updated GhPatch schema prohibits it.
  - Added `componentAdd` and `groupAdd` schema definitions that reference `ghjson.schema.json` definitions while forbidding `instanceGuid`.
  - Validation must reject any `.ghpatch` that includes `instanceGuid` in an `add` operation.
  - The `instance_guid_collision` apply conflict has been replaced by `id_collision` for the case where an added component id collides with an existing component and id renumbering is disabled.
- Clarified `instanceGuid` placement semantics: a component whose `instanceGuid` matches an existing canvas object updates that object in place (preserving identity, position, and wiring) instead of producing a duplicate; to place a copy, omit `instanceGuid`.
- Clarified that `connections` are document-scoped: endpoints reference components declared in the same document by `id`; wiring to pre-existing canvas objects requires a separate mechanism (e.g. a connect operation keyed by `instanceGuid`).
- Generation guidance in §3.4 and examples in §3.1, §7 and §9 no longer carry `componentGuid`/`instanceGuid`: generated documents must identify new components by `name` plus `id` only, because a wrong-but-plausible `componentGuid` silently instantiates a different component than `name` suggests. `componentGuid` should only be emitted when it is the component type's real GUID copied from a serialized document.
- Rewrote and reorganized `README.md` with an updated Quick Look example (omitting `componentGuid`, `instanceGuid`, and `pivot` on newly generated components), specification/schema reference table, and ecosystem status.
