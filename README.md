# OH-DHS-Workshop
This repository contains information for learning to create an IG project for use in OH FSH Onsite workshop April 11th, 2025.

## Practical Example: Building an Allergy Recording Implementation Guide


### 1. Create a Basic Patient Profile
For example: Create a new file named patient-profile.fsh.

// Open patient-profile.fsh and add FSH code: 

     Profile: BasicPatientProfile
     Parent: Patient
     Id: basic-patient
     Title: "Basic Patient Profile for Allergy Recording"
     Description: "A very basic profile requiring only name and gender."
     * name MS
     * gender MS

     Instance: PatientAlice
     InstanceOf: BasicPatientProfile
     Usage: #example
     * name.given = "Alice"
     * name.family = "Smith"
     * gender = #female


### 2. Create a ValueSet 
For example: Create a new file (Using External CodeSystem - SNOMED CT) named allergy-severity-vs.fsh.

// Open allergy-severity-vs.fsh and add FSH code: 

     ValueSet: AllergyReactionSeverityVS
     Id: allergy-severity-vs
     Title: "Allergy Reaction Severity Value Set"
     Description: "A value set for indicating the severity of an allergy reaction, using SNOMED CT."
     * include codes from system http://snomed.info/sct where concept is-a #24484000 // Severity of condition (finding)


### 3. Create an Extension:
For example: Create a new file named allergy-reaction-severity-extension.fsh.

// Open allergy-reaction-severity-extension.fsh and add FSH code: 

     Extension: AllergyReactionSeverityExt
     Id: allergy-reaction-severity
     Title: "Allergy Reaction Severity Extension"
     Description: "Extension to record the severity of an allergy reaction."
     Context: AllergyIntolerance.reaction
     * value[x] only CodeableConcept
     * valueCodeableConcept from AllergyReactionSeverityVS (required)


### 4. Create a CodeSystem (Local)
For example: Create a new file named allergy-substance-cs.fsh.

// Open allergy-substance-cs.fsh and add FSH code:

    CodeSystem: AllergySubstanceCS
    Id: allergy-substance-cs
    Title: "Allergy Substance Code System"
    Description: "A simple code system for common allergy substances."
    * #peanut "Peanut" "Allergy to peanuts."
    * #shellfish "Shellfish" "Allergy to shellfish."
    * #latex "Latex" "Allergy to latex."`


### 5. Create Allergy Substance ValueSet 
For example: Create a new file (Using External CodeSystem - SNOMED CT) named allergy-substance-vs.fsh.

// Open allergy-substance-vs.fsh and add FSH code:

     ValueSet: AllergySubstanceVS 
     Id: allergy-substance-vs
     Title: "Allergy Substance VS"
     Description: "A basic substance Valueset with peanut and shellfish"
     * include codes from system AllergySubstanceCS 
     * AllergySubstanceCS#peanut "peanut"
     , AllergySubstanceCS#shellfish "shellfish


### 6. Create an ALlergy Intolerance Profile

For example: Create a new file named allergy-intolerance-profile.fsh

// Open allergy-intolerance-profile and add FSH code:

     Profile: AllergyIntoleranceProfile
     Parent: AllergyIntolerance
     Id: profile-AllergyIntolerance
     Title: "basic profile for allergy intolerance"
     Description: "A very basic profile with a severity extension on reaction"
     * code 1..1 MS
     * code from AllergySubstanceVS (extensible)
     * reaction 1..1 MS
     * reaction.extension contains allergy-reaction-severity 1..1 MS

// Create an Instnce of the profile

     Instance: PatientALiceAllergy
     InstanceOf: AllergyIntoleranceProfile // States conformance to the profile
     Usage: #example
     Title: "Patient Alice peanut allergy"
     Description: "An AllergyIntolerance instance for a peanut allergy"
     
     * id = "allergy-peanut-ex1"
     * patient.reference = "PatientAlice" // Link to the relevant patient record
     * clinicalStatus = http://terminology.hl7.org/CodeSystem/allergyintolerance-clinical#active // Current status
     * verificationStatus = http://terminology.hl7.org/CodeSystem/allergyintolerance-verification#confirmed // Status is confirmed
     
     // 'code' field: Mandatory (1..1), Must Support, bound to AllergySubstanceVS (extensible)
     // We use #peanut, which is present in AllergySubstanceVS.
     * code = AllergySubstanceCS#peanut "Peanut"
     
     // 'reaction' field: Mandatory (1..1), Must Support
     * reaction[0].manifestation[0].coding[0].system = "http://snomed.info/sct" // Example reaction manifestation
     * reaction[0].manifestation[0].coding[0].code = #39579001 // Anaphylaxis (disorder)
     * reaction[0].manifestation[0].coding[0].display = "Anaphylaxis"
     * reaction[0].manifestation[0].text = "Severe difficulty breathing, swelling"
     * reaction[0].description = "Rapid onset after ingestion of trace amount of peanut."
     * reaction[0].onset = "2023-05-20T14:15:00Z"
     
     // 'reaction.extension' for severity: Mandatory (1..1), Must Support
     // We use the 'allergy-reaction-severity' extension ID defined in the profile.
     // The value uses a code from the hypothetical AllergyReactionSeverityVS.
     * reaction[0].extension[allergy-reaction-severity].valueCodeableConcept = http://snomed.info/sct#6736007 "Mild"

Save All Files: Ensure all your .fsh files are saved.


### 7. Run sushi .
* Run the "sushi ." script


### 8. Run IG Publisher
* Run "./_updatePublisher.bat" script if using the publisher for the first time.
  then...
* Run : "./_genonce.bat" to generate IG





