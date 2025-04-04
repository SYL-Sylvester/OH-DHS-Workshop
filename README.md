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

     `Profile: BasicPatientProfile
     Parent: Patient
     Id: basic-patient
     Title: "Basic Patient Profile for Allergy Recording"
     Description: "A very basic profile requiring only name and gender."
     * name MS
     * gender MS`


### 2. Create a ValueSet 
For example: Create a new file (Using External CodeSystem - SNOMED CT) named allergy-severity-vs.fsh.

// Open allergy-severity-vs.fsh and add FSH code: 

    `ValueSet: AllergyReactionSeverityVS
     Id: allergy-severity-vs
     Title: "Allergy Reaction Severity Value Set"
     Description: "A value set for indicating the severity of an allergy reaction, using SNOMED CT."
     * include codes from system http://snomed.info/sct
         where concept is-a 24484000 // Severity of condition (finding)`


### 3. Create an Extension:
For example: Create a new file named allergy-reaction-severity-extension.fsh.

// Open allergy-reaction-severity-extension.fsh and add FSH code: 

    `Extension: AllergyReactionSeverityExt
    Id: allergy-reaction-severity
    Title: "Allergy Reaction Severity Extension"
    Description: "Extension to record the severity of an allergy reaction."
    Context: AllergyIntolerance.reaction
    ContextType: element
    ValueCodeableConcept:
        binding: AllergyReactionSeverityVS (required)`


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


### 5. Create another Profile 
For example: Create a new file named allergy-intolerance-profile.fsh. (with Slicing on reaction)

// Open allergy-intolerance-profile.fsh and add FSH code: 

    `Profile: AllergyToPeanutProfile
    Parent: AllergyIntolerance
    Id: allergy-to-peanut
    Title: "AllergyIntolerance Profile for Peanut Allergy"
    Description: "A profile for recording a patient's allergy specifically to peanuts, including reaction details."
    * patient MS
    * substance MS
    * substance.coding MS
    * substance.coding only AllergySubstanceCS#peanut "Must be coded as Peanut from our Allergy Substance CodeSystem"
    * reaction 1..* MS
    * reaction ^slicing.discriminator.type = #pattern
    * reaction ^slicing.discriminator.path = "manifestation.coding.system"
    * reaction ^slicing.discriminator.value.code = "http://snomed.info/sct"
    * reaction ^slicing.rules = #open
    * reaction[snomed-manifestation] MS where manifestation.coding contains (system = 'http://snomed.info/sct')
    * reaction[snomed-manifestation].extension[AllergyReactionSeverityExt]`

// Explanation:

This profile requires a patient and a substance.
It constrains the substance to be coded specifically as "peanut" from our AllergySubstanceCS.
It requires at least one reaction and introduces slicing based on the manifestation.coding.system.
It creates a slice named "snomed-manifestation" that is Must Support and requires a coding from SNOMED CT, and includes our AllergyReactionSeverityExt on this slice.


### 6. Create Example Instances
For example: Create a file named allergy-examples.fsh.

// Open allergy-examples.fsh and add FSH code:

    `Instance: PatientAlice
    InstanceOf: BasicPatientProfile
    Usage: #example
    * name.given = "Alice"
    * name.family = "Smith"
    * gender = #female`

    `Instance: PeanutAllergyAlice
    InstanceOf: AllergyToPeanutProfile
    Usage: #example
    * patient = Reference(PatientAlice)
    * substance.coding[0].system = "http://example.org/fhir/allergy-substances" // Replace with your IG's CodeSystem URL if publishing
    * substance.coding[0].code = #peanut
    * substance.coding[0].display = "Peanut"
    * reaction[snomed-manifestation].manifestation.coding[0].system = "http://snomed.info/sct"
    * reaction[snomed-manifestation].manifestation.coding[0].code = "247472004" // Hives
    * reaction[snomed-manifestation].manifestation.coding[0].display = "Urticaria"
    * reaction[snomed-manifestation].extension[AllergyReactionSeverityExt].valueCodeableConcept = SeverityModerate`

    `Instance: SeverityModerate
    InstanceOf: AllergyReactionSeverityVS
    Usage: #inline
    * coding[0].system = "http://snomed.info/sct"
    * coding[0].code = "6736005" // Moderate (qualifier value)
    * coding[0].display = "Moderate"`

// Explanation:

We need to create an example PatientAlice instance.
We need to create an AllergyIntolerance instance named PeanutAllergyAlice that references PatientAlice.
In the Instance above, the substance is coded as "peanut" from our local CodeSystem
We included a reaction with a SNOMED CT code for "Hives" and include our AllergyReactionSeverityExt with a value indicating "Moderate" severity (using an inline instance referencing SNOMED CT).


### 7. Save All Files: Ensure all your .fsh files are saved.


### 8. Stage Your Changes:
* Open the Source Control panel and stage all the newly created and modified files.


### 9. Commit Your Changes:
* Enter a commit message like: "Exercise: Created Allergy IG artifacts including external CS, splicing, and examples".
* Commit your changes.


### 10. Push Your Changes to GitHub:
* Push your local FSH-changes branch to the remote repository.


