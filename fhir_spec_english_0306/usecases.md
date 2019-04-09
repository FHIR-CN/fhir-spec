\[%settitle Common Example Scenarios in FHIR%\]
\[%file newnavbar%\]
<span id="root"></span>
Common Example Scenarios in FHIR
================================

|                                                |                                               |                                                                                        |
|------------------------------------------------|-----------------------------------------------|----------------------------------------------------------------------------------------|
| [\[%wgt fhir%\]](%5B%wg%20fhir%%5D) Work Group | [Maturity Level](versions.html#maturity): N/A | [Standards Status](versions.html#std-process):[Informative](versions.html#std-process) |

FHIR is a framework standard that defines a common way to solve healthcare problems and provides a set of resources that can be used in many ways. This page describes how certain common usage scenarios are implemented using the capabilities that FHIR defines. The provided scenarios are examples of usage and are not in any way exhaustive. FHIR can and will be used in a wide variety of circumstances.

In addition, to the information on this page, see also [Resource Guide](resourceguide.html).

<span id="phr"></span>
Personal Health Record (PHR)
----------------------------

**Note: Further significant development in the area of consumer centered data exchange is expected for the next release of this specification (R5).**

In the PHR scenario, an Electronic Medical Record system (EMR, though many other names and acronyms are also used) provides a RESTful API that allows patients to access their own medical record via a common web portal or mobile application, usually provided by a third party. In this scenario, the PHR provider:

-   Provides the patient with a login that identifies them (or links the patient record to an external identity provided by OpenID, Facebook, Google, etc.)
-   Authenticates the client using an appropriate OAuth server for the login (possibly their own) and restricts the client to viewing records associated with the specific patient (or patients, where appropriate access has been arranged)

The EMR exposes a FHIR server that supports the [search](http.html#search) and [read](http.html#read) operations on the following resources:

1.  the [Patient](patient.html) resource in order to provide demographics to the client. When a client searches patients with no search criteria, they get a list of all patients they have access to
2.  [search](http.html#search) and [read](http.html#read) on the [Document Reference](documentreference.html) resource to provide access to general patient documents in the form of PDFs etc. (PDFs are preferred)
3.  [search](http.html#search) and [read](http.html#read) on a set of [clinical resources](clinicalsummary-module.html)

Here is the Capabilities Statement for this scenario: [XML](capabilitystatement-phr-example.xml.html) or [JSON](capabilitystatement-phr-example.json.html).

The EMR may also choose to provide additional functionality, such as shared access to patient records by relatives/caregivers, to allow the patient to upload their own documents, medication statements, observations (e.g. from patient monitoring devices) and/or to allow the patient to make appointments. This additional functionality will involve additional API capabilities to be implemented and exposed. The EMR server may also choose to expose the [search](http.html#search), [read](http.html#read) and [history](http.html#history) operation on the [AuditEvent](auditevent.html) resource for the patient-specific records to allow patient review of record access. Note that all usage of the RESTful API should be logged in an AuditEvent resource.

<span id="xds"></span>
Document Sharing (XDS)
----------------------

One common way to integrate healthcare information from a variety of sources is to build a repository of documents around a patient record. Building a repository of documents allows for less stringent alignment around policy, procedures and record-keeping/informatics standards.

The most widely adopted framework for sharing documents within institutions, regions, states or countries is IHE Cross-Enterprise Document Sharing (XDS). XDS allows for a federated system of repositories with a registry to provide coordinated access to the documents.

FHIR provides equivalent functionality to XDS that can be used to implement XDS behind the existing XDS.b interface, to provide a simpler mobile-friendly interface to an existing XDS ecosystem, or to link document sharing into other functionality provided through a FHIR interface.

The following FHIR Resources are involved in the XDS functionality:

-   The [DocumentReference](documentreference.html) resource describes a document that is located elsewhere. A document registry is a system that maintains a set of Document References
-   The [Binary](http.html#binary) support can be used to store the actual documents on a FHIR server. A repository is a system that stores the binary document in addition to Document References (or sometimes without)
-   [Patient](patient.html), [Practitioner](practitioner.html) and [Organization](organization.html) resources provide support for identifying people and organizations
-   The [AuditEvent](auditevent.html) resource tracks usage of the document registry and repository
-   The [DocumentManifest](documentmanifest.html) resource describes a set of documents that are grouped together in original publication

At present, IHE is working with the FHIR project team to use FHIR for Mobile Health Documents (MHD).

<span id="decision"></span>
Decision Support
----------------

One common use of healthcare information systems is to integrate some form of decision support software into clinical systems. Common uses of clinical decision support are:

-   Drug-drug interaction checking, and more generally, prescription safety checks
-   Suggesting commonly missed diagnostic data interpretations (including delta checking)
-   Patient surveillance for early warning of deteriorating patient health (both acute and ambulatory care)
-   Identifying candidates for alternative treatment plans for improved efficacy

Note that in addition to clinical decision support, there are also infrastructural uses, such as managing access control.

The various forms of decision support each involve different interaction patterns, so there is no single decision support implementation in the FHIR specification. Generally, the patterns fall into several classes:

1.  The decision comes from an engine entirely hidden behind a system interface and has no direct impact on the data exchange
2.  The decision support engine uses existing data and generates alarm messages concerning patient state that are visible on the FHIR interface
3.  The decision support engine is consulted through a described interface; it accepts a request for, and returns a decision

Any decision support may fall into multiple categories at once, depending on the perspective of a given system.

1.  There is no support required from the FHIR specification, though there will be ongoing review of the contents of the resources to ensure that they support common decision support practices appropriately
2.  There is no suitable resource for this use yet. The [Flag](flag.html) resource is intended for clinical notes about the patient, and is not intended for this use. A resource called "Alarm" is under preparation for this purpose
3.  A request for a decision support is understood as a [search](search.html) using a named \_query that takes a set of parameters. See below

### Explicit Requests for Decisions

When a query is initiated in order to get a decision made, the following considerations apply:

**Request**

-   The request for a decision is made using one of the interaction patterns described for [search/query](search.html): A RESTful search, a query posted to [/Mailbox](messaging.html#mailbox), a query [message](messaging.html), or the Asynchronous query pattern
-   The request has a \_query parameter that identifies the decision that is being requested
-   The request also has a set of parameter values. These parameter values may be the data that describes the decision being made or they may be references to specific resources that contain the request. In general, the more complex the decision request is, the more likely it is that a full resource is appropriate, particularly since this provides a ready-made way to record and manage the requests themselves.
-   In some of the query interaction patterns, the resources identified in the parameter value can be bundled up with the request. In others, only the references can be passed
-   Which of the query patterns is most appropriate depends on the complexity of the decision support input, and the length of time the decision is expected to take. As either of these increases, the more complex query patterns become more appropriate

**Response**

-   If the decision support engine is unable to provide the requested decision, it returns an [OperationOutcome Resource](operationoutcome.html) describing the issue
-   Otherwise it returns a resource that represents the decision, along with other resources as supporting information, as described by the resource, or applicable profiles
-   In principle, the decision provider can choose to make a copy of the returned decision resource available through a normal RESTful interface, or it can choose not to. This decision may be constrained by applicable profiles, policy decision, or the innate nature of the query
-   If either the decision provider or the requester chooses to retain a copy of the decision, they must ensure that the (lack of) currency of the decision is appropriately considered when it is used

It follows from this then, that decisions that may be requested need at least a response resource defined, and possibly a request resource. This table summarizes known decisions for which resources have been defined.

|                                              |                                                                         |                                                          |
|----------------------------------------------|-------------------------------------------------------------------------|----------------------------------------------------------|
| **Decision**                                 | **Resources**                                                           | **Invocation**                                           |
| What immunizations should this patient have? | Response: [ImmunizationRecommendation](immunizationrecommendation.html) | The exact way to invoke this decision is not yet defined |

Implementers are can use existing resources for decisions not documented here, but there is no guarantee that they will be suitable. Improving decision support will be a focus for ongoing development during the Trial Use period.

\[%file newfooter%\]
