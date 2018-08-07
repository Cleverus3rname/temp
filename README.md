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

You'll notice that the ``Limit`` parameter is 999999999, this is just to ensure the API call returns ALL of the Content Spec Documents.

*Note: For some dumb reason, CASE will always return 2-3 less entries than specified in the ``Limit`` parameter.*

* The Response Body will be an array of all the CFDocument objects stored in the CASE DB

The majority of these will be SmarterBalanced Spec Documents with titles containing Subject, Grade, Claim, and Target.

*However*, a few of these Documents will **not** be Grade/Claim/Target, but still important.
Mainly these 4 Documents:

* **Smarter Balanced ELA Content Specification** 	(GUID: ``cbbb8d01-63fc-4adf-9803-a3781839b6b6``)
* **Smarter Balanced Math Content Specification** (GUID: ``255adad7-2854-47d7-9aa4-c5e1750eb8ca``)
* **CCSS Imported from Digital Library** (GUID: ``1a66f1ae-9f7d-4c86-9e24-d0f022279578``)
* **Norm Webb's Depth of Knowledge [DOK] Levels of Cognitive Difficulty** (GUID: ``b7bc9d8c-cb5f-4dc8-96da-13ce635053d1``)

Each of these 4 documents will come in handy later when we need information like Standards and Depth of Knowledge.


### Step 2: Parsing Subject, Grade, Claim, and Target Values from these Documents
We can now iterate through the array of CFDocuments to scrape their Subject, Grade and Claim information.

* Subject can be found in ``CFDocument[index].subjectURI[0].title`` *(Always use 0th index for subjectURI)*

* Grade, Claim, and Target can be found in ``CFDocument[index].title``

* The string found in ``CFDocument[index].title`` will be in one of the following formats:

#### ELA:
```
1. English Language Arts Specification: Grade *X* Claim *Y* Target *Z*

or

2. English Language Arts Performance Task Specification: Grade *X* "Performance Task Name"
```
 


#### Math:
```
1. Grade *X* Mathematics Item Specification C*Y* T*Z*

or

2. *HS* Mathematics Item Specification C*Y* T*Z*

or

3. Math Grades *X-X*, Claim *Y*

or

4. Math *High School*, Claim *Y* 
```

* **X** = Grade (Either INT from 3-8 **or** String HS/High School to represent 9-12)

* **Y** = Claim (INT 0-9)

* **Z** = Target (If ELA: INT 0-99, If Math: Char A-Z)



### Step 3: Getting Claim Description, Domain and Shortname
To get the fulltext description for a claim, its Domains, and a shortname that will be useful later, we need to move our scope from getAllCFDocuments() to getCFPackage().

* Make a GET Request to CASE's getPackage() API Call with the GUID of your subject's corresponding content spec document as a parameter (remember the 4 important documents from above?)

* **Smarter Balanced ELA Content Specification** 	(GUID: ``cbbb8d01-63fc-4adf-9803-a3781839b6b6``)
* **Smarter Balanced Math Content Specification** (GUID: ``255adad7-2854-47d7-9aa4-c5e1750eb8ca``)

**Example:**
**ELA Content Spec as Parameter:**

```
https://case.smarterbalanced.org/ims/case/v1p0/CFPackages/cbbb8d01-63fc-4adf-9803-a3781839b6b6
```

The Response Body will have a singular CFDocument object containing mostly unimportant information like the title of this document and its subject.
However, it also returns an array of CFItems that contain important information about claims like their descriptions, shortnames, and domains.

The main difficulty here is distinguishing "Claim" CFItems from other CFItems like "Measured Skills" and "Components"

* Filter out any CFItems that are not of type "Claim". 
* Item Types are found at ``CFItems[index].CFItemType``
* Claim Descriptions are found at ``CFItems[indexOfClaim].fullStatement``
* Claim Shortnames are found at ``CFItems[indexOfClaim].humanCodingScheme``
* The Domain **MIGHT** be found at ``CFItems[indexOfClaim + 1].fullStatement`` **IF** the ``CFItem[indexOfClaim + 1].CFItemType === "Domain"``
Domains are optionally included with claims.

#### Code Example:
``` javascript

for (var i = 0; i < CFItems.length; i++) {

	if (CFItems[i].CFItemType === "Claim") {
	
		console.log(CFItems[i].fullStatement);
		console.log(CFItems[i].humanCodingScheme);
		
		if(CFItems[i+1].CFItemType === "Domain") {
		
			console.log(CFItems[i+1].fullStatement);
			
		}
	}	
		
}
```
