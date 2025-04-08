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



### 5. Save All Files: Ensure all your .fsh files are saved.

### 6. Run sushi .
* Run the "sushi ." script


### 7. Run IG Publisher
* Run "./_updatePublisher.bat" script if using the publisher for the first time.
  then...
* Run : "./_genonce.bat" to generate IG





