---
layout: default
title: Home
---

## GhJSON Specification

GhJSON is a JSON-based format for representing [Grasshopper](https://www.grasshopper3d.com/) definitions.

## Quick Links

- [Format Specification](docs/specification.md)
- [JSON Schema v1.0](schema/v1.0/ghjson.schema.json)
- [Examples](examples/)
- [GitHub Repository](https://github.com/architects-toolkit/ghjson-spec)

## Schema URL

```text
https://architects-toolkit.github.io/ghjson-spec/schema/v1.0/ghjson.schema.json
```

## Example

```json
{
  "schemaVersion": "1.0",
  "components": [
    {
      "name": "Number Slider",
      "instanceGuid": "11111111-1111-1111-1111-111111111111",
      "id": 1,
      "pivot": "100,100",
      "componentState": { "value": "5<0,10>" }
    }
  ]
}
```

## Implementations

| Language | Package | Status |
| -------- | ------- | ------ |
| .NET | [ghjson-dotnet](https://github.com/architects-toolkit/ghjson-dotnet) | In Development |
| Web | [ghjson-web](https://github.com/architects-toolkit/ghjson-web) | Planned |
