# Changelog

All notable changes to the GhJSON specification are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

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
