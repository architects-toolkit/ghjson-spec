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
