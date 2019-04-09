\[%settitle Extensibility%\]
\[%file newnavbar%\]
&lt;%extheader base%&gt; <span id="root"></span>
Extensibility
=============

|                                                |                                                     |                                                                                      |
|------------------------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------|
| [\[%wgt fhir%\]](%5B%wg%20fhir%%5D) Work Group | [Maturity Level](versions.html#maturity): Normative | [Standards Status](versions.html#std-process):[Normative](versions.html#std-process) |

\[%normative page%\]
This exchange specification is based on generally agreed common requirements across healthcare - covering many jurisdictions, domains, and different functional approaches. It is common for specific implementations to have valid requirements that are not part of these agreed common requirements. Incorporating all valid requirements would make this specification very cumbersome and difficult to implement. Instead, this specification expects that additional valid requirements will be implemented as extensions.

As such, extensibility is a fundamental part of the design of this specification. Every element in a resource can have extension child elements to represent additional information that is not part of the basic definition of the resource. Applications should not reject resources merely because they contain extensions, though they may need to reject resources because of the specific contents of the extensions.

Note that, unlike in many other specifications, there can be no stigma associated with the use of extensions by any application, project, or standard - regardless of the institution or jurisdiction that uses or defines the extensions. The use of extensions is what allows the FHIR specification to retain a core simplicity for everyone.

To make the use of extensions safe and manageable, there is strict governance applied to the definition and use of extensions. Although any implementer can define and use extensions, there is a set of requirements that must be met as part of their use and definition.

<span id="Extension"></span> <span id="extension"></span>
Extension Element
-----------------

Every element in a resource or data type includes an optional "extension" child element that may be present any number of times. This is the content model of the extension as it appears in each resource:

&lt;%dt Extension 1%&gt;
Notes:

-   The `url` is a mandatory attribute / property and identifies a retrievable [extension definition](defining-extensions.html) that defines the content and meaning of the extension
-   The `url` SHALL be a URL, not a URN (e.g. not an OID or a UUID), and it SHALL be the canonical URL of a StructureDefinition that defines the extension. Except for child extensions defined within complex extensions, the URL SHALL be an absolute URL.
-   The structure definitions for the extension SHOULD be available to consumers of an instance. The preferred mechanisms for achieving this availability are the direct resolvability of the extension's canonical URL and/or the publishing of the extension definition on a registry that is available and known to those systems that will be consuming the instances containing the extension
-   An extension SHALL have either a value (i.e. a value\[x\] element) or sub-extensions, but not both. If present, the value\[x\] element SHALL have content (value attribute or other elements)
-   If it is not safe for an application processing the content of the resource to ignore the extension it SHALL be represented using a [Modifier Extension](#isModifier) rather than an `extension` element
-   There are a few resources (ones that do not specialize [DomainResource](domainresource.html)) where extensions are not allowed on the root element, but extensions can appear elsewhere through the resource
-   The *value\[x\]* element has an actual name of "value" and then the TitleCased name of one of these defined types, and its contents are those as defined for that type: <span id="list"></span>

Here is an example of an extension in XML ([see definition](extension-iso21090-en-use.html)):

``` xml
<name>
  <extension url="http://hl7.org/fhir/StructureDefinition/iso-21090-EN-use" >
    <valueCode value="I" />
  </extension>
  <text value="Chief Red Cloud"/>
</name>
```

In this example the name with text = "Chief Red Cloud" is extended to have a name use code of "Indigenous" (defined in ISO 21090, but very rarely used in practice).

In JSON, extensions are represented similarly:

``` json
{
  "extension": [
    {
      "url": "http://hl7.org/fhir/StructureDefinition/iso-21090-EN-use",
      "valueCode": "I"
    }
  ],
  "text": "Chief Red Cloud"
}
```

Making the types explicit in the representation means that all systems can read and write (and therefore store and/or exchange) extensions correctly without needing to access the definition of the extension.

Note that the JSON representation for extensions on primitive data types is handled differently. See [Representing primitive types in JSON](json.html#primitive) for further information.

Extensions can also contain extensions, either because the extension definition itself defines complex content - that is, a nested tree of values in the extension - or because the extension is extended with an additional extension defined separately.

In the case where an extension defines complex content, the identity of the parts of the extension are local/relative to the reference to the extension definition.

As an example, consider extending a patient with information about citizenship ([see definition](extension-patient-citizenship.html)) containing 2 fields: code and period. In XML:

``` xml
<Patient>
  <extension url="http://hl7.org/fhir/StructureDefinition/patient-citizenship" >
    <extension url="code" >
      <valueCodeableConcept>
        <coding>
          <system value="urn:iso:std:iso:3166" />
          <code value="DE" />
        </coding>
      </valueCodeableConcept>
    </extension>
    <extension url="period" >
      <valuePeriod>
        <start value="2009-03-14" />
      </valuePeriod>
    </extension>
  </extension>
  <!-- other data for patient -->
</Patient>
```

Or in JSON:

``` json
{
  "resourceType": "Patient",
  "extension": [
    {
      "url": "http://hl7.org/fhir/StructureDefinition/patient-citizenship",
      "extension": [
        {
          "url": "code",
          "valueCodeableConcept": {
            "coding": [
              {
                "system": "urn:iso:std:iso:3166",
                "code": "DE"
              }
            ]
          }
        },
        {
          "url": "period",
          "valuePeriod": {
            "start": "2009-03-14"
          }
        }
      ]
    }
  ]
}
```

This extension can be extended again, by adding a "passport-number" extension:

The passport number is defined as a separate extension (e.g. by an implementing organization, not in this specification) rather than part of the official citizenship extension. The URL of the extension is thus different. In XML:

``` xml

<Patient>
  <extension url="http://hl7.org/fhir/StructureDefinition/patient-citizenship" >
    <extension url="code" >
      <valueCodeableConcept>
        <coding>
          <system value="urn:iso:std:iso:3166" />
          <code value="DE" />
        </coding>
      </valueCodeableConcept>
    </extension>
    <extension url="period" >
      <valuePeriod>
        <start value="2009-03-14" />
      </valuePeriod>
    </extension>
    <extension url="http://acme.org/fhir/StructureDefinition/passport-number" >
        <valueString value="12345ABC" />
      </extension>
    <!-- other data for patient -->
  </extension>
</Patient>
```

or in JSON:

``` json
{
  "resourceType": "Patient",
  "extension": [
    {
      "url": "http://hl7.org/fhir/StructureDefinition/patient-citizenship",
      "extension": [
        {
          "url": "code",
          "valueCodeableConcept": {
            "coding": [
              {
                "system": "urn:iso:std:iso:3166",
                "code": "DE"
              }
            ]
          }
        },
        {
          "url": "period",
          "valuePeriod": {
            "start": "2009-03-14"
          }
        },
        {
          "url": "http://acme.org/fhir/StructureDefinition/passport-number",
          "valueString": "12345ABC"
        }
      ]
    }
  ]
}
```

Note that this passport number extension is shown here as an example defined by a fake organization, and not appropriate for re-use by implementers.

<span id="mustUnderstand"></span> <span id="isModifier"></span> <span id="modifierExtension"></span>
Modifier Extensions
-------------------

There are some cases where the information provided in an extension modifies the meaning of the element that contains it. Typically, this means information that qualifies or negates the primary meaning of the element that contains it. Some examples:

-   A flag on a \[Patient.contact\] indicating they are **not** to be contacted - i.e. a next of kin for record-keeping purposes only
-   Using the [Condition](condition.html) resource to record an assertion that a patient has a family history of the condition rather than the condition itself
-   Asserting that a performer was **not** actually involved in a [Procedure](procedure.html)
-   Asserting an additional subsumption relationship on a concept in a [value set](valueset.html)

Such extensions are known as "modifier extensions". For further information, see [the definition of what makes an element - or an extension - a modifier](conformance-rules.html#modifier). If modifier extensions are present, an application is restricted in its ability to safely process the resource unless it knows what the extension means for its own use of the data.

Implementers SHOULD avoid the use of modifier extensions where possible. Any use should be carefully considered against its possible downstream consequences. Inclusion of modifier extensions in an instance would be expected to significantly limit the ability of other systems to process the instance. However, implementers are often forced into these situations by the business arrangements around the use of resources, so this specification creates a framework for handling such cases. Implementers who are introducing an extension and are uncertain whether the extension should be marked as a modifier are encouraged to raise the question on [chat.fhir.org](http://chat.fhir.org).

This specification allows for such modifier elements to be included at the base of a resource or in any elements that do not have a data type (e.g. the elements that correspond to classes in the resource UML diagrams), and on a [few specially selected data types](datatypes.html#modifiers). Other data types and elements inside data types SHALL NOT have modifier extensions, and extensions SHALL NOT have modifier extensions internally (except for the reusable structures allowed to appear in extensions, listed above).

Note that complex extensions are allowed to have elements in the complex extension that are marked [Is-Modifier = true](conformance-rules.html#isModifier), which means that these elements modify the extension value itself. Internal extensions like this marked "Is-Modifier" are still represented using the `extension` element, not `modifierExtension` because the impact of the modifier element is expected to be known by applications that understand the containing extension.

Modifier extensions SHALL NOT change the meaning of any elements on Resource or DomainResource (including cannot change the meaning of modifierExtension itself). Is-modifier elements not defined as part of an extension's definition cannot be conveyed.

In XML, these modifier elements are represented using an element named `modifierExtension`, which has same content as the `extension` element documented above:

Example: There's no element on [MedicationRequest](medicationrequest.html) to write an "anti-prescription" - an instruction not to take a medication for a particular period. Classical clinical recording systems do not record this as a prescription - but one particular system does, and these "anti-prescription" records need to be shared within the institution where this happens as they are an important part of the workflow. Hence, applications are allowed to extend a resource with data like this:

``` xml
<MedicationRequest>
  <modifierExtension url="http://example.org/fhir/StructureDefinition/anti-prescription">
    <valueBoolean value="true"/>
  </modifierExtension>
  <!-- ... other content ... -->
</MedicationRequest>
```

Or in JSON:

``` json
{
  "resourceType": "MedicationRequest",
  "modifierExtension": [
    {
      "url": "http://example.org/fhir/StructureDefinition/anti-prescription",
      "valueBoolean": true
    }
  ],
  .. other content ...
}
```

Implementations processing the data in resources SHALL check for modifiers anywhere they could appear, and if a modifier extension is present on a data element that the application 'processes', SHALL do one of these things:

1.  recognize the modifier extension, and understand the impact of the extension when using the data
2.  reject instances that contain the modifier extension
3.  treat such instances as "for rendering purposes only" - i.e. check that the narrative status = `extensions` or `generated`, display the narrative and don't process the discrete data
4.  Ask for a human to check the modifier extension before proceeding to process the data
5.  carry a warning concerning the data along with any action or output that results from processing the data to inform users that it has not fully understood the source information

<span id="processing"></span>
Processing the data of a resource typically means copying or filtering data out of a resource for use in another context (display to a human, decision support, exchange in another format where not all information is included, or storing it for this kind of use). Servers and background processes that simply move whole resources around unchanged are not "processing the data of the resource", and therefore these applications are not required to check for unknown modifier extensions.

**\#1**: When an application understands this extension, it means that some developer has provided appropriate instructions for what to do with the data contained in it because of the existence of the modifier extension or has determined that the modifier does not impact on the system's computational functions. Note that this assessment needs to be repeated each time a system's computational behavior changes

**\#2**: This means that implementations are not inherently required to "support" a modifier extension in any meaningful way - they can achieve this understanding by rejecting instances that contain this extension (a server, for instance, could return a HTTP 422 status code with an [OperationOutcome](operationoutcome.html) if a client PUTs or POSTs a modifier extension it does not know). Applications might also be able to ignore a modifier extension if they know that it is safe to do so in their own context, though this would not usually be the case.

Implementations SHALL ensure that they do not process data containing unrecognized modifier extensions. Note that implementations might be able to be sure, due to their implementation environment (e.g. specific trading partner agreement), that modifier extensions will never occur, and can therefore meet the requirement to check for modifiers at the design stage. However, since integration and deployment options often change, applications SHOULD always check for modifier extensions when processing resources.

**\#3**: One way to warn the user is to download the extension definition from the given URL, and then use the defined display name to present the extension to the user. An error message could look something like this:

![](modifier-extension-warning.png)
Note that the narrative of the resource SHALL contain modifying information, so it is safe to show this to the user as an expression of the resource's content. A warning dialog box could be extended to offer the user the choice to see the original narrative.

Here is the prescription example from above with narrative:

``` xml
<MedicationRequest xmlns="http://hl7.org/fhir">
  <text>
    <status value="generated"/>
    <div xmlns="http://www.w3.org/1999/xhtml">
      <p><b>Note: This prescription is an instruction NOT to take a medication</b></p>
      <!-- snip actual narrative -->
    </div>
  </text>
  <!-- ...data... -->
  <modifierExtension url="http://example.org/fhir/StructureDefinition/anti-prescription">
    <valueBoolean value="true"/>
  </modifierExtension>
  <!-- ...data... -->
</MedicationRequest>
```

An application only needs to concern itself with modifier extensions on elements that it processes. Take, for example, a case where a procedure resource has a modifier extension on one of the `performer` elements indicating that they did not participate in the procedure. If an application is not using the performer details at all, the fact that one of the performers has a modifier extension is irrelevant and the application is free to ignore it. If the application does process the performers, and it sees the modifier extension, it must act in one of the ways outlined above.

[Implementation Guides](implementationguide.html) might place limitations on the appearance of modifier extensions within instances that comply with the implementation guide.

<span id="Summary"></span>
### Summary: Conformance Rules for Modifier Extensions

-   A Modifier Extension SHALL only modify the element which contains it and/or that element's children
-   It SHALL always be safe to show the narrative to humans; any modifier extension SHALL be represented in the narrative
-   Applications SHALL always ensure unrecognized modifier extensions are not present when processing the data from any element that might have carry modifier extensions
-   If a Modifier Extension that an application does not understand is present, the application SHALL refuse to process the resource or affected element, or SHALL provide an appropriate warning to its users

<span id="Special-Case"></span>
### Special Case: Missing data

In some cases, implementers might find that they do not have appropriate data for an element with minimum cardinality = 1. In this case, the element must be present, but unless the resource or a profile on it has made the actual value of the primitive data type mandatory, it is possible to provide an extension that explains why the primitive value is not present:

``` xml
<uri>
  <extension url="http://hl7.org/fhir/StructureDefinition/data-absent-reason">
    <valueCode="unknown"/>
  </extension>
</uri>
```

In this example, instead of a value, a [data missing code](general-extensions.html) is provided. Note that it is not required that this particular extension be used. This extension is **not** a modifier extension, because the primitive data type has no value.

It is not valid to create a fictional piece of data for the primitive value, and then add an extension indicating that the data has been constructed to meet the data rules. This would be both a bad idea as well as a modifier extension, which is not allowed on simple data types.

<span id="exchange"></span>
Exchanging Extensions
---------------------

*Note: This section describes the use of "non-modifier extensions", except where "modifier extensions" are explicitly mentioned (see [Modifier Extensions](#isModifier) above for details).*

Extensions are a way of allowing local requirements to be reflected in a resource using a common information based approach so that all systems can confidently process them using the same tools. However, when it comes to processing the information, applications will be constrained in their ability to handle extensions by the degree to which they are informed about them.

While the structured definition of an extension should always be available (see below for details), the mere availability of a definition does not automatically mean that applications know how to handle them correctly - generally, human decisions are required to determine how the data in extensions should be handled, along with the implicit obligations that surround the information.

For this reason, local requirements that manifest as extensions are an obstacle to integration and interoperability. The more the requirements are shared (i.e. regional or national scale), the less of an obstacle the extensions (and the requirements they represent) will represent. The consistent representation, definition and registration of extensions that this specification defines cannot resolve that problem - it only provides a framework within which such local variations can be handled more easily.

When it comes to deploying applications that support local requirements, situations will very likely arise where different applications exchanging information with each other are supporting different sets of extensions. This specification defines some basic rules that are intended to make management of these situations easier, but it cannot resolve them.

-   When exchanging resources, systems SHOULD retain unknown extensions when they are capable of doing so (just as they SHOULD retain core elements when they are capable of doing so)
-   If a system modifies a resource it SHOULD remove any extensions that it does not understand from the modified element and its descendants, because it cannot know whether the modifications it has made might invalidate the value of the unknown extension
-   Systems that drop existing elements are considered to be "[processing the containing element](#processing)"
-   A system SHALL NOT modify a resource or element that contains "modifier" extensions it doesn't understand
-   Applications SHOULD ignore extensions that they do not recognize if they are not "modifier" extensions

The degree to which a system can retain unknown extensions is a function of the type of system it is: a general purpose FHIR server, or a middleware engine would be expected to retain all extensions, while an application that manages patient registration through a user interface can only retain extensions to the degree that the information in them is part of the set managed by the user. Other applications will fall somewhere between these two extremes.

<span id="Summary2"></span>
### Summary: Handling extensions

Use the following rules as a guideline for handling resources:

-   When writing extensions, make sure they are defined and published
-   When reading, navigating through or searching on elements that can have modifier extensions, check whether there are any modifier extensions present
-   When reading elements, read and process the extensions you know and use, and ignore other extensions **except for modifier extensions**
-   Retain extensions whenever you can

\[%file newfooter%\]