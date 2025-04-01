# FSH Workshop Exercise

Develop an Emergency Department (ED) Observation Implementation Guide for Ontario, the primary goal is to standardize the representation of essential clinical information captured during an ED visit to improve interoperability and data sharing across the province.

## I. OntarioEDPatient Profile Requirements:

* Base Resource: Must be based on the Patient resource.
* Mandatory Elements:
  * name: Must Support.
  * birthDate: Must Support.
  * gender: Must Support.
  * identifier: Must Support and occur at least once.
* Identifier Requirements:
  * The identifier.system element must be populated with the canonical URL for the Ontario Client Registry (or relevant provincial identifier system).
  * The identifier.value element must contain the patient's unique identifier from the specified system.
  * The identifier.type element SHOULD be populated with a CodeableConcept indicating the type of identifier (e.g., "MRN", "PHN"). A ValueSet for identifier types SHOULD be defined.
* Optional Elements:
  * telecom: May be included to capture contact information.
  * address: May be included to capture address information.


## II. OntarioEDEncounter Profile Requirements:

* Base Resource: Must be based on the Encounter resource.
* Mandatory Elements:
  * status: Must Support and bound to the standard FHIR ValueSet for Encounter Status.
  * class: Must Support and the code element MUST be coded with EMER from the http://terminology.hl7.org/CodeSystem/encounter-class CodeSystem.
  * subject: Must Support and reference an instance of the OntarioEDPatient profile.
  * period: Must Support, with both start and end elements present.
  * location: Must Support and occur at least once, referencing a Location resource. The location.physicalType SHOULD be bound to a ValueSet of ED-specific location types.
* Optional Elements:
  * reasonCode: May be included to capture the primary reason for the encounter, bound to a ValueSet of common ED presenting complaints (potentially using SNOMED CT).
  * participant: May be included to document involved healthcare providers.
  * hospitalization.dischargeDisposition: May be included to record the patient's disposition upon leaving the ED, bound to the OntarioEDDispositionValueSet.


## III. OntarioEDCondition Profile Requirements:

* Base Resource: Must be based on the Condition resource.
* Mandatory Elements:
  * subject: Must Support and reference an instance of the OntarioEDPatient profile.
  * code: Must Support. The coding element MUST be present and SHOULD primarily use codes from SNOMED CT. The code element SHOULD be bound to a ValueSet of common ED presenting problems or diagnoses.
  * clinicalStatus: Must Support and bound to the standard FHIR ValueSet for Condition Clinical Status.
* Optional Elements:
  * onsetDateTime OR onsetAge OR onsetPeriod OR onsetRange OR onsetString: May be included to specify the onset of the condition.
  * evidence: May be included to document supporting evidence.


## IV. OntarioEDObservation Profiles (One for Each Vital Sign except blood pressure):

* Base Resource (for all): Must be based on the Observation resource.
*Mandatory Elements (for all):*
  * status: Must Support and bound to the standard FHIR ValueSet for Observation Status.
  * category: Must Support and include at least one coding from the http://terminology.hl7.org/CodeSystem/observation-category CodeSystem with the code "vital-signs".
  * code: Must Support.
  * subject: Must Support and reference an instance of the OntarioEDPatient profile.
  * effectiveDateTime OR effectivePeriod: Must Support.
  * valueQuantity: Must Support.
    
*Required Slices (on component element for Blood Pressure):*
  * Systolic Blood Pressure:
    * Must Support.
    * code element MUST be coded with LOINC code 8462-4 ("Systolic blood pressure").
    * valueQuantity element MUST be present and Must Support, with unit bound to the UCUM ValueSet and expected value "mm[Hg]".
  * Diastolic Blood Pressure:
    * Must Support.
    * code element MUST be coded with LOINC code 8480-2 ("Diastolic blood pressure").
    * valueQuantity element MUST be present and Must Support, with unit bound to the UCUM ValueSet and expected value "mm[Hg]".
      
*Specific Requirements for individual Vital Sign Profile:*  
  * OntarioEDHeartRateObservation Profile:
    * code element MUST be coded with LOINC code 8867-4 ("Heart rate").
    * valueQuantity.unit element MUST be bound to the UCUM ValueSet and expected value "/min".
    * Consider including a Must Support requirement for the OntarioEDArrivalTimeExtension.
  * OntarioEDRespiratoryRateObservation Profile:
    * code element MUST be coded with LOINC code 9279-1 ("Respiratory rate").
    * valueQuantity.unit element MUST be bound to the UCUM ValueSet and expected value "/min".
    * Consider including a Must Support requirement for the OntarioEDArrivalTimeExtension.
  * OntarioEDTemperatureObservation Profile:
    * code element MUST be coded with LOINC code 8310-5 ("Body temperature").
    * valueQuantity.unit element MUST be bound to the UCUM ValueSet and expected value "Cel" OR "F".
    * Consider including a Must Support requirement for the OntarioEDArrivalTimeExtension.
  * OntarioEDOxygenSaturationObservation Profile:
    * code element MUST be coded with LOINC code 2708-6 ("Oxygen saturation in Arterial blood").
    * valueQuantity.unit element MUST be bound to the UCUM ValueSet and expected value "%".
    * Consider including a Must Support requirement for the OntarioEDArrivalTimeExtension.
      
