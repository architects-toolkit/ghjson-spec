# GhJSON Specification

[![Schema Version](https://img.shields.io/badge/schema-v1.0-blue)](https://architects-toolkit.github.io/ghjson-spec/schema/v1.0/ghjson.schema.json)
[![License](https://img.shields.io/badge/license-CC0%201.0%20Universal-white)](LICENSE)
[![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/architects-toolkit/ghjson-spec)

**GhJSON** is an open, human-readable JSON format for representing [Grasshopper 3D](https://discourse.mcneel.com/c/grasshopper) definitions (`.ghjson`) and structural diffs/edits (**GhPatch**, `.ghpatch`).

It is designed as a community standard so AI tools, version control systems, and external scripts can read, generate, diff, and patch Grasshopper definitions without binary dependencies or token-heavy XML.

> Built on ideas pioneered by community formats such as [GHPT](https://github.com/enmerk4r/GHPT) and [WolfParametric's wolf-community-scripts](https://huggingface.co/datasets/WolfParametric/wolf-community-scripts).

## Why GhJSON?

- **`.gh` is binary** — opaque to Git diffs, code review, and language models.
- **`.ghx` is verbose XML** — a handful of components quickly consumes thousands of LLM tokens.
- **GhJSON is compact, structured JSON** — captures components, connections, groups, parameter modifiers, internalized values, and component-specific state (sliders, panels, scripts) in a fraction of the size.

### Common use cases

- **AI-assisted design** — Let LLMs inspect a canvas, generate new definitions, or propose surgical `.ghpatch` edits.
- **Version control & code review** — Track Grasshopper changes in Git with readable diffs and checksummed patches.
- **Automation & headless pipelines** — Build, validate, merge, or inspect definitions from .NET, Python, or web tools without launching Rhino.

## Quick look

A minimal `.ghjson` document wiring a **Number Slider** into an **Addition** component:

```json
{
  "schema": "1.0",
  "components": [
    {
      "name": "Number Slider",
      "id": 1,
      "pivot": "100,100",
      "componentState": {
        "extensions": {
          "gh.numberslider": {
            "value": "5<0~10>"
          }
        }
      }
    },
    {
      "name": "Addition",
      "id": 2,
      "pivot": "300,100"
    }
  ],
  "connections": [
    {
      "from": { "id": 1, "paramName": "Number" },
      "to": { "id": 2, "paramName": "A" }
    }
  ]
}
```

> **Tip for AI generators:** When generating *new* components, omit `componentGuid`, `instanceGuid`, and `pivot` and specify `"name"` + `"id"` — the host resolves each component by name, assigns fresh instance GUIDs, and computes a compact layout automatically. Include `instanceGuid` only when referencing or updating an existing canvas object, and `pivot` only when preserving existing coordinates.

## Specification & schemas

| Resource | Description |
|---|---|
| [**GhJSON Format Specification**](docs/specification.md) | Full reference for `.ghjson` documents: metadata, components, connections, groups, data types, and extensions |
| [**GhPatch Specification**](docs/ghpatch.md) | Full reference for `.ghpatch` diffs and partial edits (`add`, `remove`, `modify`) |
| [**Extensions Guide**](docs/extensions.md) | How to define and publish custom `componentState.extensions` and data types |
| [**`ghjson.schema.json` (v1.0)**](schema/v1.0/ghjson.schema.json) | Canonical JSON Schema (Draft 2020-12) for `.ghjson` documents |
| [**`ghpatch.schema.json` (v1.0)**](schema/v1.0/ghpatch.schema.json) | Canonical JSON Schema (Draft 2020-12) for `.ghpatch` documents |

### Hosted schema URLs

```text
https://architects-toolkit.github.io/ghjson-spec/schema/v1.0/ghjson.schema.json
https://architects-toolkit.github.io/ghjson-spec/schema/v1.0/ghpatch.schema.json
```

### Validate from the command line

```bash
npm install -g ajv-cli ajv-formats
ajv validate -s schema/v1.0/ghjson.schema.json -d your-file.ghjson --spec=draft2020 -c ajv-formats
```

## Ecosystem & implementations

| Project | Role | Status |
|---|---|---|
| [**ghjson-dotnet**](https://github.com/architects-toolkit/ghjson-dotnet) | Official .NET SDK (`GhJSON.Core` & `GhJSON.Grasshopper` on NuGet) for reading, writing, validating, diffing, and placing GhJSON | Stable |
| [**SmartHopper**](https://github.com/architects-toolkit/SmartHopper) | Open-source AI assistant and component suite for Grasshopper 3D powered by GhJSON | Active |
| **ghjson-web** | Browser-based viewer and validator | Planned |
| **ghjson-python** | Python library for `.ghjson` / `.ghpatch` manipulation | Planned |

## Contributing & license

This repository contains the format specification and JSON Schemas. Issues and pull requests are welcome — simply fork the repository, make your changes on a feature branch, and open a pull request.

Released into the public domain under [CC0 1.0 Universal](LICENSE).
