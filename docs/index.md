# GhJSON Documentation

Welcome to the GhJSON specification documentation.

## Contents

- [**Format Specification**](specification.md) - Complete format reference
- [**JSON Schema**](../schema/v1.0/ghjson.schema.json) - Machine-readable validation schema

## What is GhJSON?

GhJSON is a JSON-based format for representing Grasshopper definitions. It provides a human-readable, portable way to serialize and deserialize Grasshopper documents.

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
  "schemaVersion": "1.0",
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
  "properties": { ... },
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

Validate your GhJSON files against the official schema:

```text
https://architects-toolkit.github.io/ghjson-spec/schema/v1.0/ghjson.schema.json
```

## Version History

| Version | Date | Description |
| ------- | ---- | ----------- |
| 1.0 | 2026-01-11 | Initial specification |
