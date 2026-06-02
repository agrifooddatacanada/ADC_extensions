**Title**: Sensitive by ADC - v1.1

**Community Grouping**: community/adc/extension/vXX

**Authors**: Steven Mugisha Mizero, Carly Huitema

**Date released**: 15-05-2025

This overlay follows official OCA Package requirements documented at [https://github.com/agrifooddatacanada/OCA_package_standard](https://github.com/agrifooddatacanada/OCA_package_standard)

**Description**:

This overlay corrects for the missing sensitive (aka flagged) attributes that were previously in the capture base. This information is now in a new sensitive overlay.

**Canonicalization Rules**:

The ordering overlay begins with the canonical ordering of OCA overlays.
1) d (digest of the overlay)
2) capture_base (capture base SAID the overlay is specific to)
3) type (community/overlays/adc/sensitive/1.1)

Then there is the sensitive_attributes object with an array of sensitive attributes in lexicographical order.

**Example**: 

```
"sensitive":{
  "d": "EAgeUSa7JMG9oxJHUKTEBz4chJLWE5ayTq2rvBHo_P5R",
  "type": "community/overlays/adc/sensitive/1.1",
  "sensitive_attributes": [
    "farm",
    "latitude",
    "longitude",
    "samplers"
  ]
}
```


**Rules summary**: 
- Only attributes which are listed in the capture base can appear in the sensitive overlay.
- Attributes can appear only once in the sensitive_attributes array.

**Test case**: 

```
{"d":"ELdKVgQaneGEDU36iQHA1LWPilIT9FFq400cID1auysS","type":"oca_package/1.0","oca_bundle":{"v":"OCAA11JSON000236_","bundle":{"v":"OCAS11JSON000219_","d":"EKGImlIzhvYeYNFbMeDI5j3Unx54AB7dhr7CJtqLqEzE","capture_base":{"d":"EFAwR3ChI4QQ4_mH8MWRBvHCrNau1Ng7LpnRm5ITzfuV","type":"spec/capture_base/1.1","attributes":{"v1":"Text","v2":"Numeric","v3":"Text"},"classification":"","flagged_attributes":[]},"overlays":{"meta":[{"d":"EH1YjII020FIRSlxHJmQZRq_PfA4yVpKIgW-CHFcHYop","capture_base":"EFAwR3ChI4QQ4_mH8MWRBvHCrNau1Ng7LpnRm5ITzfuV","type":"spec/overlays/meta/1.1","language":"eng","description":"test schema demonstrating sensitive attributes.","name":"test"}]}},"dependencies":[]},"extensions":{"adc":{"EFAwR3ChI4QQ4_mH8MWRBvHCrNau1Ng7LpnRm5ITzfuV":{"d":"EI5Z0U5OpuyAb1XlDXwI2N_VMI9Ay4oxwG6KJ5sMjwYE","type":"community/adc/extension/1.0","overlays":{"ordering":{"d":"EOMjYokuRJ20OTfJhrcm41G5rCKfUgliXHQPjfTBXU5K","type":"community/overlays/adc/ordering/1.1","attribute_ordering":["v1","v2","v3"],"entry_code_ordering":{}},"sensitive":{"d":"ELjrEWt2iAX7Ky83QbtXG-OPGZv-QhgsMBetDMDptgOa","type":"community/overlays/adc/sensitive/1.1","sensitive_attributes":["v1","v3"]}}}}}}
```

## Normative references
- [OCA specification v1.0.1](http://oca.colossi.network/specification/) 
- [3.2.3 Sorting of Object Properties](https://www.rfc-editor.org/rfc/rfc8785#section-3.2.3)
- [CESR Specification](https://weboftrust.github.io/ietf-cesr/draft-ssmith-cesr.html) for SAID calculations
- [OCA Package Standard](https://github.com/agrifooddatacanada/OCA_package_standard)
