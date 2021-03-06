\[%settitle Diagnostic Medicine Module%\]
\[%file newnavbar%\]
|                                                                              |                                                                                        |
|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|
| Work Group [\[%wgt oo%\]](%5B%wg%20oo%%5D) & [\[%wgt cg%\]](%5B%wg%20cg%%5D) | [Standards Status](versions.html#std-process):[Informative](versions.html#std-process) |

<span id="root"></span>
Diagnostic Medicine Module
--------------------------

<span id="intro"></span>
### Introduction

The Diagnostics Module provides an overview and guide to the FHIR content that addresses ordering and reporting of clinical diagnostics including laboratory testing, imaging and genomics.

<span id="index"></span>
### Index

The Diagnostics module covers the following resources:

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<tbody>
<tr class="odd">
<td><ul>
<li><a href="observation.html">Observation</a></li>
<li><a href="diagnosticreport.html">DiagnosticReport</a></li>
<li><a href="servicerequest.html">ServiceRequest</a></li>
</ul></td>
<td><ul>
<li><a href="media.html">Media</a></li>
<li><a href="imagingstudy.html">ImagingStudy</a></li>
<li><a href="molecularsequence.html">MolecularSequence</a></li>
</ul></td>
<td><ul>
<li><a href="specimen.html">Specimen</a></li>
<li><a href="bodystructure.html">BodyStructure</a></li>
</ul></td>
</tr>
</tbody>
</table>

The diagnostic resources and their relationships are shown below. The arrows represent the direction of the references between resources (for example, DiagnosticReport references ServiceRequest). See the [Workflow Module](workflow-module.html) for information about the coordination of activities such as ordering and fulfilling of diagnostics.

<img src="diagnostic-module-resources.png" alt="Image showing the diagnostic resources" width="800" /> \[%impl-note%\] See the [Genomics Implementation Guidance](genomics.html) for additional information about how to use the Diagnostic resources for Clinical Genomic Reporting and Analysis. \[%end-note%\]
**Name**
**Aliases**
**Description**
\[%res-item Observation%\] \[%res-item DiagnosticReport%\] \[%res-item ServiceRequest%\] \[%res-item Media%\] \[%res-item ImagingStudy%\] \[%res-item MolecularSequence%\] \[%res-item Specimen%\] \[%res-item BodyStructure%\]
<span id="secpriv"></span>
### Security and Privacy

The diagnostic resources often represent patient-specific data, and as such are susceptible to data breaching. Necessary privacy and security provisions must be in place when searching and fetching this information. For more general considerations, see [the Security and Privacy module](secpriv-module.html).

<span id="uses"></span>
### Common Use Cases

Diagnostic resources are commonly used to plan, recommend, order and report clinical diagnostics:

-   Planning a diagnostic test such as in this [Careplan-example](careplan-example-f203-sepsis.html)
-   Recommending diagnostics such as is done by Clinical Decision Support tools. See the [Clinical Reasoning module](clinicalreasoning-module.html) for more information.
-   Ordering a Laboratory Test such as a [lipid panel](servicerequest-example-lipid.html)
-   Reporting a Laboratory Test such as a [lipid panel](diagnosticreport-example-lipids.html)
-   Ordering an imaging study such as a [CT study](servicerequest-example-di.html)
-   Reporting an imaging study such as a [CT study](imagingstudy-example.html)
-   Ordering a genetic test such as a [gene mutation analysis](servicerequest-genetics-example-1.html)
-   Reporting a genetic test such as [HLA genotyping](diagnosticreport-hla-genetics-results-example.html)

There are many ways to use these resources independently as well. The Observation resource, in particular, is central to capturing many measurements and events in healthcare and is often used outside the context of diagnostic orders and reports.

-   recording vital signs such as [temperature](observation-example-f202-temperature.html)
-   recording other observations of a patient's social history such as tobacco use
-   recording other characteristics of a patient such as pregnancy status (note: The Condition resource may be used to represent these as well)
-   recording clinical assessment tool scores such as an [Apgar score](observation-example-5minute-apgar-score.html).
-   identifying and describing a [specimen](specimen-example-serum.html) or [body site](bodystructure-example-tumor.html)

<span id="related"></span>
### Related Resources and Modules

The resources that represent the basic information about a patient and a clinical encounter can be found in the [Administration Module](administration-module.html). Other resources that represent core clinical information generated by healthcare providers during the course of a patient encounter are detailed in the [Clinical Summary Module](clinicalsummary-module.html) and the [Medications Module](medications-module.html).

<span id="roadmap"></span>
### Developmental Roadmap

-   The *Observation* and *Diagnostic Report* resources have been tested and used in production tooling and as such have reached a [maturity level FMM=4](versions.html#maturity) where changes become less likely and require implementer community approval. Future work will focus on developing core profiles and clarifying use cases and boundaries and providing more domain specific examples. We anticipate *Observation* and *Diagnostic Report* reaching normative status by the end of this cycle.
-   The *ImagingStudy* resource has had some limited testing and use. Further use of this resource in production tooling is needed for it to reach a more stable FMM level.
-   The *ServiceRequest* resources has undergone substantial changes since DSTU2. Foremost of which is the merging of DSTU2 *DiagnosticOrder* and *ServiceRequest* into a single resource. In addition, as a result of the addition of [FHIR workflow](workflow.html) for STU3, its structure and content have been updated to align with the general FHIR workflow "request" pattern. Because of these, it remains a relatively immature resource. No more sweeping changes to its structure are expected. However, its scope and boundaries vis a vis other resources will be the topic of future workgroup efforts. We anticipate its broad use by implementers and that it will mature as a resource throughout the upcoming cycle.
-   The *Specimen* resource will be the focus of work group efforts for the upcoming cycle. A new *SpecimenDefinition* resource is planned for describing specimen types vs specimen instances and will take over some of the Specimen resource's current scope. We expect to see more widespread use of this resource and future updates to its FMM level
-   The *MolecularSequence* resource remains draft and we expect to see more widespread use of this resource and future updates to its FMM level
-   The *BodyStructure* resource is an immature resource with a FMM level of 1. To date there has been little implementation feedback.
-   The *Media* resource is the newest member to the Diagnostics module having undergone substantial scope and structural changes to create a specialized observation resource focused on digital media such as images and audio files. Future work will focus on developing core profiles and providing more domain specific examples.

\[%file newfooter%\]
