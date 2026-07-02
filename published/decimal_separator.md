**Title**: Decimal Separator by ADC - v1.1

**Community Grouping**: community/adc/extension/vXX

**Authors**: Ali Asjad

**Date released**: 25-05-2026

This overlay follows official OCA Package requirements documented at [https://github.com/agrifooddatacanada/OCA_package_standard](https://github.com/agrifooddatacanada/OCA_package_standard)

**Description**:

This overlay specifies which decimal separator is used for numeric values when they are represented as text for data entry, display, exchange formats (e.g., CSV), and localization. It allows a community or publisher to set a default decimal separator for a schema and optionally override that separator for specific attributes.

This overlay does not change the semantic numeric values of attributes; it only defines formatting and parsing expectations for decimal representation.

Keys defined by this overlay:
- `decimal_separator` (string): The decimal separator for the entire schema. Allowed values are "." or ",".

**Canonicalization Rules**:

The decimal separator overlay begins with the canonical ordering of OCA overlays.
1) d (digest of the overlay)
2) capture_base (capture base SAID the overlay is specific to)
3) type (community/overlays/adc/decimal_separator/1.1)

Next, the overlay contains `decimal_separator`.

**Example**:

The following is an example of a canonicalized overlay object. The `d` value is illustrative only.

```
"decimal_separator":{
  "d": "EXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  "capture_base": "Etszl9LgLUjllI950rd2lO6rF5-BP_jGzXGBPkFZCZFA",
  "type": "community/overlays/adc/decimal_separator/1.1",
  "decimal_separator": ","
}
```

**Rules summary**:

- `decimal_separator` MUST be present and MUST be either "." or ",".
- This overlay MUST NOT change the underlying numeric semantics; it only informs display, entry, and parsing behavior.

**Test case**:

```
{
  "d": "EMePm-KYyHs2Rprjvx6xXblgkZKqJDoukUjmjEoptPWu",
  "type": "oca_package/1.0",
  "oca_bundle": {
    "bundle": {
      "v": "OCAS11JSON000214_",
      "d": "EGxBspm17GfMyQwFahApmBOZoEarVW6GxpLx28cSfqLq",
      "capture_base": {
        "d": "EJi6VGWxBvPSnUCrMib6tO9ixmd8DYo34t-9wHt0dW05",
        "type": "spec/capture_base/1.1",
        "attributes": {
          "DecimalValue": "Numeric"
        },
        "classification": "RDF106",
        "flagged_attributes": []
      },
      "overlays": {
        "meta": [
          {
            "d": "EHF74vaAa6OYXNQd81EHo8GrHYtIcZSxWAlktAAxO7tf",
            "capture_base": "EJi6VGWxBvPSnUCrMib6tO9ixmd8DYo34t-9wHt0dW05",
            "type": "spec/overlays/meta/1.1",
            "language": "eng",
            "description": "Schema to test the decimal separator",
            "name": "Decimal Separator "
          }
        ]
      }
    },
    "dependencies": []
  },
  "extensions": {
    "adc": {
      "EJi6VGWxBvPSnUCrMib6tO9ixmd8DYo34t-9wHt0dW05": {
        "d": "ELP5ESyjJyawHNjcrD-2voXOkM8KKpUiLYW_oEopNU6z",
        "type": "community/adc/extension/1.0",
        "overlays": {
          "decimal_separator": {
            "d": "EFNfrJvE_BuXVsFqjragbTXwHNq6-IZJJlPKvVk0Ip61",
            "capture_base": "EJi6VGWxBvPSnUCrMib6tO9ixmd8DYo34t-9wHt0dW05",
            "type": "community/overlays/adc/decimal_separator/1.0",
            "delimiter": ","
          },
          "ordering": {
            "d": "EDBNPed3y75T33yorrCU4vgGiocuMX-H9n8MbgAEb7X2",
            "capture_base": "EJi6VGWxBvPSnUCrMib6tO9ixmd8DYo34t-9wHt0dW05",
            "type": "community/overlays/adc/ordering/1.0",
            "attribute_ordering": [
              "DecimalValue"
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
