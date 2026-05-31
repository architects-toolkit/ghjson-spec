# GhJSON Documentation

Welcome to the GhJSON specification documentation.

## Contents

- [**Format Specification**](specification.md) - Complete format reference
- [**GhPatch Specification**](ghpatch.md) - Sibling patch format for diffing and partial edits
- [**Extensions**](extensions.md) - How to extend GhJSON safely and publish extensions
- [**JSON Schema (ghjson)**](../schema/v1.0/ghjson.schema.json) - Machine-readable schema for documents
- [**JSON Schema (ghpatch)**](../schema/v1.0/ghpatch.schema.json) - Machine-readable schema for patches

## What is GhJSON?

GhJSON is a JSON-based format for representing Grasshopper definitions. It provides a human-readable, portable way to serialize and deserialize Grasshopper documents. GhJSON files use the `.ghjson` extension.

GhJSON also has a sibling **[GhPatch](ghpatch.md)** format for representing diffs and partial edits on GhJSON documents. GhPatch files use the `.ghpatch` extension.

### Key Features

- **Human-readable** - JSON format with meaningful property names
- **Compact** - Optimized representations (e.g., compact pivot format `"100,200"`)
- **Complete** - Captures all necessary information to reconstruct a definition
- **Extensible** - Supports custom properties and future enhancements
- **Backward-compatible** - Legacy formats supported during deserialization

## Quick Reference

### Document Structure

```json
{
  "schema": "1.0",
  "metadata": { ... },
  "components": [ ... ],
  "connections": [ ... ],
  "groups": [ ... ]
}
```

### Component Structure

```json
{
  "name": "Addition",
  "componentGuid": "a0d62394-a118-422d-abb3-6af115c75b25",
  "instanceGuid": "12345678-1234-1234-1234-123456789abc",
  "id": 1,
  "pivot": "100,200",
  "inputSettings": [ ... ],
  "outputSettings": [ ... ],
  "componentState": { ... }
}
```

### Connection Structure

```json
{
  "from": { "id": 1, "paramIndex": 0 },
  "to": { "id": 2, "paramIndex": 0 }
}
```

## Schema Validation

Validate your GhJSON files against the official schemas:

```text
https://architects-toolkit.github.io/ghjson-spec/schema/v1.0/ghjson.schema.json
https://architects-toolkit.github.io/ghjson-spec/schema/v1.0/ghpatch.schema.json
```
