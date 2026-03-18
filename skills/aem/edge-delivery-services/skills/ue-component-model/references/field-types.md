# Component Model Field Types Reference

## Table of Contents
1. [Field Structure](#field-structure)
2. [Component Types](#component-types)
3. [Value Types](#value-types)
4. [Field Properties](#field-properties)
5. [Conditional Fields](#conditional-fields)
6. [Select/Multiselect Options](#selectmultiselect-options)
7. [Container Fields](#container-fields)
8. [Tab Fields](#tab-fields)

---

## Field Structure

Every field in a component model follows this base structure:

```json
{
  "component": "<field-type>",
  "name": "<property-name>",
  "label": "<Display Label>",
  "valueType": "<data-type>",
  "value": "<default-value>",
  "description": "<helper-text>",
  "multi": false,
  "required": false
}
```

Only `component` and `name` are strictly required. `label` is strongly recommended.

---

## Component Types

### `text`
Simple text input field. Used for short strings.
```json
{
  "component": "text",
  "name": "title",
  "label": "Title",
  "valueType": "string"
}
```

### `text-input`
Alternative text input, typically used for form-style inputs. Functionally similar to `text` but rendered differently in UE.
```json
{
  "component": "text-input",
  "name": "variation",
  "value": "",
  "label": "Variation",
  "valueType": "string"
}
```

### `richtext`
Rich text editor with formatting controls (bold, italic, lists, links).
```json
{
  "component": "richtext",
  "name": "text",
  "value": "",
  "label": "Text",
  "valueType": "string"
}
```

### `reference`
Asset/media picker. Opens DAM browser for selecting images, videos, documents.
```json
{
  "component": "reference",
  "name": "image",
  "label": "Image",
  "valueType": "string",
  "multi": false
}
```
- Set `multi: true` for multiple asset selection
- Typically paired with a `text` field for alt text (e.g., `imageAlt`)

### `aem-content`
AEM content path picker. Used for selecting pages, URLs, or content paths.
```json
{
  "component": "aem-content",
  "name": "link",
  "label": "Link"
}
```
- Common for URLs, page links, navigation targets
- Values resolve to content paths or external URLs

### `aem-content-fragment`
Content Fragment picker with optional validation.
```json
{
  "component": "aem-content-fragment",
  "name": "articlepath",
  "value": "",
  "label": "Article Content Fragment path",
  "valueType": "string"
}
```

### `select`
Single-choice dropdown.
```json
{
  "component": "select",
  "name": "titleType",
  "label": "Title Type",
  "valueType": "string",
  "value": "h2",
  "options": [
    { "name": "H1", "value": "h1" },
    { "name": "H2", "value": "h2" },
    { "name": "H3", "value": "h3" }
  ]
}
```

### `multiselect`
Multiple-choice selector. Supports grouped options.
```json
{
  "component": "multiselect",
  "name": "style",
  "label": "Style",
  "valueType": "string",
  "options": [
    { "name": "Highlight", "value": "highlight" },
    { "name": "Dark", "value": "dark" }
  ]
}
```

**Grouped options** (with `children`):
```json
{
  "component": "multiselect",
  "name": "classes",
  "label": "Style",
  "valueType": "string",
  "maxSize": 3,
  "options": [
    {
      "name": "Theme",
      "children": [
        { "name": "Light", "value": "light" },
        { "name": "Dark", "value": "dark" }
      ]
    },
    {
      "name": "Alignment",
      "children": [
        { "name": "Left", "value": "left" },
        { "name": "Right", "value": "right" }
      ]
    }
  ]
}
```

### `boolean`
Toggle/checkbox field.
```json
{
  "component": "boolean",
  "name": "hideHeading",
  "label": "Hide Heading",
  "description": "Hide the heading of the block",
  "valueType": "boolean",
  "value": false
}
```

### `number`
Numeric input.
```json
{
  "component": "number",
  "name": "maxItems",
  "label": "Max Items",
  "valueType": "number",
  "description": "Maximum number of items to display"
}
```

### `container`
Groups nested fields together. Used for composite fields or repeated field groups.
```json
{
  "component": "container",
  "name": "ctas",
  "label": "Call to Actions",
  "collapsible": false,
  "multi": true,
  "fields": [
    { "component": "richtext", "name": "text", "label": "Text" },
    { "component": "aem-content", "name": "link", "label": "Link" }
  ]
}
```
- `collapsible`: Whether the group can be collapsed in the UI
- `multi: true`: Makes the container repeatable (add/remove instances)
- `fields`: Array of nested field definitions

### `tab`
Creates a tab separator in the properties panel. Not a data field — purely UI organization.
```json
{
  "component": "tab",
  "label": "Validation",
  "name": "validation"
}
```

### `date-time`
Date/time picker.
```json
{
  "component": "date-time",
  "name": "startDate",
  "label": "Start Date",
  "valueType": "date"
}
```

### `radio-group`
Radio button group for mutually exclusive choices.
```json
{
  "component": "radio-group",
  "name": "orientation",
  "label": "Display Options",
  "valueType": "string",
  "value": "horizontal",
  "options": [
    { "name": "Horizontally", "value": "horizontal" },
    { "name": "Vertically", "value": "vertical" }
  ]
}
```

---

## Value Types

| valueType | Description | Use with |
|-----------|-------------|----------|
| `"string"` | Text value (default) | text, richtext, select, reference, aem-content |
| `"number"` | Numeric value | number, text (for numeric input) |
| `"boolean"` | True/false | boolean |
| `"date"` | Date value | date-time |
| `"string[]"` | Array of strings | multiselect, checkbox-group |
| `"number[]"` | Array of numbers | checkbox-group |
| `"boolean[]"` | Array of booleans | checkbox-group |

---

## Field Properties

| Property | Type | Description |
|----------|------|-------------|
| `component` | string | **Required**. Field type (see above) |
| `name` | string | **Required**. Property name stored on the component |
| `label` | string | Display label in the property panel |
| `description` | string | Helper text shown below the field |
| `valueType` | string | Data type for the value |
| `value` | any | Default value |
| `multi` | boolean | Allow multiple values |
| `required` | boolean | Whether the field is required |
| `options` | array | Choices for select/multiselect/radio-group |
| `condition` | object | JSON Logic condition for showing/hiding the field |
| `validation` | object | Validation rules (regExp, rootPath, etc.) |
| `maxSize` | number | Max selections for multiselect |
| `collapsible` | boolean | For containers: whether they can collapse |

---

## Conditional Fields

Use JSON Logic syntax to show/hide fields based on other field values:

```json
{
  "component": "text",
  "name": "customUrl",
  "label": "Custom URL",
  "condition": {
    "==": [
      { "var": "linkType" },
      "custom"
    ]
  }
}
```

Operators: `==`, `===`, `!=`, `!==`, and standard JSON Logic operators.

---

## Select/Multiselect Options

### Flat options
```json
"options": [
  { "name": "Display Name", "value": "stored-value" }
]
```

### Grouped options (multiselect only)
```json
"options": [
  {
    "name": "Group Label",
    "children": [
      { "name": "Option A", "value": "a" },
      { "name": "Option B", "value": "b" }
    ]
  }
]
```

### With empty/default option
```json
"options": [
  { "name": "default", "value": "" },
  { "name": "primary", "value": "primary" },
  { "name": "secondary", "value": "secondary" }
]
```
