---
name: schema-generator
description: Generates input, output, and dataset schemas for Apify Actors. Use when defining Actor interfaces.
allowed-tools: Read, Write, Glob
---

# Schema Generator

Generate proper JSON schemas for Apify Actors.

## Input Schema

Create `.actor/input_schema.json`:

```json
{
    "title": "Actor Input",
    "type": "object",
    "schemaVersion": 1,
    "properties": {
        "startUrls": {
            "title": "Start URLs",
            "type": "array",
            "description": "URLs to start scraping from",
            "editor": "requestListSources",
            "prefill": [{ "url": "https://example.com" }]
        },
        "maxRequestsPerCrawl": {
            "title": "Max Requests",
            "type": "integer",
            "description": "Maximum pages to scrape (0 = unlimited)",
            "default": 100,
            "minimum": 0
        },
        "proxyConfiguration": {
            "title": "Proxy Configuration",
            "type": "object",
            "editor": "proxy",
            "default": { "useApifyProxy": false }
        }
    },
    "required": ["startUrls"]
}
```

## Output Schema

Create `.actor/output_schema.json`:

```json
{
    "actorOutputSchemaVersion": 1,
    "title": "Actor Output",
    "properties": {
        "dataset": {
            "type": "string",
            "title": "Dataset",
            "template": "{{links.apiDefaultDatasetUrl}}/items"
        }
    }
}
```

## Dataset Schema

Create `.actor/dataset_schema.json` for Output tab UI:

```json
{
    "actorSpecification": 1,
    "fields": {},
    "views": {
        "overview": {
            "title": "Overview",
            "transformation": {
                "fields": ["url", "title", "price"]
            },
            "display": {
                "component": "table",
                "properties": {
                    "url": { "label": "URL", "format": "link" },
                    "title": { "label": "Title", "format": "text" },
                    "price": { "label": "Price", "format": "number" }
                }
            }
        }
    }
}
```

## Common Field Types

| Type | Editor | Use For |
|------|--------|---------|
| `array` | `requestListSources` | Start URLs |
| `object` | `proxy` | Proxy settings |
| `string` | `textfield` | Single line text |
| `string` | `textarea` | Multi-line text |
| `boolean` | `checkbox` | True/false options |
| `integer` | `number` | Numeric values |
| `string` | `select` | Dropdown with `enum` |
