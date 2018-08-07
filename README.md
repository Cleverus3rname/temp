# About SB-CSE Data Structure
This new and improved data structure takes the required data from CASE API and condenses it into a cohesive, lighter, and manageable format. 
Because this format is based on JSON, a Document Store Database would be the ideal implementation method. (e.x. MongoDB or Couchbase)
## JSON:
``` JSON {  
   "claim":{  
      "Title":"string",
      "Grade":"string",
      "Subject":"string",
      "Description":"string",
      "Short":"string",
      "Domain":"string",
      "Targets":[  
         {  
            "Title":"string",
            "Short":"string",
            "Standards":[  
               "string"
            ],
            "StdDesc":[  
               "string"
            ],
            "DOK":[  
               "string"
            ],
            "DOKDesc":[  
               "string"
            ],
            "Type":"string",
            "Clarification":"string",
            "Heading":"string",
            "Evidence":[  
               "string"
            ],
            "Vocab":"string",
            "Tools":"string",
            "StimInfo":"string",
            "DevNotes":"string",
            "Complexity":"string",
            "DualText":"string",
            "Accessibility":"string",
            "Stem":[  
               "string"
            ],
            "TaskDescription":[  
               "string"
            ],
            "TaskModel":[  
               "string"
            ],
            "Examples":[  
               "string"
            ],
            "Rubrics":[  
               "string"
            ],
            "Stimulus":[  
               "string"
            ],
            "ShortStem":[  
               "string"
            ],
            "KeyDetails":"string",
            "MSkill":"string"
         }
      ]
   }
}
```



## Claim:

|  **Key**  | **Description** | **CASE API Scope** | **Data Location** | 
|  :------ | :------ | :------ | :------ | 
|  Title (String)  | Claim # and title | getAllCFDocuments() | ``CFDocument[index].title`` | 
|  Grade (String) | Grade # 01-12 also includes HS for 09-12 | getAllCFDocuments() | ``CFDocument[index].notes`` |
|  Subject (String) | "Mathematics" or  "English Language Arts" | getAllCFDocuments() | ``CFDocument[index].subjectURI[0].title`` |
|  Description (String) | Claim Fulltext | getCFPackage(SB ELA/Math Content Spec) | ``CFItem[indexOfClaim].fullStatement`` |
|  Short (String) | Claim shortname | getCFPackage(SB ELA/Math Content Spec) | ``CFItem[indexOfClaim].abbreviatedStatement`` | 
|  Domain (String) | Optional Domain Fulltext | getCFPackage(SB ELA/Math Content Spec) | ``CFItem[indexOfDomain].fullStatement`` | 
|  Targets (String) | Array of target type | getCFPackage(Target Document) | see below | 

## Target:

|  **Key** | **Description** | **CASE API Scope** | **Data Location** |
|  :------ | :------ | :------ | :------ |
|  Title (String) | Target Name | getCFPackage(Claim-Target Document) | ``CFDocument.title`` |
|  Short (String) | Target shortname | getCFPackage(Claim-Target Document) | ``CFDocument.notes`` |
|  KeyDetails (String) | Target Fulltext | getCFPackage(Claim-Target Document) | ``CFItems[idxOfTarget].fullStatement`` |  
|  Standards [String] | Array of standard identifiers | getCFPackage(Claim-Target Document) | ``CFItems[idxOfMeasuredSkill].humanCodingScheme`` |
|  StdDesc [String] | Fulltext Description of above standard | getCFPackage(CCSS Import Doc) | ``CFItems[idxOfStdIdentifier].fullStatement`` |
|  DOK [String] | Depth of knowledge fulltext WIP | getCFPackage(SB ELA/Math Content Spec) | ``CFAssociations[idxOfTarget].destinationNodeURI.uri`` |
|  Type (String) | CAT, "PT", or "Both" - Math only has CAT - | getCFPackage(Claim-Target Document) | ``CFDocument.creator`` |
|  Clarificiation (String) | Clarification fulltext | getCFPackage(Claim-Target Document) | ``CFItems[idxOfClarification].fullStatement`` |
|  Heading (String) | Section Heading fulltext | getCFPackage(Claim-Target Document) | ``CFItems[idxOfHeading].fullStatement`` |
|  Evidence [String] | Array of Required Evidence fulltext | getCFPackage(Claim-Target Document) | ``CFItems[idxOfEvidenceReq].fullStatement`` |
|  Vocab (String) | key/construct vocabulary | getCFPackage(Claim-Target Document) | ``CFItems[idxOfGeneralReqs].fullStatement`` |
|  Tools (String) | allowed tools | getCFPackage(Claim-Target Document) | ``CFItems[idxOfGeneralReqs].fullStatement`` |
|  StimInfo (String) | Stimuli info | getCFPackage(Claim-Target Document) | ``CFItems[idxOfGeneralReqs].fullStatement`` |
|  DevNotes (String) | Developer notes | getCFPackage(Claim-Target Document) | ``CFItems[idxOfGeneralReqs].fullStatement`` |  
|  Complexity (String) | Stimuli complexity | getCFPackage(Claim-Target Document) | ``CFItems[idxOfGeneralReqs].fullStatement`` |
|  DualText (String) | Dual Text complexity | getCFPackage(Claim-Target Document) | ``CFItems[idxOfGeneralReqs].fullStatement`` |
|  Accessibility (String) | Accessibility fulltext | getCFPackage(Claim-Target Document) | ``CFItems[idxOfAccessibility].fullStatement`` |
|  TaskModel [String] | Array of task model fulltext | getCFPackage(Claim-Target Document) | ``CFItems[idxOfTaskModel].fullStatement`` |  
|  TaskDescription [String] | Parallel array to taskmodel of description fulltext | getCFPackage(Claim-Target Document) | ``CFItems[idxOfTaskDesc].fullStatement`` | 
|  Stimulus (String) | Stimulus Fulltext | getCFPackage(Claim-Target Document) | ``CFItems[idxOfStimulus].fullStatement`` | 
|  Stem [String] | Array of Stems fulltext | getCFPackage(Claim-Target Document) | ``CFItems[idxOfStems].fullStatement`` |  
|  Example [String] | Array of TaskModel Examples | getCFPackage(Claim-Target Document) | ``CFItems[idxOfExample].fullStatement`` |  
|  Rubric [String] | Array of TaskModel Scoring rules Fulltext | getCFPackage(Claim-Target Document) | ``CFItems[idxOfRubric].fullStatement`` |  
|  MSkill (String) | Measured Skill fulltext | getCFPackage(Claim-Target Document) | ``CFItems[idxOfMeasuredSkill].fullStatement`` |  

## From CASE to SB-CSE Data Structure (Step-by-step):
This section describes exactly what is required to scrape data from CASE to our new structure. This guide should be referenced when designing a script to scrape all data from CASE for our new DB.


### Step 1: Getting All Content Spec Documents

* Make a GET Request to CASE's getAllCFDocuments() API Call Ex:

``
https://case.smarterbalanced.org/ims/case/v1p0/CFDocuments?limit=99999999999&offset=0&sort&orderBy&filter&fields
``






