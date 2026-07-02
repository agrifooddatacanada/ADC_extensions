**Title**: File Delimiter by ADC - v1.1

**Community Grouping**: community/adc/extension/vXX

**Authors**: Ali Asjad

**Date released**: 25-05-2026

This overlay follows official OCA Package requirements documented at [https://github.com/agrifooddatacanada/OCA_package_standard](https://github.com/agrifooddatacanada/OCA_package_standard)

**Description**:

The File Delimiter overlay standardizes how delimited text files (commonly .csv) are produced and consumed for a schema bundle. It defines the delimiter character and related serialization parameters that vary by locale and toolchain. This addresses internationalization concerns where, for example, semicolons (;) or tabs (\t) are used instead of commas when the comma is a decimal separator.

This overlay works alongside other presentation/formatting overlays. For example, numeric rendering (e.g., the character used as a decimal separator) is governed by the `decimal_separator` overlay, while the character that separates fields in exported/imported files is governed here by `file_delimiter`.

Keys defined by this overlay:
- `delimiter` (string, REQUIRED): The single-character field delimiter used between values. Allowed examples include `","`, `";"`, `"\t"` (tab), and `"|"`. The value MUST represent exactly one Unicode code point in JSON string form.
- `data_start_row` (integer, REQUIRED): The 1-based row index at which data rows begin. MUST be a positive integer. For example, `3` indicates that the data begins on row 3 (e.g., two header rows).
- `quote_char` (string, REQUIRED): Character used to quote fields. Use `""` if no quoting is used.
- `escape_char` (string, REQUIRED): Character used to escape `quote_char` inside quoted fields. Use `""` if no escaping is used.
- `line_terminator` (string, REQUIRED): Line break sequence for rows. Allowed values are `"lf"` (Unix `\n`), `"crlf"` (Windows `\r\n`), or `""` (auto-detect).

This overlay does not change attribute semantics. It specifies file-level serialization behavior so CSV/TSV-like files can be correctly generated and parsed across locales.

**Canonicalization Rules**:

The file_delimiter overlay begins with the canonical ordering of OCA overlays.
1) d (digest of the overlay)
2) capture_base (capture base SAID the overlay is specific to)
3) type (community/overlays/adc/file_delimiter/1.1)

All other objects present follow the first three keys and MUST follow [3.2.3 Sorting of Object Properties](https://www.rfc-editor.org/rfc/rfc8785#section-3.2.3). Arrays, if present, are insertion ordered. For SAID generation, JSON MUST be serialized in a fully compact form with no extraneous whitespace.

**Example**:

The following is a canonicalized overlay object. The SAID values are illustrative.

```
"file_delimiter": {
  "d": "EOYw7L0iTZbmJvXRAxuAMajHZFq4PtAmSn8CxE671zmL",
  "capture_base": "ECYZbkMeKLwlaMCW1kiG9OkWy5B4pExR4sbENH9G9qQF",
  "type": "community/overlays/adc/file_delimiter/1.1",
  "data_start_row": 3,
  "delimiter": ";",
  "escape_char": "\\",
  "line_terminator": "lf",
  "quote_char": "\""
}
```

**Rules summary**:

- `delimiter` MUST be present and MUST represent exactly one Unicode code point as a JSON string (e.g., `","`, `";"`, `"|"`, or `"\t"` for tab).
- `data_start_row` MUST be a positive integer of value 1 or greater.
- `line_terminator` MUST be one of `"lf"`, `"crlf"`, or `""`.
- This overlay MUST NOT alter attribute semantics or numeric formatting; those concerns are addressed by other overlays (e.g., `decimal_separator`).

**Test case**:

```
{
  "d": "EB_Qwpx--_22rJVgvX3zbgeRS_LwsUKRy-Q6Mii0z_UM",
  "type": "oca_package/1.0",
  "oca_bundle": {
    "bundle": {
      "v": "OCAS11JSON00022c_",
      "d": "ELcFFNIxlsCBeoMVGOZ3z4_03FGlCz0yXOw3ry4tJ43R",
      "capture_base": {
        "d": "ECYZbkMeKLwlaMCW1kiG9OkWy5B4pExR4sbENH9G9qQF",
        "type": "spec/capture_base/1.1",
        "attributes": {
          "AnotherRandomVar": "Numeric",
          "SomeVariable": "Text"
        },
        "classification": "RDF401",
        "flagged_attributes": []
      },
      "overlays": {
        "meta": [
          {
            "d": "EEPz5yKSta1ZYMwZkHvj7sCSjbvuW1eOyRusttVkg6oM",
            "capture_base": "ECYZbkMeKLwlaMCW1kiG9OkWy5B4pExR4sbENH9G9qQF",
            "type": "spec/overlays/meta/1.1",
            "language": "eng",
            "description": "Schema to test the file delimiter",
            "name": "File Delimiter Test"
          }
        ]
      }
    },
    "dependencies": []
  },
  "extensions": {
    "adc": {
      "ECYZbkMeKLwlaMCW1kiG9OkWy5B4pExR4sbENH9G9qQF": {
        "d": "EPqd_tZxboaLI8zve-7yQSvNgVvsF4jEk_TGQzjb4oVP",
        "type": "community/adc/extension/1.0",
        "overlays": {
          "file_delimiter": {
            "d": "EOYw7L0iTZbmJvXRAxuAMajHZFq4PtAmSn8CxE671zmL",
            "capture_base": "ECYZbkMeKLwlaMCW1kiG9OkWy5B4pExR4sbENH9G9qQF",
            "type": "community/overlays/adc/file_delimiter/1.0",
            "delimiter": ";",
            "escape_char": "\\",
            "data_start_row": 3,
            "line_terminator": "lf",
            "quote_char": "\""
          },
          "ordering": {
            "d": "EJ8Wvk5rFkODvVraR8yiPp2RtCZXNGpNc8LPyj8VQIiG",
            "capture_base": "ECYZbkMeKLwlaMCW1kiG9OkWy5B4pExR4sbENH9G9qQF",
            "type": "community/overlays/adc/ordering/1.0",
            "attribute_ordering": [
              "SomeVariable",
              "AnotherRandomVar"
            ],
            "entry_code_ordering": {}
          }
        }
      }
    }
  }
}
```

## Normative references
- [OCA specification v1.0.1](http://oca.colossi.network/specification/)
- [3.2.3 Sorting of Object Properties](https://www.rfc-editor.org/rfc/rfc8785#section-3.2.3)
- [CESR Specification](https://weboftrust.github.io/ietf-cesr/draft-ssmith-cesr.html) for SAID calculations
- [OCA Package Standard](https://github.com/agrifooddatacanada/OCA_package_standard)
