# Real Examples from the Codebase

## Table of Contents
1. [Simple Block: Hero](#simple-block-hero)
2. [Simple Block: Embed](#simple-block-embed)
3. [Container Block: Cards](#container-block-cards)
4. [Block with Variants: Teaser](#block-with-variants-teaser)
5. [Key-Value Block: Product Details](#key-value-block-product-details)
6. [Block with Content Fragment: Article](#block-with-content-fragment-article)
7. [Section Configuration](#section-configuration)

---

## Simple Block: Hero

A basic block with an image, alt text, and rich text content.

### _hero.json (distributed config)
```json
{
  "definitions": [
    {
      "title": "Hero",
      "id": "hero",
      "plugins": {
        "xwalk": {
          "page": {
            "resourceType": "core/franklin/components/block/v1/block",
            "template": {
              "name": "Hero",
              "model": "hero"
            }
          }
        }
      }
    }
  ],
  "models": [
    {
      "id": "hero",
      "fields": [
        {
          "component": "reference",
          "valueType": "string",
          "name": "image",
          "label": "Image",
          "multi": false
        },
        {
          "component": "text",
          "valueType": "string",
          "name": "imageAlt",
          "label": "Alt",
          "value": ""
        },
        {
          "component": "richtext",
          "name": "text",
          "value": "",
          "label": "Text",
          "valueType": "string"
        }
      ]
    }
  ],
  "filters": []
}
```

**Key patterns**:
- `image` + `imageAlt` pair: The pipeline collapses these into `<picture><img alt="...">`
- `richtext` for flexible content with formatting

---

## Simple Block: Embed

A block that embeds external content (YouTube, Vimeo, Twitter).

### component-definition.json entry
```json
{
  "title": "Embed",
  "id": "embed",
  "plugins": {
    "xwalk": {
      "page": {
        "resourceType": "core/franklin/components/block/v1/block",
        "template": {
          "name": "Embed",
          "model": "embed"
        }
      }
    }
  }
}
```

### component-models.json entry
```json
{
  "id": "embed",
  "fields": [
    {
      "component": "aem-content",
      "valueType": "string",
      "name": "url",
      "label": "URL"
    },
    {
      "component": "reference",
      "valueType": "string",
      "name": "image",
      "label": "Placeholder Image",
      "multi": false
    },
    {
      "component": "text",
      "valueType": "string",
      "name": "imageAlt",
      "label": "Placeholder Image Alt Text",
      "value": ""
    }
  ]
}
```

### component-filters.json entry
The embed block appears in the `section` filter:
```json
{
  "id": "section",
  "components": ["text", "image", "button", "title", "hero", "...", "embed"]
}
```

**Key patterns**:
- `aem-content` for URL field (allows AEM content paths and external URLs)
- Optional `image` + `imageAlt` for placeholder thumbnail

---

## Container Block: Cards

A block with repeatable child items.

### _cards.json
```json
{
  "definitions": [
    {
      "title": "Cards",
      "id": "cards",
      "plugins": {
        "xwalk": {
          "page": {
            "resourceType": "core/franklin/components/block/v1/block",
            "template": {
              "name": "Cards",
              "filter": "cards"
            }
          }
        }
      }
    },
    {
      "title": "Card",
      "id": "card",
      "plugins": {
        "xwalk": {
          "page": {
            "resourceType": "core/franklin/components/block/v1/block/item",
            "template": {
              "name": "Card",
              "model": "card"
            }
          }
        }
      }
    }
  ],
  "models": [
    {
      "id": "card",
      "fields": [
        {
          "component": "reference",
          "valueType": "string",
          "name": "image",
          "label": "Image",
          "multi": false
        },
        {
          "component": "richtext",
          "name": "text",
          "value": "",
          "label": "Text",
          "valueType": "string"
        }
      ]
    }
  ],
  "filters": [
    {
      "id": "cards",
      "components": ["card"]
    }
  ]
}
```

**Key patterns**:
- Container (Cards) has `filter` in template, NO `model`
- Item (Card) has `model` in template, uses `block/item` resourceType
- Filter restricts container to only accept card items
- Both container and item need separate definition entries

---

## Block with Variants: Teaser

A complex block with style variants via multiselect, multiple fields with defaults.

### Key component-definition.json entry
```json
{
  "title": "Teaser",
  "id": "teaser",
  "plugins": {
    "xwalk": {
      "page": {
        "resourceType": "core/franklin/components/block/v1/block",
        "template": {
          "name": "Teaser",
          "model": "teaser",
          "title": "Teaser Title",
          "titleType": "h3",
          "longDescr": "",
          "classes": ["light", "left"]
        }
      }
    }
  }
}
```

### Key model fields (abbreviated)
```json
{
  "id": "teaser",
  "fields": [
    {
      "component": "reference",
      "name": "fileReference",
      "label": "Image",
      "multi": false
    },
    {
      "component": "multiselect",
      "name": "classes",
      "label": "Style",
      "required": true,
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
    },
    {
      "component": "select",
      "name": "titleType",
      "value": "h3",
      "label": "Heading Type",
      "options": [
        { "name": "H1", "value": "h1" },
        { "name": "H2", "value": "h2" },
        { "name": "H3", "value": "h3" }
      ]
    }
  ]
}
```

**Key patterns**:
- `classes` field with grouped multiselect → CSS classes on the block div
- Template pre-populates default values (`classes: ["light", "left"]`)
- `titleType` suffix → heading level in rendered HTML

---

## Key-Value Block: Product Details

Renders as a 2-column key-value table rather than a grid.

### component-definition.json entry
```json
{
  "title": "Product Details",
  "id": "product-details",
  "plugins": {
    "xwalk": {
      "page": {
        "resourceType": "core/franklin/components/block/v1/block",
        "template": {
          "name": "Product Details",
          "model": "product-details",
          "key-value": true
        }
      }
    }
  }
}
```

**Key pattern**: `"key-value": true` in the template changes how the block renders in the pipeline (2-column format: property name | value).

---

## Block with Content Fragment: Article

Uses a Content Fragment reference.

### component-models.json entry
```json
{
  "id": "article",
  "fields": [
    {
      "component": "aem-content-fragment",
      "name": "articlepath",
      "value": "",
      "label": "Article Content Fragment path",
      "valueType": "string"
    },
    {
      "component": "text-input",
      "name": "variation",
      "value": "",
      "label": "Variation",
      "valueType": "string"
    }
  ]
}
```

**Key pattern**: `aem-content-fragment` component for selecting Content Fragments from the DAM.

---

## Section Configuration

Sections use the same model/filter system but with a different resource type.

### component-definition.json
```json
{
  "title": "Section",
  "id": "section",
  "plugins": {
    "xwalk": {
      "page": {
        "resourceType": "core/franklin/components/section/v1/section",
        "template": {
          "model": "section"
        }
      }
    }
  }
}
```

### component-models.json
```json
{
  "id": "section",
  "fields": [
    {
      "component": "text",
      "name": "name",
      "label": "Section Name",
      "description": "The label shown for this section in the Content Tree"
    },
    {
      "component": "multiselect",
      "name": "style",
      "label": "Style",
      "options": [
        { "name": "Highlight", "value": "highlight" },
        { "name": "Background Color", "value": "background" }
      ]
    }
  ]
}
```

### component-filters.json
```json
{
  "id": "section",
  "components": [
    "text", "image", "button", "title", "hero", "article", "cards",
    "columns", "teaser", "fragment", "offer", "storelocator", "blog-list",
    "product-recommendations", "product-teaser", "product-list-page",
    "product-details", "video", "form", "embed-adaptive-form",
    "reward", "reward-sticky", "quiz", "embed"
  ]
}
```

The `section` filter is the main gatekeeper: any block that should be available to authors **must** be listed here.
