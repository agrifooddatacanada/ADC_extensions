**Title**: Form by ADC - v1.0

**Community Grouping**: community/adc/extension/vXX

**Authors**: Carly Huitema, Setayesh Sanavi

**Date released**: 25-05-2026

This overlay follows official OCA Package requirements documented at [https://github.com/agrifooddatacanada/OCA_package_standard](https://github.com/agrifooddatacanada/OCA_package_standard)

**Description**:

The Form overlay extends OCA schemas with form structure and presentation metadata. It allows schema authors to define multi-page layouts with organized sections, questions, and interactive elements, while supporting multiple languages.

Each Form overlay is language-specific, represented as a separate instance with its own SAID. All language versions reference the same `capture_base` and share identical page UUIDs, ensuring structural consistency across translations. Because the overlay is language-specific, it is stored as an array (one entry per language), following the same pattern as the `entry`, `label`, and `meta` overlays.

The Form overlay supports:
1. **Page Organization** — Define multiple pages with a clear, ordered layout.
2. **Section Grouping** — Organize questions within pages using named sections identified by UUIDs.
3. **Question Attributes** — Reference schema attributes as individual form questions via `attribute_order`.
4. **Multi-language Support** — Provide labels, descriptions, and placeholders in multiple languages using ISO 639-2 three-letter codes.
5. **Interactive Elements** — Configure input types and placeholders for each question via `interaction`.
6. **Navigation Structure** — Specify page order and sidebar navigation labels.

**Canonicalization Rules**:

The form overlay begins with the canonical ordering of OCA overlays.
1) d (digest of the overlay)
2) capture_base (capture base SAID the overlay is specific to)
3) type (community/overlays/adc/form/1.0)

The remaining top-level keys MUST follow [3.2.3 Sorting of Object Properties](https://www.rfc-editor.org/rfc/rfc8785#section-3.2.3), giving the order:
4) description
5) interaction
6) language
7) page_labels
8) page_order
9) pages
10) sidebar_label
11) title

Within `pages`, each page object's properties are also lexicographically ordered (`attribute_order` before `named_section`). Within `interaction`, each `arguments` object's attribute keys are lexicographically ordered. Arrays follow insertion/display order. For SAID generation, JSON MUST be serialized in a fully compact form with no extraneous whitespace.

**Example**:

The following is a canonicalized overlay object for a single-page, single-language form. SAID values are illustrative.

```
"form": [
  {
    "d": "EKRhP_rcN_JliDmOF1DCNZ00llwS2XuNBrruXEJjKpDj",
    "capture_base": "ECYZbkMeKLwlaMCW1kiG9OkWy5B4pExR4sbENH9G9qQF",
    "type": "community/overlays/adc/form/1.0",
    "description": {
      "e83f3dc1-5cb6-4ce0-96d6-8ead0acf6c93": "Page 1"
    },
    "interaction": [
      {
        "arguments": {
          "SomeVariable": {
            "placeholder": "Any text up to 50 characters long",
            "type": "Text"
          }
        }
      }
    ],
    "language": "eng",
    "page_labels": {
      "e83f3dc1-5cb6-4ce0-96d6-8ead0acf6c93": "Page 1"
    },
    "page_order": ["e83f3dc1-5cb6-4ce0-96d6-8ead0acf6c93"],
    "pages": [
      {
        "attribute_order": ["SomeVariable"],
        "named_section": "e83f3dc1-5cb6-4ce0-96d6-8ead0acf6c93"
      }
    ],
    "sidebar_label": {
      "e83f3dc1-5cb6-4ce0-96d6-8ead0acf6c93": "Page 1"
    },
    "title": "File Delimiter Test"
  }
]
```

For a multi-language schema, a second overlay object with the same page UUIDs but translated strings and a different `d` value would be added to the array.

**Rules summary**:

- The form overlay MUST include: `d`, `capture_base`, `type`, `language`, `pages`, `page_order`, `page_labels`, `sidebar_label`, `description`, `interaction`, and `title`.
- `language` MUST be a valid ISO 639-2 three-letter code (e.g., `"eng"`, `"fra"`).
- Each entry in `pages` MUST have a `named_section` (UUID string) and an `attribute_order` array.
- All page UUIDs referenced in `page_labels`, `sidebar_label`, `description`, and `page_order` MUST correspond to `named_section` values defined in `pages`.
- Each attribute referenced in `attribute_order` or `interaction` MUST exist in the `capture_base` of the source schema.
- Duplicate page UUIDs are not permitted within a single overlay instance.
- Each language version of the form MUST share the same page UUIDs and attribute references but has its own `d` value.
- `interaction` is an array of objects; each object has an `arguments` key mapping attribute names to their input configuration (`type` and optionally `placeholder`).

**Test case**:

```
Intentionally left empty
```

## Normative references
- [OCA specification v1.0.1](http://oca.colossi.network/specification/)
- [3.2.3 Sorting of Object Properties](https://www.rfc-editor.org/rfc/rfc8785#section-3.2.3)
- [CESR Specification](https://weboftrust.github.io/ietf-cesr/draft-ssmith-cesr.html) for SAID calculations
- [OCA Package Standard](https://github.com/agrifooddatacanada/OCA_package_standard)
