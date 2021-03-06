\[%settitle Relationship between FHIR and Misc. HL7 Standards%\]
\[%file newnavbar%\]
\[%cmpheader misc%\]
Other HL7 Standards
-------------------

|                                                |                                               |                                                                                        |
|------------------------------------------------|-----------------------------------------------|----------------------------------------------------------------------------------------|
| [\[%wgt fhir%\]](%5B%wg%20fhir%%5D) Work Group | [Maturity Level](versions.html#maturity): N/A | [Standards Status](versions.html#std-process):[Informative](versions.html#std-process) |

HL7 has produced a number of other standards that don't overlap with FHIR as closely as those listed above, primarily because they aren't focused solely on information exchange. However, they deserve a brief mention:

### EHR Functional Model (EHR-FM)

This specification defines a number of functional behaviors for Electronic Health Record systems. FHIR is a healthcare information exchange standard that can be used to satisfy some of these functional behaviors. Details on how FHIR fits into the EHR-FM can be found [here](ehr-fm.html)

### Context Management Specifications (CCOW)

CCOW is a standard for allowing independent systems to synchronize context on a single workstation, providing a seamless interface for the user of that workstation (e.g. ensuring consistent user authentication, display of the same patient, display of the same order, etc.) In theory, FHIR resources could be used as an alternative CCOW implementation technology, however the business case for doing this is not clear. CCOW profiles include [HL7 v2](http://www.hl7.org/implement/standards/product_brief.cfm?product_id=185) mappings. These mappings can be used to help identify the equivalent FHIR data elements when establishing CCOW linkages in FHIR-based systems.

### Arden Syntax

Arden Syntax is a language for defining decision support rules. These rules reference data elements that are used as part of the decision making process. However, the specification does not define how these data elements are identified. FHIR element and extension identifiers would provide one mechanism for identifying the relevant data elements.

### Virtual Medical Record

The Virtual Medical Record is a draft specification under development by HL7 that also serves the decision support space. It defines a logical medical record that decision support rules can be constructed against. At present, this model is a custom model created specifically for VMR. However, the Decision Support work group is evaluating the possibility of using FHIR as a structure for future versions of the specification.

\[%file newfooter%\]