* OntarioEDChiefComplaintObservation Profile:
  * Base Resource: Must be based on the Observation resource.
    * Mandatory Elements: status, category (with "clinical-note"), code (bound to Chief Complaint LOINC ValueSet), subject, effectiveDateTime OR effectivePeriod, valueString.
    * code element MUST be bound to the OntarioEDChiefComplaintLOINCValueSet.
    * Consider including a Must Support requirement for the OntarioEDArrivalTimeExtension.
      
* Common Extensions (Consider applying to relevant individual vital sign profiles or at the Encounter level):
    * OntarioEDCTASExtension: May be relevant to the overall ED assessment and potentially linked at the Encounter level or to a primary Observation.
    * OntarioEDArrivalMethodExtension: Likely relevant at the Encounter level.
    * OntarioEDPainScoreExtension: Could be a separate Observation or an extension on a relevant Observation.


## V. CodeSystem Requirements:

* Ontario ED Disposition Codes:
  * Canonical URL: http://example.org/fhir/CodeSystem/on-ed-disposition (Replace with a proper URL).
  * Title: "Ontario Emergency Department Disposition Codes".
  * Description: "A code system defining the disposition of a patient upon discharge from an Ontario Emergency Department."
  * Codes: Include codes and display names for common dispositions (e.g., Discharged Home, Admitted to Inpatient, Transferred to Another Facility, Deceased). Each code MUST have a clear definition.

* Ontario Identifier Type Codes:
  * Canonical URL: http://example.org/fhir/CodeSystem/on-identifier-types (Replace with a proper URL).
  * Title: "Ontario Identifier Types".
  * Description: "A code system defining common identifier types used in Ontario healthcare."
  * Codes: Include codes and display names for identifier types like "MRN", "PHN".


## VI. ValueSet Requirements:

* Ontario CTAS ValueSet:
  * Canonical URL: http://example.org/fhir/ValueSet/on-ctas-levels (Replace with a proper URL).
  * Title: "Ontario Canadian Triage and Acuity Scale (CTAS) Levels".
  * Description: "A value set for the different levels of the Canadian Triage and Acuity Scale."
  * Inclusion Criteria: Include all valid CTAS levels (Level 1 - Resuscitation, Level 2 - Emergent, Level 3 - Urgent, Level 4 - Semi-Urgent, Level 5 - Non-Urgent). Specify the codes and display names for each level (likely using a pre-existing standard if available).

* Ontario Arrival Method ValueSet:
  * Canonical URL: http://example.org/fhir/ValueSet/on-ed-arrival-method (Replace with a proper URL).
  * Title: "Ontario Emergency Department Arrival Method".
  * Description: "A value set for the different ways a patient can arrive at an Ontario Emergency Department."
  * Codes: Include codes and display names for common arrival methods (e.g., Ambulance, Walk-in, Police, Transfer from another facility).

* UCUM ValueSet (for Vital Sign Units):
  * Canonical URL: http://unitsofmeasure.org (Reference the standard UCUM system).
  * Title: "Units of Measure (UCUM)".
  * Description: "A value set including common units of measure from the Unified Code for Units of Measure (UCUM) relevant to vital signs."
  * Inclusion Criteria: Include codes for "mm[Hg]", "/min", "Cel", "F", "%".

* ED Presenting Complaints ValueSet:
  * Canonical URL: http://example.org/fhir/ValueSet/on-ed-presenting-complaints (Replace with a proper URL).
  * Title: "Ontario Emergency Department Presenting Complaints".
  * Description: "A value set of common presenting complaints in an Ontario Emergency Department, using SNOMED CT."
  * Inclusion Criteria: Include relevant SNOMED CT codes for common ED complaints (e.g., chest pain, abdominal pain, headache). You may need to define specific inclusion criteria based on the scope of the IG.

* Identifier Type ValueSet:
Canonical URL: http://example.org/fhir/ValueSet/on-identifier-type (Replace with a proper URL).
Title: "Ontario Identifier Type".
Description: "A value set for the different types of identifiers used for patients in Ontario healthcare."
Inclusion Criteria: Include codes representing different identifier types (e.g., "MRN", "PHN"). You may reference codes from a standard terminology if appropriate.
