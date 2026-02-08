# GhJSON Extensions

GhJSON supports **extensions** to allow new object-handler features to evolve without breaking the core specification.

Extensions are used for both:

- Native Grasshopper-specific component state (under the `gh.*` namespace).
- Third-party tools and plugins.

Extensions are intentionally constrained to avoid schema fragmentation:

- Extensions MAY only appear under:
  - `metadata.extensions`
  - `components[].componentState.extensions`
- Extensions MUST NOT change the meaning of core properties.

## 1. Goals

- Allow vendors/tools to add extra metadata for advanced reconstruction.
- Keep the base schema stable and easy to validate.
- Avoid introducing new top-level properties that would require schema version bumps.

## 2. Extension shape

An extension container is an object where:

- Each key SHOULD be namespaced, e.g.:
  - `gh.panel`
  - `gh.scribble`
  - `gh.valuelist`
  - `vendor.plugin.feature`
- Each value MUST be an object.

Example:

```json
{
  "metadata": {
    "title": "Example",
    "extensions": {
      "gh.panel": {
        "text": "Hello World",
        "multiline": true,
        "wrap": true,
        "alignment": "Left",
        "bounds": ["150x100"]
      }
    }
  },
  "components": [
    {
      "name": "Panel",
      "id": 1,
      "componentState": {
        "extensions": {
          "gh.panel": {
            "text": "Hello World",
            "multiline": true,
            "wrap": true,
            "alignment": "Left",
            "bounds": ["150x100"]
          }
        }
      }
    }
  ]
}
```

## 3. How extensions relate to object handlers

In implementations, extensions typically map to **object handlers**:

- A handler MAY emit an extension block when serializing.
- A handler MAY consume an extension block when deserializing.
- Handlers SHOULD ignore unknown extension keys.

This creates a stable contract:

- Core properties remain portable.
- Optional extra fidelity is available when the corresponding handler/plugin is installed.

The `gh.*` namespace is reserved for native Grasshopper-specific extension keys that are broadly useful but would otherwise make the base schema large and hard to maintain.

## 4. Where to place extension specifications in the ghjson-spec repo

Keep the base spec readable by placing extension specifications under a dedicated folder:

- `docs/extensions/`
  - `index.md`
  - `gh.panel.md`
  - `gh.scribble.md`
  - `gh.valuelist.md`
  - `gh.vbscript.md`
  - `gh.ghpython.md`
  - `gh.numberslider.md`
  - `vendor.<name>.<feature>.md`

`docs/extensions.md` should remain the entry point describing the extension mechanism, while each extension file documents its payload.

## 5. Built-in Grasshopper extension examples

All examples below live under `components[].componentState.extensions`.

### 5.1 `gh.panel`

```json
{
  "componentState": {
    "extensions": {
      "gh.panel": {
        "text": "Hello World",
        "multiline": true,
        "wrap": true,
        "alignment": "Left",
        "bounds": ["150x100"]
      }
    }
  }
}
```

### 5.2 `gh.scribble`

Corners A, B, D are stored as offsets relative to the component pivot. Corner C is derived as `B + D - A` (parallelogram rule). See [specification §7.7](specification.md#77-scribble) for details.

```json
{
  "componentState": {
    "extensions": {
      "gh.scribble": {
        "text": "Design Notes",
        "fontFamily": "Arial",
        "fontSize": 14,
        "bold": true,
        "italic": false,
        "corners": ["0,0", "200,0", "0,100"]
      }
    }
  }
}
```

### 5.3 `gh.valuelist`

```json
{
  "componentState": {
    "extensions": {
      "gh.valuelist": {
        "listMode": "DropDown",
        "items": [
          { "name": "Option A", "expression": "0", "selected": true },
          { "name": "Option B", "expression": "1" },
          { "name": "Option C", "expression": "2" }
        ]
      }
    }
  }
}
```

### 5.4 `gh.vbscript`

```json
{
  "componentState": {
    "extensions": {
      "gh.vbscript": {
        "vbCode": {
          "imports": "Imports System.Math",
          "script": "A = Sin(x)",
          "additional": "' Helper functions here"
        }
      }
    }
  }
}
```

### 5.5 `gh.ghpython`

Used for old GhPython script components (`ZuiPythonComponent` from `GhPython.dll`).
This covers the generic **GhPython Script** component, **Ladybug Tools**, **Honeybee**, **Dragonfly**, and any other plugin that ships pre-configured `ZuiPythonComponent` instances.

Unlike Rhino 8 script components (`gh.csharp`, `gh.python`, `gh.ironpython`) which implement `IScriptComponent`, old GhPython components do not expose that interface. Marshalling options are therefore **not applicable**.

```json
{
  "componentState": {
    "extensions": {
      "gh.ghpython": {
        "code": "import rhinoscriptsyntax as rs\nimport ghpythonlib.components as ghcomp\n\na = x + y",
        "showStandardOutput": true
      }
    }
  }
}
```

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `code` | string | **Yes** | Python (IronPython) script code |
| `showStandardOutput` | boolean | No | Whether the `out` standard output parameter is visible |
| `outModifiers` | object | No | Modifiers on the `out` parameter (same shape as other script extensions) |

> **Note:** For Ladybug/Honeybee components, the `code` property captures the embedded script
> for documentation and AI understanding. During deserialization on a machine where the plugin
> is installed, `AddedToDocument` may reinitialize the component from the plugin's own script files.

### 5.6 `gh.numberslider`

```json
{
  "componentState": {
    "extensions": {
      "gh.numberslider": {
        "value": "5.5<0~10>",
        "rounding": "Float"
      }
    }
  }
}
```

## 6. Publishing public extensions

To enable interoperability between tools, extensions SHOULD:

- Use a stable namespace (do not rename keys lightly).
- Publish:
  - The JSON shape for your extension values.
  - Supported versions and migration notes.
  - Any assumptions (e.g., requires a specific plugin installed).

Extension values SHOULD be **forward-compatible** (add new optional fields instead of changing meaning).

## 7. Official extension schemas (automated validation)

The GhJSON specification MAY publish official JSON Schemas for well-known extension keys.

In `schema/v1.0/`, the extension registry schema lives at:

- `extensions/extensions.schema.json`

This registry schema:

- Defines schemas for known keys (e.g., `gh.panel`).
- Allows unknown keys, but requires their values to be objects.

The main GhJSON schema references this registry at `metadata.extensions` and `components[].componentState.extensions`, so validators that validate a document against `ghjson.schema.json` automatically apply the official extension schemas.

## 8. Runtime registration

The GhJSON specification does not mandate a specific runtime registration mechanism.

At runtime, implementations that support a given extension key typically provide an object handler responsible for reading and writing:

- `metadata.extensions["your.namespace"]`
- `componentState.extensions["your.namespace"]`

The base schema guarantees that the extension container exists only in supported locations.
