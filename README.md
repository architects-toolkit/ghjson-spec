# GhJSON Specification

[![Schema Version](https://img.shields.io/badge/schema-v1.0-blue)](https://architects-toolkit.github.io/ghjson-spec/schema/v1.0/ghjson.schema.json)
[![License](https://img.shields.io/badge/license-CC0%201.0%20Universal-white)](LICENSE)

GhJSON is a JSON-based format for representing [Grasshopper](https://discourse.mcneel.com/c/grasshopper) definitions. It provides a human-readable, portable way to serialize and deserialize Grasshopper documents.

> ⚠️ **Warning:** This specification is still under development. Please do not use it in production yet.

**This repository establishes a community-driven standard for JSON-based Grasshopper definition representation, enabling open-source initiatives to adopt a compatible format.**

This format builds upon existing JSON-based Grasshopper formats, such as those used by [GHPT](https://github.com/enmerk4r/GHPT) and [WolfParametric's wolf-community-scripts](https://huggingface.co/datasets/WolfParametric/wolf-community-scripts).

## Why GhJSON?

With the growth of AI and the potential for generating, optimizing, and fixing Grasshopper definitions with LLMs, a standard text-based format for representing Grasshopper definitions has become essential.

- The default `.gh` file format is binary, making it neither human-readable nor portable.
- The `.ghx` file format is XML-based, but its verbosity results in excessive token consumption for LLMs.
- Most LLMs are JSON-compatible, making JSON the natural choice for sending Grasshopper definitions to AI tools.

## Potential uses

GhJSON is a minimal, human-readable JSON-based format for Grasshopper files that could be used for:

- **Version control** - Track changes to Grasshopper definitions in Git
- **AI integration** - Enable AI tools to read and generate Grasshopper definitions
- **Cross-platform sharing** - Share definitions without binary format dependencies
- **Automation** - Programmatically create and modify definitions
- **Any other use case** that requires a text-based representation of Grasshopper definitions

This repository is JUST the specification for GhJSON format files, not an implementation for a specific use case.

## Projects Known to Use GhJSON

| Name | Link | Description |
| -------- | ------- | ------ |
| SmartHopper | [SmartHopper](https://github.com/architects-toolkit/SmartHopper) | An open-source plugin that implements third-party AI APIs to provide advanced features for Grasshopper3D. |

## Quick Start

### Example Document

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
    },
    {
      "name": "Addition",
      "instanceGuid": "22222222-2222-2222-2222-222222222222",
      "id": 2,
      "pivot": "300,100"
    }
  ],
  "connections": [
    { "from": { "id": 1, "paramName": "Number" }, "to": { "id": 2, "paramName": "A" } }
  ]
}
```

### Validate with JSON Schema

```bash
# Using ajv-cli
npm install -g ajv-cli ajv-formats
ajv validate -s schema/v1.0/ghjson.schema.json -d your-file.ghjson --spec=draft2020 -c ajv-formats
```

## Documentation

- [**Format Specification**](docs/specification.md) - Complete format documentation
- [**JSON Schema**](schema/v1.0/ghjson.schema.json) - Machine-readable schema

## File Extension

GhJSON files use the `.ghjson` extension.

## Schema URL

The canonical JSON Schema is available at:

```text
https://architects-toolkit.github.io/ghjson-spec/schema/v1.0/ghjson.schema.json
```

## Contributing

Contributions are welcome! Please read our contributing guidelines before submitting issues or pull requests.

### Development

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## Useful Tools to Implement GhJSON

| Language | Package | Status |
| -------- | ------- | ------ |
| .NET | [ghjson-dotnet](https://github.com/architects-toolkit/ghjson-dotnet) | In Development |
| Python | ghjson-python | Planned |
| PowerShell | ghjson-powershell | Planned |

## License

This specification is licensed under the [CC0-1.0 Universal License](LICENSE).

## Related Projects

- [SmartHopper](https://github.com/architects-toolkit/SmartHopper) - AI-powered Grasshopper plugin (original GhJSON implementation)
