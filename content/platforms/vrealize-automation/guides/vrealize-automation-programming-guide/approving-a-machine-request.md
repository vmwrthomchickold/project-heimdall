# Approving a Machine Request

You use a series of work item service commands to approve a machine request. 

Basic components of the work item service are the work item and the assignment. The work item service provides a standard way to present work items to users. For example, a user can view all work items and select the item to perform such as approving a machine request.

To approve a machine request, you first get a work item ID, then specify the ID in the approval. 

## Prerequisites

* Log in to vRealize Automation as an approver with at least one of the following qualifications: 
  * You are designated as an approver in an approval policy. 
  * You belong to a group which has been designated as an approval group in an approval policy. 
  * You are designated as a delegate for someone who is an approver. 
* Verify that the appliance name and fully qualified domain name of the vRealize Automation instance are available. 
* Verify that you have a valid HTTP bearer token that matches your login credentials. See [REST API Authentication](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-05B9796E-1C20-4860-B77B-1E7F4BD7E556.html#GUID-05B9796E-1C20-4860-B77B-1E7F4BD7E556). 

## Procedure

1. List all available work item IDs. 

   ```text
   curl --insecure -H "Content-Type: application/json" 
   -H "Authorization: Bearer $token" 
   https://$vRA/workitem-service/api/workitems
   ```

   For details regarding input and output for this request, see [Syntax for Listing Work Items](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-91EC5E52-07CC-4F75-8301-2A27009E27B1.html#GUID-91EC5E52-07CC-4F75-8301-2A27009E27B1). 

2. Examine the response to find the workItemId
3. Get details for a specific work item ID. 

   Use the workItemId to get the details for this work item. In this example, the workItemId is 5e3e9519-78ea-4409-a52c-e4aa3bc56511. 

   ```text
   curl --insecure -H "Content-Type: application/json" 
   -H "Authorization: Bearer $token" 
   https://$vRA/workitem-service/api/workitems/5e3e9519-78ea-4409-a52c-e4aa3bc56511
   ```

   For details regarding input and output for this request, see [Syntax for Getting Work Item Details](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-7C03A5C3-087F-4021-A873-095EE66B200C.html#GUID-7C03A5C3-087F-4021-A873-095EE66B200C). 

4. Construct a JSON file that contains the work item ID information that you need to approve a machine request. 

   1. Copy the appropriate JSON input file template to a new file in an XML editor that maintains formatting. 
   2. Substitute the input variables in the template with the values you obtained for your specific work item ID, for example 5e3e9519-78ea-4409-a52c-e4aa3bc56511. 
   3. Save the file with a new name, for example, approve.json. 

   For details regarding input and output for this request, see [Syntax for Constructing a JSON File to Approve a Machine Request](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-6A843F38-8C57-4165-8E5C-07A3C055BA27.html#GUID-6A843F38-8C57-4165-8E5C-07A3C055BA27). 

5. Approve the submitted machine request by specifying the work item ID and including the JSON file as part of the command line. 

   ```text
   curl --insecure -H "Content-Type:application/json" 
   -H "Authorization: Bearer $token" 
   https://$vRA/workitem-service/api/workitems/5e3e9519-78ea-4409-
   a52c-e4aa3bc56511/actions/com.mycompany.csp.core.approval.action.approve 
   --d @approve.json
   ```

   For details regarding input and output for this request, see [Syntax for Approving a Submitted Machine Request](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-85FAD2F3-7C91-41E3-A57F-DA30C1D70290.html#GUID-85FAD2F3-7C91-41E3-A57F-DA30C1D70290). 

## Results

If the command is successful, the HTTP status is 201 Created. If the command is not successful, the HTTP status is 204 No Content.

## Work Item Service Examples

Syntax for each service example lists input parameters, output parameters, and curl commands.

### Listing Work Items

GET /api/workitems lists the unique IDs of all available work items. 

#### Inputs

Use the supported input parameters to control the command output. 

| Parameter  | Description  |
| :--- | :--- |
| URL  | https://$vRA/workitem-service/api/workitems  |
| $vRA | Specifies the appliance name and fully qualified domain name, or IP address of the vRealize Automation server.  |
| $token | Specifies a valid HTTP bearer token with necessary credentials.  |

#### Output

The command output contains property names and values based on the command input parameters. 

<table>
  <thead>
    <tr>
      <th style="text-align:left">Property</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">Links</td>
      <td style="text-align:left">
        <p>Specifies an array of link objects, each of which contains the following
          parts:</p>
        <ul>
          <li>rel: Specifies the name of the link.
            <ul>
              <li>Self refers to the object that was returned or requested. This property
                does not exist when you query for a single profile.</li>
              <li>First, Previous, Next, and Last refer to corresponding pages of pageable
                lists.</li>
              <li>Specifies the application or service that determines the other names.</li>
            </ul>
          </li>
          <li>href: Specifies the URL that produces the result.</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">work itemNumber</td>
      <td style="text-align:left">Displays a reference number for the work item.</td>
    </tr>
    <tr>
      <td style="text-align:left">id</td>
      <td style="text-align:left">Specifies the unique identifier of this resource.</td>
    </tr>
    <tr>
      <td style="text-align:left">version</td>
      <td style="text-align:left">Displays the object version number.</td>
    </tr>
    <tr>
      <td style="text-align:left">assignees</td>
      <td style="text-align:left">Displays the list of work item assignees.</td>
    </tr>
    <tr>
      <td style="text-align:left">subTenantId</td>
      <td style="text-align:left">Optionally associates the work item with a specific business group granting
        users with management responsibilities over that business group permission
        to see the approval.</td>
    </tr>
    <tr>
      <td style="text-align:left">tenantId</td>
      <td style="text-align:left">Specifies the tenant ID for the work item.</td>
    </tr>
    <tr>
      <td style="text-align:left">callbackEntityId</td>
      <td style="text-align:left">Specifies the callback entity ID for the work item.</td>
    </tr>
    <tr>
      <td style="text-align:left">work itemType</td>
      <td style="text-align:left">Specifies the work item type for the work item.</td>
    </tr>
    <tr>
      <td style="text-align:left">completedDate</td>
      <td style="text-align:left">Specifies the date when the work item was completed.</td>
    </tr>
    <tr>
      <td style="text-align:left">assignedDate</td>
      <td style="text-align:left">Specifies the date when the work item was assigned.</td>
    </tr>
    <tr>
      <td style="text-align:left">createdDate</td>
      <td style="text-align:left">Specifies the created date of this instance.</td>
    </tr>
    <tr>
      <td style="text-align:left">assignedOrCompletedDate</td>
      <td style="text-align:left">Specifies the date to be displayed on UI.</td>
    </tr>
    <tr>
      <td style="text-align:left">formUrl</td>
      <td style="text-align:left">Specifies the URL from which the layout for this work item can be retrieved.</td>
    </tr>
    <tr>
      <td style="text-align:left">serviceId</td>
      <td style="text-align:left">Specifies the service ID that generated this work item instance.</td>
    </tr>
    <tr>
      <td style="text-align:left">work itemRequest</td>
      <td style="text-align:left">Specifies the corresponding work item request object.</td>
    </tr>
    <tr>
      <td style="text-align:left">status</td>
      <td style="text-align:left">Specifies the status of the work item.</td>
    </tr>
    <tr>
      <td style="text-align:left">completedBy</td>
      <td style="text-align:left">Specifies the principal ID of user who completed the work item.</td>
    </tr>
    <tr>
      <td style="text-align:left">availableActions</td>
      <td style="text-align:left">Contains a list of relevant work item actions.</td>
    </tr>
    <tr>
      <td style="text-align:left">Metadata</td>
      <td style="text-align:left">
        <p>Specifies the paging-related data:</p>
        <ul>
          <li>Size: Specifies the maximum number of rows per page.</li>
          <li>totalElement: Specifies the number of rows returned.</li>
          <li>totalPages: Specifies the total number of pages of data available.</li>
          <li>Number: Specifies the current page number.</li>
          <li>Offset: Specifies the number of rows skipped.</li>
        </ul>
      </td>
    </tr>
  </tbody>
</table>#### curl Command

The following example command retrieves all the available work item IDs. 

```text
curl --insecure -H "Content-Type: application/json" 
-H "Authorization: Bearer $token” 
https://$vRA/workitem-service/api/workitems
```

#### JSON Output

The following JSON output is returned based on the command input. Note:

Price is referred to as `cost` in API commands and output. 

```text
{
  "links" : [ ],
  "content" : [ {
    "@type" : "WorkItem",
    "id" : "1755ef1a-d6f0-4901-9ecd-d03352ae4a05",
    "version" : 1,
    "workItemNumber" : 1,
    "assignees" : [ {
      "principalId" : "tony@example.mycompany.com",
      "principalType" : "USER"
    } ],
    "tenantId" : "MYCOMPANY",
    "callbackEntityId" : "1",
    "workItemType" : {
      "id" : "com.mycompany.cafe.samples.travel.workItem",
      "name" : "Workspace Assignment",
      "pluralizedName" : "Workspace Assignments",
      "description" : "Location Specific Workspace Assignment",
      "serviceTypeId" : "com.mycompany.cafe.samples.travel.api",
      "actions" : [ {
        "id" : "com.mycompany.cafe.samples.travel.workItem.complete",
        "name" : "Reserve Workspace",
        "stateName" : "Completed",
        "icon" : {
          "id" : "baa623db-0ca0-4db7-af41-9a301bc9e152",
          "name" : "Complete Action Icon",
          "contentType" : "image/png",
          "image" : null
        }
      }, {
        "id" : "com.mycompany.cafe.samples.travel.workItem.cancel",
        "name" : "Workspace Unavailable",
        "stateName" : "Cancelled",
        "icon" : {
          "id" : "b03f994a-e1ec-4aae-8fae-e747ed680a5e",
          "name" : "Cancel Action Icon",
          "contentType" : "image/png",
          "image" : null
        }
      } ],
      "completeByEmail" : true,
      "commentsField" : null,
      "listView" : {
        "columns" : [ {
          "id" : "duration",
          "label" : "Duration",
          "description" : "The length of stay, measured in days.",
          "dataType" : {
            "type" : "primitive",
            "typeId" : "INTEGER"
          },
          "displayAdvice" : null,
          "state" : {
            "dependencies" : [ ],
            "facets" : [ ]
          },
          "filterable" : false,
          "sortable" : false,
          "isMultiValued" : false
        }, {
          "id" : "location",
          "label" : "Destination",
          "description" : "The destination to which travel is being requested.",
          "dataType" : {
            "type" : "ref",
            "componentTypeId" : null,
            "componentId" : null,
            "classId" : "location",
            "typeFilter" : null,
            "label" : null
          },
          "displayAdvice" : null,
          "state" : {
            "dependencies" : [ ],
            "facets" : [ ]
          },
          "filterable" : false,
          "sortable" : false,
          "isMultiValued" : false
        }, {
          "id" : "arrivalDate",
          "label" : "Arrival Date",
          "description" : "The date of arrival at the destination",
          "dataType" : {
            "type" : "primitive",
            "typeId" : "DATE_TIME"
          },
          "displayAdvice" : null,
          "state" : {
            "dependencies" : [ ],
            "facets" : [ ]
          },
          "filterable" : false,
          "sortable" : false,
          "isMultiValued" : false
        } ],
        "defaultSequence" : [ "location", "arrivalDate", "duration" ]
      },
      "version" : 3,
      "forms" : {
        "workItemDetails" : {
          "type" : "external",
          "formId" : "travel.seating.task"
        },
        "workItemSubmission" : {
          "type" : "external",
          "formId" : "travel.seating.task"
        },
        "workItemNotification" : {
          "type" : "external",
          "formId" : "travel.itinerary.details"
        }
      }
    },

            .
            .
            .


    "completedDate" : null,
    "assignedDate" : "2014-02-20T23:55:31.600Z",
    "createdDate" : "2014-02-20T23:55:31.600Z",
    "assignedOrCompletedDate" : "2014-02-20T23:55:31.600Z",
    "serviceId" : "2af18227-6a00-49e9-a76b-96de3ee767d2",
    "workItemRequest" : {
      "itemId" : "531660fd-b540-4946-9917-38c023b61c02",
      "itemName" : "test travel 1",
      "itemDescription" : "test travel 1",
      "itemRequestor" : "tony@example.mycompany.com",
      "itemCost" : 0.0,
      "itemData" : {
        "entries" : [ {
          "key" : "requestLeaseTotal",
          "value" : {
            "type" : "money",
            "currencyCode" : null,
            "amount" : 1065.0
          }
        }, {
          "key" : "approvalId",
          "value" : {
            "type" : "string",
            "value" : "7a8b6054-1922-4f82-9266-245dffaa957c"
          }
        }, {
          "key" : "requestClassId",
          "value" : {
            "type" : "string",
            "value" : "request"
          }
        }, {
          "key" : "requestedFor",
          "value" : {
            "type" : "string",
            "value" : "tony@example.mycompany.com"
          }
        }, {
          "key" : "requestReasons"
        }, {
          "key" : "requestedItemName",
          "value" : {
            "type" : "string",
            "value" : "test travel 1"
          }
        }, {
          "key" : "requestInstanceId",
          "value" : {
            "type" : "string",
            "value" : "1cfe7177-74e3-4d68-a559-ea17587022ca"
          }
        }, {
          "key" : "requestRef",
          "value" : {
            "type" : "string",
            "value" : "15"
          }
        }, {
          "key" : "requestedItemDescription",
          "value" : {
            "type" : "string",
            "value" : "test travel 1"
          }
        }, {
          "key" : "requestLeaseRate",
          "value" : {
            "type" : "moneyTimeRate",
            "cost" : {
              "type" : "money",
              "currencyCode" : null,
              "amount" : 213.0
            },
            "basis" : {
              "type" : "timeSpan",
              "unit" : "DAYS",
              "amount" : 1
            }
          }
        }, {
          "key" : "requestingServiceId",
          "value" : {
            "type" : "string",
            "value" : "f91d044a-04f9-4b96-8542-375e3e4e1dc1"
          }
        }, {
          "key" : "policy",
          "value" : {
            "type" : "string",
            "value" : "test travel approval policy"
          }
        }, {
          "key" : "phase",
          "value" : {
            "type" : "string",
            "value" : "Pre Approval"
          }
        }, {
          "key" : "requestDescription",
          "value" : {
            "type" : "string",
            "value" : "t"
          }
        }, {
          "key" : "requestLease",
          "value" : {
            "type" : "timeSpan",
            "unit" : "DAYS",
            "amount" : 5
          }
        }, {
          "key" : "requestedBy",
          "value" : {
            "type" : "string",
            "value" : "tony@example.mycompany.com"
          }
        } ]
      }
    },
    "status" : "Active",
    "availableActions" : [ ]
  } ],
  "metadata" : {
    "size" : 20,
    "totalElements" : 7,
    "totalPages" : 1,
    "number" : 1,
    "offset" : 0
  }
}

```

### Getting Work Item Details

GET /api/workitems/{id} retrieves the details of a pending work item. You need these details to submit a completion request. 

#### Input

Use the supported input parameters to control the command output. 

| Parameter  | Description  |
| :--- | :--- |
| URL  | https://$vRA/workitem-service/api/workitems/workitem\_ID |
| $vRA | Specifies the appliance name and fully qualified domain name, or IP address of the vRealize Automation server.  |
| $token | Specifies a valid HTTP bearer token with necessary credentials.  |
| workitem\_ID | Specifies the unique identifier of a work item. See [Syntax for Listing Work Items](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-91EC5E52-07CC-4F75-8301-2A27009E27B1.html#GUID-91EC5E52-07CC-4F75-8301-2A27009E27B1).  |

#### Output

The command output contains property names and values based on the command input parameters. 

<table>
  <thead>
    <tr>
      <th style="text-align:left">Property</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">Links</td>
      <td style="text-align:left">
        <p>Specifies an array of link objects, each of which contains the following
          parts:</p>
        <ul>
          <li>rel: Specifies the name of the link.
            <ul>
              <li>Self refers to the object that was returned or requested. This property
                does not exist when you query for a single profile.</li>
              <li>First, Previous, Next, and Last refer to corresponding pages of pageable
                lists.</li>
              <li>Specifies the application or service that determines the other names.</li>
            </ul>
          </li>
          <li>href: Specifies the URL that produces the result.</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">work itemNumber</td>
      <td style="text-align:left">Displays a reference number for the work item.</td>
    </tr>
    <tr>
      <td style="text-align:left">id</td>
      <td style="text-align:left">Specifies the unique identifier of this resource.</td>
    </tr>
    <tr>
      <td style="text-align:left">version</td>
      <td style="text-align:left">Displays the object version number.</td>
    </tr>
    <tr>
      <td style="text-align:left">assignees</td>
      <td style="text-align:left">Displays the list of work item assignees.</td>
    </tr>
    <tr>
      <td style="text-align:left">subTenantId</td>
      <td style="text-align:left">Optionally associates the work item with a specific business group granting
        users with management responsibilities over that business group permission
        to see the approval.</td>
    </tr>
    <tr>
      <td style="text-align:left">tenantId</td>
      <td style="text-align:left">Specifies the tenant ID for the work item.</td>
    </tr>
    <tr>
      <td style="text-align:left">callbackEntityId</td>
      <td style="text-align:left">Specifies the callback entity ID for the work item.</td>
    </tr>
    <tr>
      <td style="text-align:left">work itemType</td>
      <td style="text-align:left">Specifies the work item type for the work item.</td>
    </tr>
    <tr>
      <td style="text-align:left">completedDate</td>
      <td style="text-align:left">Specifies the date when the work item was completed.</td>
    </tr>
    <tr>
      <td style="text-align:left">assignedDate</td>
      <td style="text-align:left">Specifies the date when the work item was assigned.</td>
    </tr>
    <tr>
      <td style="text-align:left">createdDate</td>
      <td style="text-align:left">Specifies the created date of this instance.</td>
    </tr>
    <tr>
      <td style="text-align:left">assignedOrCompletedDate</td>
      <td style="text-align:left">Specifies the date to be displayed on UI.</td>
    </tr>
    <tr>
      <td style="text-align:left">formUrl</td>
      <td style="text-align:left">Specifies the URL from which the layout for this work item can be retrieved.</td>
    </tr>
    <tr>
      <td style="text-align:left">serviceId</td>
      <td style="text-align:left">Specifies the service ID that generated this work item instance.</td>
    </tr>
    <tr>
      <td style="text-align:left">work itemRequest</td>
      <td style="text-align:left">Specifies the corresponding work item request object.</td>
    </tr>
    <tr>
      <td style="text-align:left">status</td>
      <td style="text-align:left">Specifies the status of the work item.</td>
    </tr>
    <tr>
      <td style="text-align:left">completedBy</td>
      <td style="text-align:left">Specifies the principal ID of user who completed the work item.</td>
    </tr>
    <tr>
      <td style="text-align:left">availableActions</td>
      <td style="text-align:left">Contains a list of relevant work item actions.</td>
    </tr>
    <tr>
      <td style="text-align:left">Metadata</td>
      <td style="text-align:left">
        <p>Specifies the paging-related data:</p>
        <ul>
          <li>Size: Specifies the maximum number of rows per page.</li>
          <li>totalElement: Specifies the number of rows returned.</li>
          <li>totalPages: Specifies the total number of pages of data available.</li>
          <li>Number: Specifies the current page number.</li>
          <li>Offset: Specifies the number of rows skipped.</li>
        </ul>
      </td>
    </tr>
  </tbody>
</table>#### curl Command

The following example command retrieves the necessary details for the specified work item. 

```text
curl --insecure -H "Content-Type: application/json" 
-H "Authorization: Bearer $token” 
https://$vRA/workitem-service/api/workitems
```

#### JSON Output

The following JSON output is returned based on the command input. Note:

Price is referred to as `cost` in API commands and output. 

To view the contents of a JSON output file, for example workItemDetails.json, use the ! command with more in UNIX or type in Windows. 

* \(UNIX\) vcac-shell&gt;! more workItemDetails.json
* \(Windows\) vcac-shell&gt; ! CMD /C type workItemDetails.json 

```text
{
  "links" : [ ],
  "content" : [ {
    "@type" : "WorkItem",
    "id" : "1755ef1a-d6f0-4901-9ecd-d03352ae4a05",
    "version" : 1,
    "workItemNumber" : 1,
    "assignees" : [ {
      "principalId" : "tony@example.mycompany.com",
      "principalType" : "USER"
    } ],
    "tenantId" : "MYCOMPANY",
    "callbackEntityId" : "1",
    "workItemType" : {
      "id" : "com.mycompany.cafe.samples.travel.workItem",
      "name" : "Workspace Assignment",
      "pluralizedName" : "Workspace Assignments",
      "description" : "Location Specific Workspace Assignment",
      "serviceTypeId" : "com.mycompany.cafe.samples.travel.api",
      "actions" : [ {
        "id" : "com.mycompany.cafe.samples.travel.workItem.complete",
        "name" : "Reserve Workspace",
        "stateName" : "Completed",
        "icon" : {
          "id" : "baa623db-0ca0-4db7-af41-9a301bc9e152",
          "name" : "Complete Action Icon",
          "contentType" : "image/png",
          "image" : null
        }
      }, {
        "id" : "com.mycompany.cafe.samples.travel.workItem.cancel",
        "name" : "Workspace Unavailable",
        "stateName" : "Cancelled",
        "icon" : {
          "id" : "b03f994a-e1ec-4aae-8fae-e747ed680a5e",
          "name" : "Cancel Action Icon",
          "contentType" : "image/png",
          "image" : null
        }
      } ],
      "completeByEmail" : true,
      "commentsField" : null,
      "listView" : {
        "columns" : [ {
          "id" : "duration",
          "label" : "Duration",
          "description" : "The length of stay, measured in days.",
          "dataType" : {
            "type" : "primitive",
            "typeId" : "INTEGER"
          },
          "displayAdvice" : null,
          "state" : {
            "dependencies" : [ ],
            "facets" : [ ]
          },
          "filterable" : false,
          "sortable" : false,
          "isMultiValued" : false
        }, {
          "id" : "location",
          "label" : "Destination",
          "description" : "The destination to which travel is being requested.",
          "dataType" : {
            "type" : "ref",
            "componentTypeId" : null,
            "componentId" : null,
            "classId" : "location",
            "typeFilter" : null,
            "label" : null
          },
          "displayAdvice" : null,
          "state" : {
            "dependencies" : [ ],
            "facets" : [ ]
          },
          "filterable" : false,
          "sortable" : false,
          "isMultiValued" : false
        }, {
          "id" : "arrivalDate",
          "label" : "Arrival Date",
          "description" : "The date of arrival at the destination",
          "dataType" : {
            "type" : "primitive",
            "typeId" : "DATE_TIME"
          },
          "displayAdvice" : null,
          "state" : {
            "dependencies" : [ ],
            "facets" : [ ]
          },
          "filterable" : false,
          "sortable" : false,
          "isMultiValued" : false
        } ],
        "defaultSequence" : [ "location", "arrivalDate", "duration" ]
      },
      "version" : 3,
      "forms" : {
        "workItemDetails" : {
          "type" : "external",
          "formId" : "travel.seating.task"
        },
        "workItemSubmission" : {
          "type" : "external",
          "formId" : "travel.seating.task"
        },
        "workItemNotification" : {
          "type" : "external",
          "formId" : "travel.itinerary.details"
        }
      }
    },

            .
            .
            .


    "completedDate" : null,
    "assignedDate" : "2014-02-20T23:55:31.600Z",
    "createdDate" : "2014-02-20T23:55:31.600Z",
    "assignedOrCompletedDate" : "2014-02-20T23:55:31.600Z",
    "serviceId" : "2af18227-6a00-49e9-a76b-96de3ee767d2",
    "workItemRequest" : {
      "itemId" : "531660fd-b540-4946-9917-38c023b61c02",
      "itemName" : "test travel 1",
      "itemDescription" : "test travel 1",
      "itemRequestor" : "tony@example.mycompany.com",
      "itemCost" : 0.0,
      "itemData" : {
        "entries" : [ {
          "key" : "requestLeaseTotal",
          "value" : {
            "type" : "money",
            "currencyCode" : null,
            "amount" : 1065.0
          }
        }, {
          "key" : "approvalId",
          "value" : {
            "type" : "string",
            "value" : "7a8b6054-1922-4f82-9266-245dffaa957c"
          }
        }, {
          "key" : "requestClassId",
          "value" : {
            "type" : "string",
            "value" : "request"
          }
        }, {
          "key" : "requestedFor",
          "value" : {
            "type" : "string",
            "value" : "tony@example.mycompany.com"
          }
        }, {
          "key" : "requestReasons"
        }, {
          "key" : "requestedItemName",
          "value" : {
            "type" : "string",
            "value" : "test travel 1"
          }
        }, {
          "key" : "requestInstanceId",
          "value" : {
            "type" : "string",
            "value" : "1cfe7177-74e3-4d68-a559-ea17587022ca"
          }
        }, {
          "key" : "requestRef",
          "value" : {
            "type" : "string",
            "value" : "15"
          }
        }, {
          "key" : "requestedItemDescription",
          "value" : {
            "type" : "string",
            "value" : "test travel 1"
          }
        }, {
          "key" : "requestLeaseRate",
          "value" : {
            "type" : "moneyTimeRate",
            "cost" : {
              "type" : "money",
              "currencyCode" : null,
              "amount" : 213.0
            },
            "basis" : {
              "type" : "timeSpan",
              "unit" : "DAYS",
              "amount" : 1
            }
          }
        }, {
          "key" : "requestingServiceId",
          "value" : {
            "type" : "string",
            "value" : "f91d044a-04f9-4b96-8542-375e3e4e1dc1"
          }
        }, {
          "key" : "policy",
          "value" : {
            "type" : "string",
            "value" : "test travel approval policy"
          }
        }, {
          "key" : "phase",
          "value" : {
            "type" : "string",
            "value" : "Pre Approval"
          }
        }, {
          "key" : "requestDescription",
          "value" : {
            "type" : "string",
            "value" : "t"
          }
        }, {
          "key" : "requestLease",
          "value" : {
            "type" : "timeSpan",
            "unit" : "DAYS",
            "amount" : 5
          }
        }, {
          "key" : "requestedBy",
          "value" : {
            "type" : "string",
            "value" : "tony@example.mycompany.com"
          }
        } ]
      }
    },
    "status" : "Active",
    "availableActions" : [ ]
  } ],
  "metadata" : {
    "size" : 20,
    "totalElements" : 7,
    "totalPages" : 1,
    "number" : 1,
    "offset" : 0
  }
}
```

### Constructing a JSON File to Approve a Machine Request

You can specify a JSON file in your vRealize Automation REST API command line input. For example, when you enter a command to approve a machine request, you can include the name of a JSON file that contains all the parameters required to approve the request and complete the work item. 

#### Template JSON File Values

Copy the following template to start constructing a properly formatted JSON file in a text editor. Replace the highlighted values with your obtained work item details. After you create the JSON file, you can include it, or its contents, when you approve a submitted machine request. See [Syntax for Approving a Submitted Machine Request](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-85FAD2F3-7C91-41E3-A57F-DA30C1D70290.html#GUID-85FAD2F3-7C91-41E3-A57F-DA30C1D70290). 

```text
{
   "formData": {
      "entries": [
         {
            "key": "source-source-provider-Cafe.Shim.VirtualMachine.NumberOfInstances",
            "value": {
               "type": "integer",
               "value": 1
            }
         },
         {
            "key": "source-source-provider-VirtualMachine.Memory.Size",
            "value": {
               "type": "integer",
               "value": 512
            }
         },
         {
            "key": "source-source-provider-VirtualMachine.CPU.Count",
            "value": {
               "type": "integer",
               "value": 1
            }
         },
         {
            "key": "source-businessJustification",
            "value": {
               "type": "string",
               "value": "solves abx request"
            }
         },
         {
            "key": "source-source-provider-VirtualMachine.LeaseDays",
            "value": {
               "type": "integer",
               "value": 0
            }
         }
      ]
   },
   "workItemId": "5e3e9519-78ea-4409-a52c-e4aa3bc56511",
   "workItemActionId": "com.mycompany.csp.core.approval.action.approve"
}
```

Certain parameters are available to use in the JSON template. 

<table>
  <thead>
    <tr>
      <th style="text-align:left">JSON Template Value Table</th>
      <th style="text-align:left"></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">JSON File Parameter Name</td>
      <td style="text-align:left">Description of Value</td>
    </tr>
    <tr>
      <td style="text-align:left">workItemId</td>
      <td style="text-align:left">Specifies the value of the corresponding work item ID obtained from the
        work item list.</td>
    </tr>
    <tr>
      <td style="text-align:left">source-source-provider-Cafe.Shim.VirtualMachine.NumberOfInstances value</td>
      <td
      style="text-align:left">Specifies the number of instances requested.</td>
    </tr>
    <tr>
      <td style="text-align:left">source-source-provider-VirtualMachine.Memory.Size</td>
      <td style="text-align:left">Specifies the amount of memory requested in GB.</td>
    </tr>
    <tr>
      <td style="text-align:left">source-source-provider-VirtualMachine.CPU.Count</td>
      <td style="text-align:left">Specifies the number of CPUs requested.</td>
    </tr>
    <tr>
      <td style="text-align:left">source-businessJustification</td>
      <td style="text-align:left">Specifies the text description of reason for request.</td>
    </tr>
    <tr>
      <td style="text-align:left">source-source-provider-VirtualMachine.LeaseDays</td>
      <td style="text-align:left">Specifies the number of days to lease.</td>
    </tr>
    <tr>
      <td style="text-align:left">workItemActionId</td>
      <td style="text-align:left">
        <p>To approve a request, include the approve statement, for example com.mycompany.csp.core.approval.action.approve..</p>
        <p>To reject a request, include the reject statement, for example com.mycompany.csp.core.approval.action.reject.</p>
      </td>
    </tr>
  </tbody>
</table>#### JSON Input File

Use the following JSON input file sample when constructing a file. 

```text
{
    "@type": "CatalogItemRequest",
    "catalogItemRef": {
        "id": "65fbca06-a28e-46f3-bced-c6e5fb3a66f9"
    },
    "organization": {
        "tenantRef": "MYCOMPANY",
        "subtenantRef": "cccd7a7e-5283-416b-beb0-45eb4e924dcb"
    },
    "requestedFor": "fritz@example.mycompany.com",
    "state": "SUBMITTED",
    "requestNumber": 0,
    "requestData": {
        "entries": [{
            "key": "provider-blueprintId",
            "value": {
                "type": "string",
                "value": "e16edcf9-6a10-4bc7-98e2-a33361aeb857"
            }
        },
        {
            "key": "provider-provisioningGroupId",
            "value": {
                "type": "string",
                "value": "cccd7a7e-5283-416b-beb0-45eb4e924dcb"
            }
        },
        {
            "key": "requestedFor",
            "value": {
                "type": "string",
                "value": "fritz@example.mycompany.com"
            }
        },
        {
            "key": "provider-VirtualMachine.CPU.Count",
            "value": {
                "type": "integer",
                "value": 1
            }
        },
        {
            "key": "provider-VirtualMachine.Memory.Size",
            "value": {
                "type": "integer",
                "value": 1024
            }
        },
        {
            "key": "provider-VirtualMachine.LeaseDays",
            "value": {
                "type": "integer",
                "value": 30
            }
        },
        {
            "key": "provider-__Notes",
            "value": {
                "type": "string",
                "value": "MYCOMPANY machine"
            }
        },
        {
            "key": "provider-VirtualMachine.Disk0.Size",
            "value": {
                "type": "string",
                "value": "1"
            }
        },
        {
            "key": "provider-VirtualMachine.Disk0.Letter",
            "value": {
                "type": "string",
                "value": "C"
            }
        },
        {
            "key": "provider-VirtualMachine.Disk0.Label",
            "value": {
                "type": "string",
                "value": "main"
            }
        }]
    }
}
```

### Approving a Submitted Machine Request

PUT /api/workitems/{id} approves a submitted work item request to complete the request. To construct the approval command, you add work item and work item form details to a JSON file, and call that JSON file from the command line. Use a template to correctly format the JSON file content. 

#### Input

Use the supported input parameters to control the command output. 

| Parameter  | Description  |
| :--- | :--- |
| URL  | https://$vRA/workitem-service/api/workitems/workitem\_ID |
| $vRA | Specifies the appliance name and fully qualified domain name, or IP address of the vRealize Automation server.  |
| $token | Specifies a valid HTTP bearer token with necessary credentials.  |
| workitem\_ID | Specifies the unique identifier of a work item. See [Syntax for Listing Work Items](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-91EC5E52-07CC-4F75-8301-2A27009E27B1.html#GUID-91EC5E52-07CC-4F75-8301-2A27009E27B1).  |

#### Output

The command output contains property names and values based on the command input parameters. 

<table>
  <thead>
    <tr>
      <th style="text-align:left">Property</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">Links</td>
      <td style="text-align:left">
        <p>Specifies an array of link objects, each of which contains the following
          parts:</p>
        <ul>
          <li>rel: Specifies the name of the link.
            <ul>
              <li>Self refers to the object that was returned or requested. This property
                does not exist when you query for a single profile.</li>
              <li>First, Previous, Next, and Last refer to corresponding pages of pageable
                lists.</li>
              <li>Specifies the application or service that determines the other names.</li>
            </ul>
          </li>
          <li>href: Specifies the URL that produces the result.</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">work itemNumber</td>
      <td style="text-align:left">Displays a reference number for the work item.</td>
    </tr>
    <tr>
      <td style="text-align:left">id</td>
      <td style="text-align:left">Specifies the unique identifier of this resource.</td>
    </tr>
    <tr>
      <td style="text-align:left">version</td>
      <td style="text-align:left">Displays the object version number.</td>
    </tr>
    <tr>
      <td style="text-align:left">assignees</td>
      <td style="text-align:left">Displays the list of work item assignees.</td>
    </tr>
    <tr>
      <td style="text-align:left">subTenantId</td>
      <td style="text-align:left">Optionally associates the work item with a specific business group granting
        users with management responsibilities over that business group permission
        to see the approval.</td>
    </tr>
    <tr>
      <td style="text-align:left">tenantId</td>
      <td style="text-align:left">Specifies the tenant ID for the work item.</td>
    </tr>
    <tr>
      <td style="text-align:left">callbackEntityId</td>
      <td style="text-align:left">Specifies the callback entity ID for the work item.</td>
    </tr>
    <tr>
      <td style="text-align:left">work itemType</td>
      <td style="text-align:left">Specifies the work item type for the work item.</td>
    </tr>
    <tr>
      <td style="text-align:left">completedDate</td>
      <td style="text-align:left">Specifies the date when the work item was completed.</td>
    </tr>
    <tr>
      <td style="text-align:left">assignedDate</td>
      <td style="text-align:left">Specifies the date when the work item was assigned.</td>
    </tr>
    <tr>
      <td style="text-align:left">createdDate</td>
      <td style="text-align:left">Specifies the created date of this instance.</td>
    </tr>
    <tr>
      <td style="text-align:left">assignedOrCompletedDate</td>
      <td style="text-align:left">Specifies the date to be displayed on UI.</td>
    </tr>
    <tr>
      <td style="text-align:left">formUrl</td>
      <td style="text-align:left">Specifies the URL from which the layout for this work item can be retrieved.</td>
    </tr>
    <tr>
      <td style="text-align:left">serviceId</td>
      <td style="text-align:left">Specifies the service ID that generated this work item instance.</td>
    </tr>
    <tr>
      <td style="text-align:left">work itemRequest</td>
      <td style="text-align:left">Specifies the corresponding work item request object.</td>
    </tr>
    <tr>
      <td style="text-align:left">status</td>
      <td style="text-align:left">Specifies the status of the work item.</td>
    </tr>
    <tr>
      <td style="text-align:left">completedBy</td>
      <td style="text-align:left">Specifies the principal ID of user who completed the work item.</td>
    </tr>
    <tr>
      <td style="text-align:left">availableActions</td>
      <td style="text-align:left">Contains a list of relevant work item actions.</td>
    </tr>
    <tr>
      <td style="text-align:left">Metadata</td>
      <td style="text-align:left">
        <p>Specifies the paging-related data:</p>
        <ul>
          <li>Size: Specifies the maximum number of rows per page.</li>
          <li>totalElement: Specifies the number of rows returned.</li>
          <li>totalPages: Specifies the total number of pages of data available.</li>
          <li>Number: Specifies the current page number.</li>
          <li>Offset: Specifies the number of rows skipped.</li>
        </ul>
      </td>
    </tr>
  </tbody>
</table>#### Example: curl Command

Approve a submitted machine request by specifying its work item ID and using a JSON file named approve.json to pass arguments to the command line. 

```text
curl -X PUT --insecure -H "Content-Type: application/json" 
-H "Authorization: Bearer $token" 
https://$vRA/workitem-service/api/workitems/5e3e9519-78ea-4409-
a52c-e4aa3bc56511/actions/com.mycompany.csp.core.approval.action.approve 
--d @approve.json
```

#### Error Conditions

If the same request is submitted a second time, the following error response is received: 

```text
Command failed [Rest Error]: {Status code: 400}, {Error code: 12005} ,
{Error Source: null}, {Error Msg: Work item 5e3e9519-78ea-4409-a52c-e4aa3bc56511
is in COMPLETED state. Requested operation cannot be performed.}, {System Msg:
Work item 5e3e9519-78ea-4409-a52c-e4aa3bc56511 is in COMPLETED state. Requested
operation cannot be performed.}
```

If a user who is not authorized to approve the request submits the request, the following error response is received: 

```text
Command failed [Rest Error]: {Status code: 400}, {Error code: 12017} ,
{Error Source: null}, {Error Msg: User fritz@example.mycompany.com not authorized to
complete work item with ID 5e3e9519-78ea-4409-a52c-e4aa3bc56511.}, {System Msg:
User fritz@example.mycompany.com not authorized to complete Work item with id
5e3e9519-78ea-4409-a52c-e4aa3bc56511.}
```

### Updating Price Information

POST /api/blueprints/{id}/costs/upfront of the composition service, updates and displays price information for a deployment. The price of a deployment is based on which blueprint you request plus details of the specific request. For example, if the blueprint allows for a range of CPU, memory, or storage values, the price depends on the value requested. 

#### Input

Use the supported input parameters to control the command output. Note:

Price is referred to as `cost` in API commands and output. 

<table>
  <thead>
    <tr>
      <th style="text-align:left">Parameter</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">URL</td>
      <td style="text-align:left">https://$vRA/composition-service/api/blueprints/$BlueprintId/costs/upfront</td>
    </tr>
    <tr>
      <td style="text-align:left">Method</td>
      <td style="text-align:left">Post</td>
    </tr>
    <tr>
      <td style="text-align:left">$vRA</td>
      <td style="text-align:left">Specifies the appliance name and fully qualified domain name, or IP address
        of the vRealize Automation server.</td>
    </tr>
    <tr>
      <td style="text-align:left">$token</td>
      <td style="text-align:left">Specifies a valid HTTP bearer token with necessary credentials.</td>
    </tr>
    <tr>
      <td style="text-align:left">HTTP Body</td>
      <td style="text-align:left">
        <p>Specifies the blueprint ID for the blueprint for which you are requesting
          price information and other information.</p>
        <ul>
          <li><code>Blueprint ID</code>: Specifies the blueprint ID.</li>
          <li><code>requestedFor</code>: The user for whom this request is being made.
            Must be the fully qualified user ID.</li>
          <li><code>subTenantId</code>: Specifies the subtenant ID associated with the
            blueprint</li>
          <li><code>requestData</code>: Specifies data that identifies the blueprint
            further.
            <ul>
              <li><code>entries</code>
                <ul>
                  <li><code>Key</code>: The name of the machine on which the blueprint resides.</li>
                  <li><code>value</code>: Specifies key-value pairs that further identify the
                    blueprint, such as the type of the value, the <code>componentType ID</code> for
                    the item, the <code>classID</code> of the value, and where the blueprint
                    resides. In turn, each entry contains an array of key-value pairs that
                    identify the type of data used to compute the price that is to be displayed.
                    <ul>
                      <li><code>Values</code>: Specifies an array of type filters.</li>
                      <li><code>Entries</code>: Specifies a list of key-value pairs that specify
                        the values to be used in computing the price. For example, the cluster,
                        CPU, and allocated memory to use.</li>
                    </ul>
                  </li>
                </ul>
              </li>
            </ul>
          </li>
        </ul>
      </td>
    </tr>
  </tbody>
</table>#### Output

The command output contains property names and values based on the command input parameters. 

| Property  | Description  |
| :--- | :--- |
| setupFee  | Specifies the one time setup fee associated with the component.  |
| totalEstimatedLeasePriceInfo  | Specifies the minimum price and maximum price for the lease period.  |
| averageDailyPriceInfo  | Specifies the average daily price, which depends on the reservation available for the component.  |
| count  | Specifies the instance count of the component.  |
| memory  | Specifies memory requested for this component.  |
| additional  | Specifies the additional price, if any, associated with the component.  |
| cpu  | Specifies the cpu requested for the component.  |
| storage  | Specifies the storage requested for the component.  |
| componentId  | Specifies the component ID, or total price of the deployment.  |

#### Example: `curl` Command

The following sample command updates and displays the price of a sample blueprint with one node. The HTTP body is included as part of the command line input. Note:

Price is referred to as `cost` in API commands and output. 

```text
curl -- insecure -H “Content Type: application/json”
-H "Authorization: Bearer $token" 
https://$vRA/composition-service/api/blueprints/$BlueprintId/costs/upfront"

{
  "blueprintId": "myblueprintId",
  "requestedFor": "fritz@coke.sqa-horizon.local",
  "subTenantId": "7a961949-13c4-4f3d-9010-66db8da6c51e",
  "requestData": {
    "entries": [
      {
        "key": "vSphere_Machine_1",
        "value": {
          "type": "complex",
          "componentTypeId": "com.vmware.csp.iaas.blueprint.service",
          "classId": "Blueprint.Node",
          "typeFilter": "phanisimple*vSphere_Machine_1",
          "values": {
            "entries": [
              {
                "key": "_cluster",
                "value": {
                  "type": "integer",
                  "value": 3
                }
              },
              {
                "key": "cpu",
                "value": {
                  "type": "integer",
                  "value": 2
                }
              },
              {
                "key": "memory",
                "value": {
                  "type": "integer",
                  "value": 2048
                }
              }
            ]
          }
        }
      }
    ]
  }
}
```

#### Example: JSON Output for a Blueprint Price Update

```text
[{"componentId":"vSphere_Machine_1",
"setupFee":"$0.00",
"totalEstimatedLeasePriceInfo":{"min":50.0543225806451601,"max":50.0543225806451601,"displayString":"$50.05"},
"averageDailyPriceInfo":{"min":16.6847741935483867,"max":16.6847741935483867,"displayString":"$16.68"},
"count":3
"fieldMap":{"setup_fee":{"min":0,"max":0,"displayString":"$0.00"},
"memory":{"min":8.00,"max":8.00,"displayString":"$8.00"},
"additional":{"min":8.6847741935483867,"max":8.6847741935483867,"displayString":"$8.68"},
"cpu":{"min":0.0,"max":0.0,"displayString":"$0.00"},
"storage":{"min":0,"max":0,"displayString":"$0.00"}}},
{"componentId":"Total","setupFee":"","totalEstimatedLeasePriceInfo":
{"min":50.0543225806451601,"max":50.0543225806451601,"displayString":"$50.05"},
"averageDailyPriceInfo":{"min":16.6847741935483867,"max":16.6847741935483867,"displayString":"$16.68"},
"count":3,"fieldMap":{}}]
```

