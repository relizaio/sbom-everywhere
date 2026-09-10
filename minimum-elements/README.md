# CISA 2026 Minimum Elements Mapped to SBOM Formats

Working with the SPDX and CycloneDX communities to identify mappings to use with the various versions of the formats.

Source: [2026 Minimum Elements for a Software Bill of Materials (SBOM)](https://www.cisa.gov/sites/default/files/2026-07/2026_cisa_sbom_minimum_elements_508c.pdf), CISA, publication date July 29, 2026 (TLP:CLEAR).

This page is the Markdown version of the working group's slide deck, kept in this repository so that it is easy to find, link, and mine. Corrections and additions are welcome as pull requests.

## CISA 2026 criteria

Notes from the [report](https://www.cisa.gov/sites/default/files/2026-07/2026_cisa_sbom_minimum_elements_508c.pdf):

- Each Data Fields element need not correlate directly with a particular data field in an SBOM data format, and an implemented data field may satisfy one or more of the minimum elements.
- **SBOM Metadata** elements capture information about the SBOM document itself. The data supports SBOM management; SBOM analysis tools leverage these data fields to improve usability of the SBOM data.
- **Component Data** elements capture information about the target component (the component for which the SBOM is generated) and all enumerated subcomponents. Analysis of this data provides information about the components and their relationships and enables better informed risk management decisions regarding the target component.

## Component data field definitions

| Element | Change from 2021 | Definition |
|---|---|---|
| Component Producer | Major Update | The name of an entity that creates, defines, and identifies components. |
| Component Dependency Relationship | Minor Update | The relationship between two components, where one component is necessary for the operation of the other. |
| Component Hash Value | New | The output generated from applying a cryptographic hash algorithm to an executable component artifact. |
| Component Hash Algorithm | New | The cryptographic algorithm used to compute the Component Hash Value of the software component. |
| Component Identifiers | Major Update | Identifiers used to identify a component or serve as a look-up key for relevant databases. |
| Component License | New | The identifier(s) for the license(s) under which the software component is available. |
| Component Name | Minor Update | The name assigned by the component producer to a software component. |
| Component Version | Major Update | Identifier used by the component producer to specify a change in a software component from a previously identified version or to indicate that it is the first version. |

## Component data mapping

"Same" means the same as the column to its left. Bracketed letters refer to the [additional notes](#additional-notes).

| Minimum Element | ISO 5962:2021 (SPDX 2.2) | SPDX 2.3 JSON | ISO 5962 DIS (SPDX 3.0) | SPDX 3.1 candidate | CycloneDX 1.5 | ECMA-424v1 (CycloneDX 1.6) | ECMA-424v2 (CycloneDX 1.7) | CycloneDX 2.0 candidate |
|---|---|---|---|---|---|---|---|---|
| Component Producer | `packages[].originator` | `packages[].originator` | `Package.originatedBy` | `Package.originatedBy` | `components[].author`; property if needed | `manufacturer.name` or `authors[].name` | Same | `components[].parties[]` [a], [b] |
| Dependency Relationship | `relationships[]`: `spdxElementId` / `DEPENDS_ON` / `relatedSpdxElement` | Same | `Relationship: dependsOn` | Same | `dependencies[].ref` / `dependsOn[]` | Same | Same | Same |
| Component Hash Value | `packages[].checksums[].checksumValue` | Same | `Hash.hashValue` | Same | `components[].hashes[].content` | Same | Same | Same [b] |
| Hash Algorithm | `packages[].checksums[].algorithm` | Same | `Hash.algorithm` | Same | `components[].hashes[].alg` | Same | Same | Same [b] |
| Component Identifiers | `packages[].externalRefs[]`; `OTHER` for SWID/gitoid | `packages[].externalRefs[]` incl. SWID/gitoid | `packageURL`; `externalIdentifier` (cpe22, cpe23, gitoid, packageUrl, swhid, swid); `contentIdentifier` (gitoid, swhid) | Same | `cpe`, `purl`, `swid`, `omniborId`, `swhid` | Same | Same | `components[].identifiers[]`; for details see [b], [c] |
| Component License | `packages[].licenseDeclared`; `packages[].licenseConcluded` | Same | `Relationship: hasDeclaredLicense`; `Relationship: hasConcludedLicense` | Same | `components[].licenses[]` | `licenses[]`; `acknowledgement=declared` | Same | Same with more options to express [b], [d] |
| Component Name | `packages[].name` | Same | `Package.name` | Same | `components[].name` | Same | Same | Same [b] |
| Component Version | `packages[].versionInfo` | Same | `Package.packageVersion` | `Package.version` | `components[].version` | Same | Same | Same [b] |

## Metadata field definitions

| Element | Change from 2021 | Definition |
|---|---|---|
| SBOM Author | Major Update | The name of the entity that creates the SBOM data for the target component. |
| SBOM Author Signature | New | A digital signature attributable to the SBOM author. |
| SBOM Data Format Name | New | The name of the data format used to represent the SBOM data. |
| SBOM Data Format Version | New | Identifier designated by the SBOM data format to specify the version of the data format. |
| SBOM Generation Context | New | The relative software lifecycle phase and data available at the time the SBOM author generated the SBOM. |
| SBOM Timestamp | Minor Update | Record of the date and time of the most recent update to the SBOM data. |
| SBOM Tool Name | New | The name of the tool used by the SBOM author to generate or amend the SBOM. |
| SBOM Tool Version | New | Identifier for the version of the tool identified in the SBOM Tool Name element. |
| SBOM Version | New | Identifier designated by the SBOM author to specify a change in the SBOM document from a previously identified version or to indicate that it is the first version. |

## Metadata mappings

"Same" means the same as the column to its left. Bracketed letters refer to the [additional notes](#additional-notes).

| Minimum Element | ISO 5962:2021 (SPDX 2.2) | SPDX 2.3 JSON | ISO 5962 DIS (SPDX 3.0 & OMG SPDX 3.0) | SPDX 3.1 candidate | CycloneDX 1.5 | ECMA-424v1 (CycloneDX 1.6) | ECMA-424v2 (CycloneDX 1.7) | CycloneDX 2.0 candidate |
|---|---|---|---|---|---|---|---|---|
| SBOM Author | `creationInfo.creators[]` Person/Organization | Same | `creationInfo.createdBy` → `Agent.name` | Same | `metadata.authors[].name`; property for org | `metadata.manufacturer.name` or `authors[].name` | Same | Same |
| Author Signature | External signed envelope | Same | `signature` (JSS) | Same | `signature` (JSF) | `signature` (JSF) | `signature` (JSF) | `signatures` (list of JSS signature objects) |
| Data Format Name | `spdxVersion = "SPDX-2.2"` (format name: SPDX) | `spdxVersion = "SPDX-2.3"` (format name: SPDX) | `@context` → `https://spdx.org/rdf/[version]/spdx-context.jsonld` | Same | `bomFormat` + media type | Same | Same | `specFormat = "CycloneDX"` + media type |
| Data Format Version | `spdxVersion = SPDX-2.2` | `spdxVersion = SPDX-2.3` | `creationInfo.specVersion` | Same | `specVersion = 1.5` | `specVersion = 1.6` | `specVersion = 1.7` | `specVersion = 2.0` |
| Generation Context | `creationInfo.comment` | Same | `software/Sbom.sbomType` | Same | `metadata.lifecycles[].phase` | Same | Same | Same |
| SBOM Timestamp | `creationInfo.created` | Same | `creationInfo.created` | Same | `metadata.timestamp` | Same | Same | Same |
| SBOM Tool Name | `creationInfo.creators[]` Tool entry | Same | `createdUsing` → `Tool.name` | Same | `metadata.tools.components[].name` | Same | Same | Same [b] |
| SBOM Tool Version | Parsed from `creators[]` Tool entry | Same | Tool → versioned Package representing executable [z] | `Tool.version` when exposed; otherwise Tool → versioned Package [z] | `metadata.tools.components[].version` | Same | Same | Same [b] |
| SBOM Version | `documentNamespace` [y] | Same | `SBOM.spdxId` and optionally Relationship to previous `SBOM.spdxId` | `SBOM.spdxId` and optionally Relationship to previous `SBOM.spdxId`; alternatively `Artifact.version` | `version` + `serialNumber` | Same | Same | Same |

## Additional notes

- **[a]** In CycloneDX 2.0 the party shape requires only `roles`, and exactly one identity block (`organization` / `person` / `system` / `persona`) is expected depending on what kind of party it is. A minimum example:

  ```json
  "parties": [
    {
      "roles": [ { "role": "author" } ],
      "person": { "name": "Jane Doe", "email": "jane@example.com" }
    }
  ]
  ```

- **[b]** In CycloneDX 2.0 components are not a root element; they can live under `inventories[].subject`, `inventories[].components[]`, and `definitions[].components[]`.
- **[c]** Shape of a single identifier element in CycloneDX 2.0: `{ "party": <bom-ref of asserting party>, "identities": [ { "scheme", "value" } ] }`, where `scheme` is an enum of: `purl`, `cpe`, `swid`, `swhid`, `omniborid`, `epc-rfid`, `giai`, `gln`, `gmn`, `gtin-8`, `gtin-12`, `gtin-13`, `gtin-14`, `mpn`, `part-number`, `model-number`, `sku`, `serial-number`, `asset-tag`, `udi-di`, `udi-pi`, `fcc-id`, `imei`, `mac-address`, `tei`.
- **[d]** Every 1.7-valid `licenses` value is 2.0-valid, but not vice versa. `oneOf` moved from the array level down to the item level. The array is now a free mix of license objects and expression objects, and multiple expressions are allowed. The 1.6 description "EITHER … OR (tuple of one …)" became "A list of SPDX licenses and/or named licenses and/or SPDX License Expression."
- **[y]** Versions of SBOMs can be indicated by a relationship of `descendentOf` between SBOM objects. SBOM versions are used for two purposes: (1) ensuring you know which exact SBOM version you are reading, and (2) knowing if the SBOM is a derivative of a previous version. For (1), the SPDX identifier for the SBOM can be used, since it must be globally unique and any modification of an SBOM requires a new globally unique ID. For (2), a relationship from the modified SBOM to the previous original version of the SBOM with relationship type `descendantOf` can be used.
- **[z]** For specific information about a Tool used to produce an SPDX document, a Package representing the tool has a relationship to the SBOM. The Package can have version information as well as other useful fields such as verification hash codes.

## References

1. [CISA, 2026 Minimum Elements for a Software Bill of Materials (SBOM)](https://www.cisa.gov/sites/default/files/2026-07/2026_cisa_sbom_minimum_elements_508c.pdf)
2. [RunSafe Security, SBOM Minimum Elements: CycloneDX and SPDX](https://runsafesecurity.com/blog/sbom-minimum-elements-cyclonedx-spdx/)
3. Allan Friedman's blogs (link to be added)
4. SPDX reference (link to be added)
5. CycloneDX reference (link to be added)

## Status

The CycloneDX 2.0 column reflects the 2.0 candidate as it stands in the CycloneDX 2.0 development branch and open pull requests; it will change until 2.0 is released.
