---
layout: default
title: Home
---

## GhJSON Specification

GhJSON is a JSON-based format for representing [Grasshopper](https://www.grasshopper3d.com/) definitions. It provides a human-readable, portable way to serialize and deserialize Grasshopper documents.

### GhPatch

GhJSON also defines a sibling **GhPatch** format for representing diffs and partial edits on GhJSON documents.

### File Extensions

- GhJSON documents use the `.ghjson` extension.
- GhPatch documents (diffs and partial edits) use the `.ghpatch` extension.

## Quick Links

- [Format Specification](docs/specification.md)
- [GhPatch Specification](docs/ghpatch.md)
- [JSON Schema v1.0](schema/v1.0/ghjson.schema.json)
- [GhPatch JSON Schema v1.0](schema/v1.0/ghpatch.schema.json)
- [Examples](examples/)
- [GitHub Repository](https://github.com/architects-toolkit/ghjson-spec)

## Schema URLs

```text
https://architects-toolkit.github.io/ghjson-spec/schema/v1.0/ghjson.schema.json
https://architects-toolkit.github.io/ghjson-spec/schema/v1.0/ghpatch.schema.json
```

## Example

```json
{
  "schema": "1.0",
  "components": [
    {
      "name": "Number Slider",
      "instanceGuid": "11111111-1111-1111-1111-111111111111",
      "id": 1,
      "pivot": "100,100",
      "componentState": {
        "extensions": {
          "gh.numberslider": {
            "value": "5<0~10>"
          }
        }
      }
    }
  ]
}
```
