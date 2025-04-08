# OH-DHS-Workshop
This repository contains files for an exercise creating an IG project for OH FSH Onsite workshop

## Practical Example: Building an Allergy Recording Implementation Guide

**Prerequisites:**

* You have cloned the workshop repository to your local machine.
* You have checked out the FSH-changes branch in VS Code.
* You have the FSH extension installed in VS Code.

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
          
     Instance: SeverityModerate
     InstanceOf: AllergyReactionSeverityVS
     Usage: #inline
     * coding[0].system = "http://snomed.info/sct"
     * coding[0].code = "6736005" // Moderate (qualifier value)
     * coding[0].display = "Moderate"



### 3. Create an Extension:
For example: Create a new file named allergy-reaction-severity-extension.fsh.

// Open allergy-reaction-severity-extension.fsh and add FSH code: 

     Extension: AllergyReactionSeverityExt
     Id: allergy-reaction-severity
     Title: "Allergy Reaction Severity Extension"
     Description: "Extension to record the severity of an allergy reaction."
     Context: AllergyIntolerance.reaction
     * ValueCodeableConcept from AllergyReactionSeverityVS (required)


### 4. Create a CodeSystem (Local)
For example: Create a new file named allergy-substance-cs.fsh.

// Open allergy-substance-cs.fsh and add FSH code:

    `CodeSystem: AllergySubstanceCS
    Id: allergy-substance-cs
    Title: "Allergy Substance Code System"
    Description: "A simple code system for common allergy substances."
    * #peanut "Peanut" "Allergy to peanuts."
    * #shellfish "Shellfish" "Allergy to shellfish."
    * #latex "Latex" "Allergy to latex."`


### 5. Create another Allergy-Intolerance Profile with example instance
For example: Create a new file named allergy-intolerance-profile.fsh. (with Slicing on reaction)

// Open allergy-intolerance-profile.fsh and add FSH code: 

    Profile: AllergyToPeanutProfile
    Parent: AllergyIntolerance
    Id: allergy-to-peanut
    Title: "AllergyIntolerance Profile for Peanut Allergy"
    Description: "A profile for recording a patient's allergy specifically to peanuts, constraining the allergen code and detailing reactions, including SNOMED CT manifestations and severity."
    // Base element constraints (ensure required elements are Must Support)
    * patient 1..1 MS // Patient is already 1..1 in base Resource, adding MS is good practice
    * clinicalStatus MS
    * verificationStatus MS

    // Constraint on the Allergen Code (using AllergyIntolerance.code, which is preferred)
    * code 1..1 MS // Make the primary allergen code mandatory and Must Support
    * code.coding 1..1 MS // Require exactly one coding for simplicity, mark MS
    * code.coding.system = #http://your-codesystem-url/AllergySubstanceCS // Fixed system (replace with actual URL)
    * code.coding.code = #peanut // Fixed code
    // Alternatively, bind to a ValueSet containing only the peanut code:
    // * code from AllergySubstanceVS (required) // Assumes AllergySubstanceVS contains only the desired peanut code

    // Reaction Element Constraints and Slicing
    * reaction 1..* MS // Require at least one reaction, mark as MS

    // Define Slicing on reaction based on manifestation coding system
    * reaction ^slicing.discriminator.type = #value // Discriminate based on the value of the system element
    * reaction ^slicing.discriminator.path = "manifestation.coding.system" // Path to the element whose value differentiates slices
    * reaction ^slicing.rules = #open // Allow reactions that don't match defined slices

    // Define the Slice for SNOMED CT coded manifestations
    * reaction contains snomedManifestation 0..* MS // Define the slice, make it Must Support

     // Constraints specific to the snomedManifestation slice
     * reaction[snomedManifestation].manifestation 1..1 MS // Require manifestation within this slice, mark MS
     * reaction[snomedManifestation].manifestation.coding MS // Mark coding within manifestation as MS
     * reaction[snomedManifestation].manifestation.coding ^slicing.discriminator.type = #value
     * reaction[snomedManifestation].manifestation.coding ^slicing.discriminator.path = "system"
     * reaction[snomedManifestation].manifestation.coding ^slicing.rules = #open // Slice codings within manifestation
     * reaction[snomedManifestation].manifestation.coding contains snomedCoding 1..* // Require at least one SNOMED coding
     * reaction[snomedManifestation].manifestation.coding[snomedCoding].system = "http://snomed.info/sct" // Fix system to SNOMED CT for this coding slice
     // * reaction[snomedManifestation].manifestation.coding[snomedCoding].code from SomeSnomedValueSet (example) // Optionally bind code

    Instance: PeanutAllergyAlice
    InstanceOf: AllergyToPeanutProfile
    Usage: #example
    * patient = Reference(PatientAlice)
    * substance.coding[0].system = "http://example.org/fhir/allergy-substances" // Replace with your IG's CodeSystem URL if publishing
    * substance.coding[0].code = #peanut
    * substance.coding[0].display = "Peanut"
    * reaction[snomed-manifestation].manifestation.coding[0].system = "http://snomed.info/sct"
    * reaction[snomed-manifestation].manifestation.coding[0].code = "247472004" // Hives
    * reaction[snomed-manifestation].manifestation.coding[0].display = "Urticaria"
    * reaction[snomed-manifestation].extension[AllergyReactionSeverityExt].valueCodeableConcept = SeverityModerate


// Explanation:
We need to create an example PatientAlice instance.
We need to create an AllergyIntolerance instance named PeanutAllergyAlice that references PatientAlice.
In the Instance above, the substance is coded as "peanut" from our local CodeSystem
We included a reaction with a SNOMED CT code for "Hives" and include our AllergyReactionSeverityExt with a value indicating "Moderate" severity (using an inline instance referencing SNOMED CT).


### 7. Save All Files: Ensure all your .fsh files are saved.

### *. Run sushi .
* Run the "sushi ." script


### 9. Run IG Publisher
* Run "./_updatePublisher.bat" script if using the publisher for the first time.
* Run : "./_genonce.bat" to generate IG





