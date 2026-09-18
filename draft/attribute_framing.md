**Title**: Attribute Framing by ADC - v1.1

**Community Grouping**: community/adc/extension/v1.1

**Authors**: Carly Huitema, Paul Knowles, Steven Mugisha Mizero

**Date released**:

This overlay follows official OCA Package requirements documented at [https://github.com/agrifooddatacanada/OCA_package_standard](https://github.com/agrifooddatacanada/OCA_package_standard)

**Description**: Framing objects to ontologies and controlled vocabularies is critical for data interoperability and harmonization. This extension adds semantic context by linking schema objects to standardized external concepts. For example, a schema may use uM for units, while UCUM standardizes it as umol/L, enabling consistent interpretation across communities.

In OCA, three types of information are contextually framed:

1. **Attributes** where the schema attribute is framed to a term drawn from another concept such as an ontology or controlled vocabulary.
2. **Units** where a schema unit is framed to a unit drawn from another concept.
3. **Entry codes** where a schema entry code term is framed to a term drawn from another concept.

This overlay covers **attribute framing**.

**Canonicalization Rules**:

The framing overlays begin with the canonical ordering of [extension overlays](https://github.com/agrifooddatacanada/OCA_package_standard/tree/fix/key_values_requirements?tab=readme-ov-file#oca-package-syntax-requirements).

1. `d` (digest of the overlay)
2. `capture_base` (capture base SAID the overlay is specific to)
3. `type` (`community/overlays/adc/attribute_framing/1.1`)
4. `framing_metadata`, then `attributes`, each lexicographically ordered.

**Example**:

**Capture base:** showing the schema attributes to be framed:

```
{
  "type": "spec/capture_base/1.0",
  "digest": "Etszl9LgLUjllI950rd2lO6rF5-BP_jGzXGBPkFZCZFA",
  "classification": "RDF106",
  "attributes": {
    "Albumin_concentration": "Numeric",
    "Glucose_concentration": "Numeric",
    "Sample _name": "Text",
    "Sample_type": "Text"
  },
  "flagged_attributes": []
}
```

#### Framing Overlays and Relationships

To describe the framing relationship between OCA schema objects and external concepts, the [Simple Standard for Sharing Ontological Mappings](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC9216545/) (SSSOM) is used.

Only the four required SSSOM metadata elements are utilized:

| OCA                       | SSSOM                  |
| ------------------------- | ---------------------- |
| attribute/unit/entry_code | subject_id             |
| term_id                   | object_id              |
| predicate_id              | predicate_id           |
| framing_justification     | matching_justification |

As mentioned above, there are three types of information in OCA being framed, thus three types of framing overlays: Attribute framing overlay, Unit framing overlay, and Entry code framing overlay.

Each framing overlay type can have more than one overlay of the same variant. Each variant is of a single primary external vocabulary being used to frame the OCA schema objects.

After `d`, `capture_base`, and `type`, `framing_metadata` MUST follow.

```
// the structure of framing metadata

"framing_metadata": {
  "prefix": "dcat", // Short prefix for the primary vocabulary (reusable in term references)
  "label": "Data Catalog Vocabulary", // Label of the primary vocabulary
  "location": "http://www.w3.org/ns/dcat#", // Base URI / location of the primary vocabulary
  "version": "3", // Version of the primary vocabulary
  "imports": { // Supporting vocabularies imported by the primary vocabulary
    "dcterms": {
      "label": "DCMI Metadata Terms",
      "location": "http://purl.org/dc/terms/",
      "version": "1.1"
    },
    "foaf": {
      "label": "Friend of a Friend",
      "location": "http://xmlns.com/foaf/0.1/",
      "version": "0.1"
    }
  }
},
```

`framing_metadata` declares a **primary vocabulary** (`prefix`, `label`, `location`, `version`) and optional supporting vocabularies under `imports`. This establishes a clear hierarchy—for example, DCAT is what is being framed, while `dcterms` and `foaf` are imported to support it—while providing reusable prefixes for term references. Each key in `imports` is the supporting vocabulary's prefix; each value MUST include `label`, `location`, and `version`.

**Attribute framing overlay example**

```

// canonicalization rules: d, capture_base, type, framing_metadata, attributes

"attribute_framing": [
  {
    "d": "EL1aIB8CxU9H6DTFtl6h4KK1Tj5jMhYRra2N42jVtaZL",
    "capture_base": "EJRQHb6p4iBOt1oB6jdeG6cQhU9gY9KuI7oyAtuky8fI",
    "type": "community/overlays/adc/attribute_framing/1.0",
    "framing_metadata": {
      "id": "DCAT",
      "label": "Data Catalog Vocabulary",
      "location": "https://www.w3.org/TR/vocab-dcat-3/",
      "version": "1.0",
      "imports": {
        "foaf": {
          "label": "Friend of a Friend",
          "location": "http://xmlns.com/foaf/0.1/",
          "version": "0.1"
        }
      }
    },
    "attributes": {
      "dcterms.title": {
        "description": "A name given to the resource.",
        "framing_justification": "semapv:MappingReview",
        "predicate_id": "skos:relatedMatch",
        "term_id": "title object"
      },
      "dcterms.type": {
        "description": "The nature or genre of the resource.",
        "framing_justification": "semapv:ManualMappingCuration",
        "predicate_id": "skos:broadMatch",
        "term_id": "type object"
      }
    }
  },
  {
    "d": "EBLcpWf4DV6Q2IWqsbcufmLlF7mUwiPgB5PxNG8nibEv",
    "capture_base": "EJRQHb6p4iBOt1oB6jdeG6cQhU9gY9KuI7oyAtuky8fI",
    "type": "community/overlays/adc/attribute_framing/1.0",
    "framing_metadata": {
      "id": "Localid",
      "label": "Local vocab",
      "location": "https://fas.org/career/ai-safety-pef/",
      "version": "1.1",
      "imports": {
        "dcterms": {
          "label": "DCMI Metadata Terms",
          "location": "http://purl.org/dc/terms/",
          "version": "1.1"
        }
      }
    },
    "attributes": {
      "dcterms.title": {
        "description": "A name given to teh resource",
        "framing_justification": "semapv:MappingReview",
        "predicate_id": "skos:relatedMatch",
        "term_id": "title object"
      },
      "dcterms.type": {
        "description": "The nature or genre of the resource",
        "framing_justification": "semapv:ManualMappingCuration",
        "predicate_id": "skos:broadMatch",
        "term_id": "type object"
      }
    }
  }
]
```

Each keyed object under `attributes` MAY include:

| Property                | Required | Description                                                                 |
| ----------------------- | -------- | --------------------------------------------------------------------------- |
| description             | no       | Human-readable description of the framed attribute / mapped concept         |
| framing_justification   | yes      | SSSOM matching justification (`semapv:` term)                               |
| predicate_id            | yes      | SKOS mapping predicate describing the relationship                          |
| term_id                 | yes      | Object term from the primary or imported vocabulary                         |

### Rules summary for attribute framing overlays

- For each framing overlay there must be a `framing_metadata.prefix` identifying the primary vocabulary.
- Within each overlay framing type (attribute, unit or entry_code) each `prefix` must be unique.
- `framing_metadata.imports` MAY declare supporting vocabularies; each import key is a prefix and MUST include `label`, `location`, and `version`.
- Not every term must be framed
- For each attribute framing there can be only one `skos:exactMatch` per term.
- Attribute framing entries MAY include an optional `description`

**Predicate_id**

The `predicate_id` MUST be a `skos` term for describing the relationship between the `subject_id` and `object_id`.

| Skos term         | Description                                                                                                                                                                                                                                                                                                                               |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| skos:closeMatch   | closeMatch is used to link two concepts that are sufficiently similar that they can be used interchangeably in some information retrieval applications. In order to avoid the possibility of "compound errors" when combining mappings across more than two concept schemes, skos:closeMatch is not declared to be a transitive property. |
| skos:exactMatch   | exactMatch is used to link two concepts, indicating a high degree of confidence that the concepts can be used interchangeably across a wide range of information retrieval applications. skos:exactMatch is a transitive property, and is a sub-property of skos:closeMatch.                                                              |
| skos:broadMatch   | <A> skos:broadMatch <B> where B is broader than A. broadMatch is used to state an associative mapping link between two concepts.                                                                                                                                                                                                          |
| skos:narrowMatch  | <A> skos:narrowMatch <B> where B is narrower than A. skos:narrowMatch is owl:inverseOf the property skos:broadMatch.                                                                                                                                                                                                                      |
| skos:relatedMatch | relatedMatch is used to state an associative mapping link between two concepts.                                                                                                                                                                                                                                                           |

Source: [SKOS mapping vocabulary](https://www.w3.org/TR/skos-reference/#mapping)

**Framing_justification**

The `framing_justification` MUST be a `semapv` term for describing the justification for the relationship between the `subject_id` and `object_id`.

|Semapv term|Description|
|---|---|
|semapv:MappingReview|A process that is concerned with determining if a mapping candidate (otherwise determined) is reasonable/correct.|
|semapv:ManualMappingCuration|A matching process that is performed by a human agent and is based on human judgment and domain knowledge.|
|semapv:LogicalReasoning|A matching process based on the inferences made by a logical reasoner.|
|semapv:LexicalMatching|A matching process based on a lexical comparison between one or more syntactic features of the subject with one or more syntactic features of the object.|
|semapv:CompositeMatching|A matching process based on multiple, possibly intertwined, matching approaches.|
|semapv:UnspecifiedMatching||A matching process based on an unspecified comparison.|
|semapv:SemanticSimilarityThresholdMatching||A matching process based on a minimum threshold of a score from a comparison based on a semantic similarity algorithm.|
|semapv:LexicalSimilarityThresholdMatching|A lexical matching process based on a minimum threshold of a score from a comparison based on a lexical similarity algorithm.|
|semapv:MappingChaining|A matching process based on the traversing of multiple mappings.|

Source: SEMAPV: [A Vocabulary for Semantic Mappings](https://github.com/mapping-commons/semantic-mapping-vocabulary) and [use in SSSOM](https://mapping-commons.github.io/sssom/mapping_justification/)

## Normative references

- [OCA specification v1.0.1](http://oca.colossi.network/specification/)
- [3.2.3 Sorting of Object Properties](https://www.rfc-editor.org/rfc/rfc8785#section-3.2.3)
- [CESR Specification](https://weboftrust.github.io/ietf-cesr/draft-ssmith-cesr.html) for SAID calculations
- [OCA Package Standard](https://github.com/agrifooddatacanada/OCA_package_standard)
