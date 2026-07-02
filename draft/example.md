**Title**: Example by ADC - v1.1 - DRAFT

**Community Grouping**: community/adc/extension/vXX

**Authors**: Carly Huitema, Paul Knowles, Ali Asjad

**Date released**: 

This overlay follows official OCA Package requirements documented at [https://github.com/agrifooddatacanada/OCA_package_standard](https://github.com/agrifooddatacanada/OCA_package_standard)

**Description**:

This overlay adds a representative example value for each attribute in a schema, expressed in one or more languages.

Example values serve several purposes:
1. **Schema comprehension** — Communicates what a schema represents by showing concrete, realistic data for each attribute, helping readers understand the schema without needing domain expertise.
2. **Data entry guidance** — Displayed alongside form fields to show users the expected format, units, or style of input for each attribute.
3. **Pipeline testing** — Provides a ready-made, valid data record that can be used to verify data ingestion, transformation, and validation pipelines against the schema.
4. **Catalogue discoverability** — Appears in dataset catalogues so that searchers can quickly understand the type and shape of data contained in a dataset described by the schema.

Each Example overlay is language-specific, represented as a separate instance with its own SAID. All language versions reference the same `capture_base`. Because the overlay is language-specific, it is stored as an array (one entry per language), following the same pattern as the `entry`, `label`, and `meta` overlays.

**Canonicalization Rules**:

The example overlay begins with the canonical ordering of OCA overlays.
1) d (digest of the overlay)
2) capture_base (capture base SAID the overlay is specific to)
3) type (community/overlays/adc/example/1.1)

Then the `examples` key appears, containing an object whose attribute-name keys are lexicographically ordered. The `language` key follows. For SAID generation, JSON MUST be serialized in a fully compact form with no extraneous whitespace.

**Example**:

The following shows a canonicalized overlay array for a two-language schema. SAID values are illustrative.

```
"example": [
  {
    "d": "EBzXGBPkFZCZFAtszl9LgLUjllI950rd2lO6rF5BP_jG",
    "capture_base": "Etszl9LgLUjllI950rd2lO6rF5-BP_jGzXGBPkFZCZFA",
    "type": "community/overlays/adc/example/1.1",
    "examples": {
      "Albumin_concentration": "52.69",
      "Glucose_concentration": "8.7",
      "Sample_name": "Carlys_sample",
      "Sample_type": "BLD003"
    },
    "language": "eng"
  },
  {
    "d": "EKpDjRhP_rcN_JliDmOF1DCNZ00llwS2XuNBrruXEJjK",
    "capture_base": "Etszl9LgLUjllI950rd2lO6rF5-BP_jGzXGBPkFZCZFA",
    "type": "community/overlays/adc/example/1.1",
    "examples": {
      "Albumin_concentration": "52.69",
      "Glucose_concentration": "8.7",
      "Sample_name": "عينة_كارلي",
      "Sample_type": "BLD003"
    },
    "language": "ara"
  }
]
```


**Rules summary**:

- The example overlay MUST include: `d`, `capture_base`, `type`, `examples`, and `language`.
- `language` MUST be a valid ISO 639-2 three-letter code (e.g., `"eng"`, `"fra"`, `"ara"`).
- Each attribute referenced in `examples` MUST exist in the `capture_base` of the source schema.
- Not all attributes are required to have an example value; attributes MAY be omitted from `examples`.
- All example values MUST be strings.
- Example values SHOULD be realistic and representative of actual data expected for the attribute.
- Example values MUST be consistent with the datatype and any format constraints defined in the schema (e.g., numeric strings for Numeric attributes, ISO 8601 strings for DateTime attributes).
- For attributes with entry codes, example values SHOULD use a valid entry code defined in the corresponding Entry overlay.
- Multiple language versions of the overlay MUST share the same `capture_base` but each has its own `d` value.
- Attribute keys within `examples` MUST be lexicographically ordered for canonicalization.


**Test case**: 

```
Intentionally left empty
```

## Normative references
- [OCA specification v1.0.1](http://oca.colossi.network/specification/) 
- [3.2.3 Sorting of Object Properties](https://www.rfc-editor.org/rfc/rfc8785#section-3.2.3)
- [CESR Specification](https://weboftrust.github.io/ietf-cesr/draft-ssmith-cesr.html) for SAID calculations
- [OCA Package Standard](https://github.com/agrifooddatacanada/OCA_package_standard)
