# GhJSON Format Specification

**Version**: 1.0  
**Status**: Draft  
**Last Updated**: 2026-01-12

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [Document Structure](#2-document-structure)
3. [Components](#3-components)
4. [Connections](#4-connections)
5. [Groups](#5-groups)
6. [Data Types](#6-data-types)
7. [Component-Specific Formats](#7-component-specific-formats)
8. [Validation](#8-validation)
9. [Examples](#9-examples)

---

## 1. Introduction

### 1.1 Purpose

GhJSON is a JSON-based format for representing Grasshopper definitions. It provides a human-readable, portable way to serialize and deserialize Grasshopper documents, enabling:

- **Version control**: Track changes to Grasshopper definitions in Git
- **AI integration**: Enable AI tools to read and generate Grasshopper definitions
- **Cross-platform sharing**: Share definitions without binary format dependencies
- **Automation**: Programmatically create and modify definitions

### 1.2 Design Principles

1. **Human-readable**: JSON format with meaningful property names
2. **Compact**: Optimized representations (e.g., compact pivot format)
3. **Complete**: Captures all necessary information to reconstruct a definition
4. **Extensible**: Supports custom properties and future enhancements
5. **Backward-compatible**: Legacy formats are supported during deserialization

### 1.3 File Extension

GhJSON files use the `.ghjson` extension.

### 1.4 Encoding

GhJSON files MUST be encoded in UTF-8 without BOM.

---

## 2. Document Structure

A GhJSON document is a JSON object with the following top-level structure:

```json
{
  "schemaVersion": "1.0",
  "metadata": { ... },
  "components": [ ... ],
  "connections": [ ... ],
  "groups": [ ... ]
}
```

### 2.1 Properties

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `schemaVersion` | string | No | GhJSON schema version (default: "1.0") |
| `metadata` | object | No | Document metadata |
| `components` | array | **Yes** | List of components |
| `connections` | array | No | List of connections |
| `groups` | array | No | List of groups |

### 2.2 Metadata

The `metadata` object contains information about the document:

```json
{
  "metadata": {
    "description": "A parametric tower generator",
    "author": "Jane Smith",
    "version": "2",
    "created": "2026-01-10T14:30:00Z",
    "modified": "2026-01-11T09:15:00Z",
    "rhinoVersion": "8.24",
    "grasshopperVersion": "1.0.0007",
    "dependencies": ["Kangaroo2", "LunchBox"],
    "componentCount": 45,
    "connectionCount": 12,
    "groupCount": 3
  }
}
```

| Property | Type | Description |
|----------|------|-------------|
| `description` | string | Description of the definition |
| `author` | string | Author name |
| `version` | string | Definition version |
| `created` | string | ISO 8601 creation timestamp |
| `modified` | string | ISO 8601 modification timestamp |
| `rhinoVersion` | string | Target Rhino version |
| `grasshopperVersion` | string | Target Grasshopper version |
| `dependencies` | string[] | Required plugin dependencies |
| `componentCount` | integer | Total component count |
| `connectionCount` | integer | Total connection count |
| `groupCount` | integer | Total group count |

---

## 3. Components

Components are the core elements of a GhJSON document. Each component represents a Grasshopper component or parameter on the canvas.

### 3.1 Component Structure

```json
{
  "name": "Addition",
  "library": "Maths",
  "nickName": "Add",
  "componentGuid": "a0d62394-a118-422d-abb3-6af115c75b25",
  "instanceGuid": "12345678-1234-1234-1234-123456789abc",
  "id": 1,
  "pivot": "100,200",
  "properties": {
    "NickName": "Custom Name"
  },
  "inputSettings": [ ... ],
  "outputSettings": [ ... ],
  "componentState": { ... }
}
```

### 3.2 Component Properties

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `name` | string | Conditional | Display name of the component |
| `library` | string | No | Component library/category |
| `nickName` | string | No | Custom nickname |
| `componentGuid` | uuid | No | Component type GUID |
| `instanceGuid` | uuid | No | Instance GUID |
| `id` | integer | Conditional | Integer ID for references (required when referenced by connections/groups) |
| `pivot` | string | No | Canvas position (compact string format: "X,Y") |
| `properties` | object | No | Simple key-value properties |
| `inputSettings` | array | No | Input parameter settings |
| `outputSettings` | array | No | Output parameter settings |
| `componentState` | object | No | UI-specific state |
| `warnings` | string[] | No | Warning messages |
| `errors` | string[] | No | Error messages |

### 3.3 Pivot Format

The `pivot` property uses a compact string format:
```json
"pivot": "100.5,200.25"
```

### 3.4 Component Identification

Components can be identified with any of these combinations (schema uses `anyOf`):

1. **By Name + ID**: `name` with compact integer `id`
2. **By Name + Instance GUID**: `name` plus `instanceGuid`
3. **By Component GUID + ID**: `componentGuid` plus `id`
4. **By Component GUID + Instance GUID**: `componentGuid` plus `instanceGuid`

You MAY include both `componentGuid` and `name` together. For reliable instantiation, include `componentGuid` and an `id` when the component will be referenced by connections or groups.

### 3.5 Integer IDs

The `id` property provides a compact integer reference for use in connections and groups. IDs:

- MUST be positive integers (≥ 1)
- MUST be unique within the document
- SHOULD be sequential (1, 2, 3, ...)

---

## 4. Connections

Connections represent the wires between component parameters.

### 4.1 Connection Structure

```json
{
  "connections": [
    {
      "from": { "id": 1, "paramIndex": 0 },
      "to": { "id": 2, "paramIndex": 0 }
    }
  ]
}
```

### 4.2 Connection Properties

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `from` | endpoint | **Yes** | Source endpoint (output) |
| `to` | endpoint | **Yes** | Target endpoint (input) |

### 4.3 Endpoint Properties

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `id` | integer | **Yes** | Component integer ID |
| `paramName` | string | Conditional | Parameter name (required if `paramIndex` is not provided) |
| `paramIndex` | integer | Conditional | Zero-based parameter index (required if `paramName` is not provided) |

The `paramIndex` provides reliable parameter matching when parameter names may vary due to localization or custom nicknames.

---

## 5. Groups

Groups organize components visually on the canvas.

### 5.1 Group Structure

```json
{
  "groups": [
    {
      "instanceGuid": "abcd1234-5678-90ab-cdef-1234567890ab",
      "id": 1,
      "name": "Input Parameters",
      "color": "255,200,220,255",
      "members": [1, 2, 3]
    }
  ]
}
```

### 5.2 Group Properties

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `instanceGuid` | uuid | Conditional | Group instance GUID (required if `id` is not provided; both may be present) |
| `id` | integer | Conditional | Group integer ID (required if `instanceGuid` is not provided; both may be present) |
| `name` | string | No | Group name/nickname |
| `color` | string | No | ARGB color (A,R,G,B format), each channel 0–255 |
| `members` | integer[] | **Yes** | Component IDs in this group |

---

## 6. Data Types

GhJSON uses prefixed string formats for geometric and special data types.

### 6.1 Basic Types

| Type | Format | Example |
|------|--------|---------|
| Text | `text:value` | `"text:Hello World"` |
| Number | `number:value` | `"number:3.14159"` |
| Integer | `integer:value` | `"integer:42"` |
| Boolean | `boolean:value` | `"boolean:true"` |

### 6.2 Geometric Types

| Type | Format | Example |
|------|--------|---------|
| Point | `pointXYZ:x,y,z` | `"pointXYZ:1,2,3"` |
| Vector | `vectorXYZ:x,y,z` | `"vectorXYZ:0,0,1"` |
| Line | `line2p:x1,y1,z1;x2,y2,z2` | `"line2p:0,0,0;1,0,0"` |
| Plane | `planeOXY:ox,oy,oz;xx,xy,xz;yx,yy,yz` | `"planeOXY:0,0,0;1,0,0;0,1,0"` |
| Circle | `circleCNRS:cx,cy,cz;nx,ny,nz;r;sx,sy,sz` | `"circleCNRS:0,0,0;0,0,1;5;1,0,0"` |
| Arc | `arc3P:x1,y1,z1;x2,y2,z2;x3,y3,z3` | `"arc3P:0,0,0;1,1,0;2,0,0"` |
| Box | `boxOXY:ox,oy,oz;xx,xy,xz;yx,yy,yz;x0,x1;y0,y1;z0,z1` | See spec |
| Rectangle | `rectangleCXY:cx,cy,cz;xx,xy,xz;yx,yy,yz;w,h` | See spec |
| Interval | `interval:min<max` | `"interval:0<10"` |
| Color | `argb:a,r,g,b` | `"argb:255,128,64,32"` |

---

## 7. Component-Specific Formats

### 7.1 Number Slider

Number sliders use a compact value format:

```json
{
  "name": "Number Slider",
  "componentState": {
    "value": "5.5<0,10>"
  }
}
```

Format: `value<min,max>` where:
- `value` is the current value
- `min` is the minimum value
- `max` is the maximum value
- Decimal precision is determined by the value with the most decimal places

Additional properties:
- `rounding`: Rounding mode ("Round", "None", "Even", "Odd")

### 7.2 Panel

```json
{
  "name": "Panel",
  "componentState": {
    "value": "Hello World",
    "multiline": true,
    "wrap": true,
    "drawIndices": false,
    "drawPaths": true,
    "alignment": 0,
    "bounds": "150x100"
  }
}
```

### 7.3 Value List

```json
{
  "name": "Value List",
  "componentState": {
    "value": [
      { "Name": "Option A", "Expression": "0" },
      { "Name": "Option B", "Expression": "1" },
      { "Name": "Option C", "Expression": "2" }
    ],
    "listMode": "DropDown",
    "selectedIndices": [0]
  }
}
```

List modes: `DropDown`, `CheckList`, `Sequence`, `Cycle`, `Toggle`

### 7.4 Script Components (C#, Python)

```json
{
  "name": "C# Script",
  "inputSettings": [
    {
      "parameterName": "x",
      "variableName": "x",
      "typeHint": "double",
      "access": "item"
    }
  ],
  "outputSettings": [
    {
      "parameterName": "A",
      "variableName": "A",
      "typeHint": "object"
    }
  ],
  "componentState": {
    "value": "A = x * 2;",
    "marshInputs": true,
    "marshOutputs": true,
    "showStandardOutput": false
  }
}
```

### 7.5 VB Script

VB Script components have three code sections:

```json
{
  "name": "VB Script",
  "componentState": {
    "vbCode": {
      "imports": "Imports System.Math",
      "script": "A = Sin(x)",
      "additional": "' Helper functions here"
    }
  }
}
```

### 7.6 Scribble

```json
{
  "name": "Scribble",
  "componentState": {
    "value": "Design Notes",
    "font": {
      "name": "Arial",
      "size": 14,
      "bold": true,
      "italic": false
    },
    "corners": [
      "100,100",
      "300,100",
      "300,200",
      "100,200"
    ]
  }
}
```

---

## 8. Validation

### 8.1 Schema Validation

GhJSON documents SHOULD validate against the JSON Schema at:
```text
https://architects-toolkit.github.io/ghjson-spec/schema/v1.0/ghjson.schema.json
```

### 8.2 Structural Validation

Beyond schema validation, implementations SHOULD verify:

1. **Unique IDs**: All component `id` values are unique
2. **Valid references**: All connection endpoint `id` values reference existing components
3. **Valid parameter names**: Connection `paramName` values exist on referenced components
4. **Group membership**: All group `members` reference existing component IDs

### 8.3 Grasshopper Validation

When deserializing, implementations MAY additionally verify:

1. **Component existence**: The `componentGuid` or `name` matches an installed component
2. **Type compatibility**: Connected parameter types are compatible
3. **Plugin dependencies**: Required plugins are installed

---

## 9. Examples

### 9.1 Minimal Document

```json
{
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

### 9.2 Simple Addition

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
    },
    {
      "name": "Number Slider",
      "instanceGuid": "22222222-2222-2222-2222-222222222222",
      "id": 2,
      "pivot": "100,150",
      "componentState": { "value": "3<0,10>" }
    },
    {
      "name": "Addition",
      "componentGuid": "a0d62394-a118-422d-abb3-6af115c75b25",
      "instanceGuid": "33333333-3333-3333-3333-333333333333",
      "id": 3,
      "pivot": "300,125"
    },
    {
      "name": "Panel",
      "instanceGuid": "44444444-4444-4444-4444-444444444444",
      "id": 4,
      "pivot": "500,125"
    }
  ],
  "connections": [
    { "from": { "id": 1, "paramName": "Number" }, "to": { "id": 3, "paramName": "A" } },
    { "from": { "id": 2, "paramName": "Number" }, "to": { "id": 3, "paramName": "B" } },
    { "from": { "id": 3, "paramName": "Result" }, "to": { "id": 4, "paramName": "Input" } }
  ]
}
```

### 9.3 With Groups and Metadata

```json
{
  "schemaVersion": "1.0",
  "metadata": {
    "description": "Simple addition example",
    "author": "GhJSON Team",
    "created": "2026-01-11T10:00:00Z"
  },
  "components": [
    {
      "name": "Number Slider",
      "instanceGuid": "11111111-1111-1111-1111-111111111111",
      "id": 1,
      "pivot": "100,100",
      "componentState": { "value": "5<0,10>" }
    },
    {
      "name": "Number Slider",
      "instanceGuid": "22222222-2222-2222-2222-222222222222",
      "id": 2,
      "pivot": "100,150",
      "componentState": { "value": "3<0,10>" }
    },
    {
      "name": "Addition",
      "instanceGuid": "33333333-3333-3333-3333-333333333333",
      "id": 3,
      "pivot": "300,125"
    }
  ],
  "connections": [
    { "from": { "id": 1, "paramName": "Number" }, "to": { "id": 3, "paramName": "A" } },
    { "from": { "id": 2, "paramName": "Number" }, "to": { "id": 3, "paramName": "B" } }
  ],
  "groups": [
    {
      "instanceGuid": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "name": "Inputs",
      "color": "255,200,220,255",
      "members": [1, 2]
    }
  ]
}
```

---

## Appendix A: MIME Type

The recommended MIME type for GhJSON files is:

```text
application/vnd.grasshopper.ghjson+json
```

## Appendix B: Schema Registry

The GhJSON schema is registered at:

- **GitHub**: https://github.com/architects-toolkit/ghjson-spec
- **Schema URL**: https://architects-toolkit.github.io/ghjson-spec/schema/v1.0/ghjson.schema.json

## Appendix C: Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-01-11 | Initial specification |
