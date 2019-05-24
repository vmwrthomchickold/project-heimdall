# Listing Provisioned Resources

You use the catalog service to list provisioned resources. 

The catalog service is designed to be used by consumers and providers of the service catalog. For example, a consumer might want to list resources provisioned by a provider. The consumer can also list the resources in multiple ways. 

Each example for this use case lists a curl command with respective JSON response, plus input and output parameters. The same set of prerequisites applies to each example.

## Prerequisites

Satisfy the following conditions before performing any tasks for this use case. 

* Log in to vRealize Automation as a business group manager. 
* Verify that the appliance name and fully qualified domain name of the vRealize Automation instance are available. 
* Verify that you have a valid HTTP bearer token that matches your login credentials. See [REST API Authentication](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-05B9796E-1C20-4860-B77B-1E7F4BD7E556.html#GUID-05B9796E-1C20-4860-B77B-1E7F4BD7E556).

## Examples

### Display Your Provisioned Resources 

GET /api/consumer/resources/{id} displays a list of all the provisioned resources that you own. 

#### **curl Command**

The following example displays all applicable provisioned resources. 

```text
curl --insecure -H "Content-Type: application/json" 
-H "Authorization: Bearer $token" 
https://$vRA/catalog-service/api/consumer/resources/?page=1&limit=n&$orderby=name
```

#### **JSON Output**

The following JSON output is returned based on the command input. 

```text
{
  "links" : [ {
    "@type" : "link",
    "rel" : "next",
    "href" : "https://vra152-009-067.mycompany.com/catalog-service/api/consumer/resources/?page=2&limit=1"
  } ],
  "content" : [ {
    "@type" : "ConsumerResource",
    "id" : "c24e8c75-c201-489c-b51c-8d7009c23563",
    "iconId" : "Travel_100.png",
    "resourceTypeRef" : {
      "id" : "com.mycompany.mystuff.samples.travel.packageType",
      "label" : "Reservation"
    },
    "name" : "example",
    "description" : "asd",
    "status" : "ACTIVE",
    "catalogResource" : {
      "id" : "6fddafcd-bc3d-4753-8a2a-5fa3f78a5a90",
      "label" : "example"
    },
    "requestId" : "55e7fcf3-4c77-4b11-a442-1f282333ac91",
    "providerBinding" : {
      "bindingId" : "1",
      "providerRef" : {
        "id" : "f60f5d1e-d6e9-4d98-9c48-f70a3e405346",
        "label" : "travel-service"
      }
    },
…
}
```

#### **Input**

Use the supported input parameters to control the command output. 

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
      <td style="text-align:left">https://$vRA/catalog-service/api/consumer/resources/</td>
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
      <td style="text-align:left">$page</td>
      <td style="text-align:left">Specifies a page number.</td>
    </tr>
    <tr>
      <td style="text-align:left">$limit</td>
      <td style="text-align:left">
        <p>Specifies the number of entries to display on a page. Maximum value is
          5000. If not specified, defaults to 20.</p>
        <p>For information regarding limits to the number of elements displayed,
          see <a href="https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-53D67A18-7FCE-436A-9FB6-6559CAF59386.html#GUID-53D67A18-7FCE-436A-9FB6-6559CAF59386__example_FFE1B9414EE14A63A443D618DB1224C5">Retrieve 10,000 Resources Ordered By Name</a>.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">$orderby</td>
      <td style="text-align:left">
        <p>Specifies how to order multiple comma-separated properties sorted in ascending
          or descending order. Values include:</p>
        <ul>
          <li>$orderby=id</li>
          <li>$orderby=name</li>
          <li>$orderby=dateCreated</li>
          <li>$orderby=lastUpdated</li>
          <li>$orderby=status</li>
          <li>$orderby=description</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">$top</td>
      <td style="text-align:left">Specifies the number of returned entries from the top of the response
        (total number per page in relation to skip).</td>
    </tr>
    <tr>
      <td style="text-align:left">$skip</td>
      <td style="text-align:left">Specifies the number of entries to skip.</td>
    </tr>
  </tbody>
</table>#### **Output**

The command output contains property names and values based on the command input parameters. 

| Property  | Description  |
| :--- | :--- |
| id  | Specifies the unique identifier of this resource.  |
| iconId  | Specifies an icon for this request based on the requested object type.  |
| resourceTypeRef  | Specifies the resource type.  |
| name  | Specifies the resource name.  |
| description  | Specifies the resource description.  |
| status  | Specifies the resource status.  |
| catalogItem  | Specifies the catalog item that defines the service this resource is based on.  |
| requestId  | Specifies the request ID that provisioned this resource.  |
| providerBinding  | Specifies the provider binding.  |
| owners  | Species the owners of this resource.  |
| organization  | Specifies the subtenant or tenant that owns this resource.  |
| dateCreated  | Specifies the data and time at which the resource was created.  |
| lastUpdated  | Specifies the date and time at which the resource was most recently modified.  |
| hasLease  | Returns true if the resource is subject to a lease.  |
| lease  | Displays the resource's current lease as start and end time stamps.  |
| leaseForDisplay  | Specifies the resource's current lease, \#getLease, with time units synchronized with \#getCosts.  |
| hasCosts  | Returns true if the resource is subject to per-time price.  |
| costs  | Displays an optional rate of the price charges for the resource. This parameter is deprecated.  |
| costToDate  | Displays an optional rate of the current price charges for the resource. This parameter is deprecated.  |
| totalCost  | Displays an optional rate of the price charges for the entire lease period. This parameter is deprecated.  |
| expenseMonthToDate  | The expense of the resource from the beginning of the month to the current date. This value is updated daily by vRealize Business for Cloud.  |
| parentResourceRef  | Displays the parent of this resource.  |
| childResources  | Displays the children of this resource.  |
| operations  | Specifies the sequence of available operations that can be performed on this resource.  |
| forms  | Specifies the forms used to render this resource.  |
| resourceData  | Displays the extended provider-defined properties of the resource.  |

#### **Retrieve 10,000 Resources Ordered By Name**

Since the catalog service limits the number of elements that can be retrieved with a single API call to 5000, retrieving 10,000 resources requires two calls. The first call displays the first 5000 elements and the second call displays the second 5000 elements. You can make the two calls by specifying either the page and limit values or the skip and top values. 

Specifying page and limit values, you make the following two calls. 

```text
curl --insecure -H "Content-Type: application/json" -H "Accept: application/json" 
   -H "Authorization: $AUTH" "https://$vRA/catalog-service/api/consumer/resources/?page=1&limit=5000&$orderby=name"
curl --insecure -H "Content-Type: application/json" -H "Accept: application/json" 
   -H "Authorization: $AUTH" "https://$vRA/catalog-service/api/consumer/resources/?page=2&limit=5000&$orderby=name"
```

Specifying skip and top values, you make the following two calls. 

```text
curl --insecure -H "Content-Type: application/json" -H "Accept: application/json" 
   -H "Authorization: $AUTH"  "https://$vRA/catalog-service/api/consumer/resources/?$skip=0&$top=5000&$orderby=name"
curl --insecure -H "Content-Type: application/json" -H "Accept: application/json" 
   -H "Authorization: $AUTH"  "https://$vRA/catalog-service/api/consumer/resources/?$skip=5000&$top=5000&$orderby=name"
```

If both page and limit values and skip and top values are specified, the skip and top values take priority.

### Displaying Provisioned Resources by Resource Type 

GET /api/consumer/resourcesTypes/{id} displays a list of the provisioned resources that you own filtered by machine resource type. 

#### curl Command

The following example displays the provisioned resources by resource type. 

```text
curl --insecure -H "Content-Type: application/json" 
-H "Authorization: Bearer $token” 
https://$vRA/catalog-service/api/consumer/resourceTypes/Infrastructure.Machine/?page=1&limit=n&$orderby=id
```

#### JSON Output

The following JSON output is returned based on the command input. 

```text
{
  "links" : [ ],
  "content" : [ {
    "@type" : "ConsumerResource",
    "id" : "3bfde906-81b9-44c3-8c2d-07d2c9768168",
    "iconId" : "cafe_default_icon_genericCatalogResource",
    "resourceTypeRef" : {
      "id" : "Infrastructure.Virtual",
      "label" : "Virtual Machine"
    },
    "name" : "test2",
    "description" : null,
    "status" : "ACTIVE",
    "catalogResource" : {
      "id" : "e2f397be-72ad-4ec4-a688-c017560fa1a3",
      "label" : "test-blueprint"
    },
    "requestId" : "b013d2fa-4ba4-416c-b46b-98bb8cc7b076",
    "providerBinding" : {
      "bindingId" : "8a4581a0-84f9-4e80-9af6-75d79633e382",
      "providerRef" : {
        "id" : "6918cd49-b737-467f-94bf-d14d52c78fba",
        "label" : "iaas-service"
      }
    },
    "owners" : [ {
      "tenantName" : "MYCOMPANY",
      "ref" : "fritz@example.mycompany.com",
      "type" : "USER",
      "value" : "Fritz Arbeiter"
    } ],
    "organization" : {
      "tenantRef" : "MYCOMPANY",
      "tenantLabel" : "QETenant",
      "subtenantRef" : "eab762cb-6e75-4379-83ef-171a71c9f00e",
      "subtenantLabel" : "MyTestAgentBusinessGroup"
    },
…
}
```

The output includes the following highlighted items: 

* Resource ID. 3bfde906-81b9-44c3-8c2d-07d2c9768168 corresponds to a provisioned machine owned by the logged-in user. The resource IDs are used in requests to retrieve the details for the corresponding machines. 
* subtenantRef ID. eab762cb-6e75-4379-83ef-171a71c9f00e corresponds to the business group of the logged-in user. If the user who is logged-in is also the manager of the business group, the subtenantRef ID is used to get resources from all business groups that the user manages. 

#### Input

Use the supported input parameters to control the command output. 

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
      <td style="text-align:left">https://$vRA/catalog-service/api/consumer/resourceTypes</td>
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
      <td style="text-align:left">page</td>
      <td style="text-align:left">Specifies a page number.</td>
    </tr>
    <tr>
      <td style="text-align:left">limit</td>
      <td style="text-align:left">
        <p>Specifies the number of entries to display on a page. Maximum value is
          5000. If not specified, defaults to 20.</p>
        <p>For information regarding limits to the number of elements displayed,
          see <a href="https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-53D67A18-7FCE-436A-9FB6-6559CAF59386.html#GUID-53D67A18-7FCE-436A-9FB6-6559CAF59386__example_FFE1B9414EE14A63A443D618DB1224C5">Retrieve 10,000 Resources Ordered By Name</a>.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">$orderby</td>
      <td style="text-align:left">
        <p>Specifies how to order multiple comma-separated properties sorted in ascending
          or descending order. Values include:</p>
        <ul>
          <li>$orderby=id</li>
          <li>$orderby=name</li>
          <li>$orderby=dateCreated</li>
          <li>$orderby=lastUpdated</li>
          <li>$orderby=status</li>
          <li>$orderby=description</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">top</td>
      <td style="text-align:left">Specifies the number of returned entries from the top of the response
        (total number per page in relation to skip).</td>
    </tr>
    <tr>
      <td style="text-align:left">skip</td>
      <td style="text-align:left">Specifies the number of entries to skip.</td>
    </tr>
  </tbody>
</table>Filter by the following resource types: 

* Infrastructure.Machine 
* Infrastructure.AppServic 
* Infrastructure.Cloud 
* Infrastructure.Physical 
* Infrastructure.vApp 
* Infrastructure.Virtual 

#### Output

The command output contains property names and values based on the command input parameters. 

| Property  | Description  |
| :--- | :--- |
| id  | Specifies the unique identifier of this resource.  |
| iconId  | Specifies an icon for this request based on the requested object type.  |
| resourceTypeRef  | Specifies the resource type.  |
| name  | Specifies the resource name.  |
| description  | Specifies the resource description.  |
| status  | Specifies the resource status.  |
| catalogItem  | Specifies the catalog item that defines the service this resource is based on.  |
| requestId  | Specifies the request ID that provisioned this resource.  |
| providerBinding  | Specifies the provider binding.  |
| owners  | Species the owners of this resource.  |
| organization  | Specifies the subtenant or tenant that owns this resource.  |
| dateCreated  | Specifies the data and time at which the resource was created.  |
| lastUpdated  | Specifies the date and time at which the resource was most recently modified.  |
| hasLease  | Returns true if the resource is subject to a lease.  |
| lease  | Displays the resource's current lease as start and end time stamps.  |
| leaseForDisplay  | Specifies the resource's current lease, \#getLease, with time units synchronized with \#getCosts.  |
| hasCosts  | Returns true if the resource is subject to per-time price.  |
| costs  | Displays an optional rate of the price charges for the resource. This parameter is deprecated.  |
| costToDate  | Displays an optional rate of the current price charges for the resource. This parameter is deprecated.  |
| totalCost  | Displays an optional rate of the price charges for the entire lease period. This parameter is deprecated.  |
| expenseMonthToDate  | The expense of the resource from the beginning of the month to the current date. This value is updated daily by vRealize Business for Cloud.  |
| parentResourceRef  | Displays the parent of this resource.  |
| childResources  | Displays the children of this resource.  |
| operations  | Specifies the sequence of available operations that can be performed on this resource.  |
| forms  | Specifies the forms used to render this resource.  |
| resourceData  | Displays the extended provider-defined properties of the resource. |

### Displaying All Available Resource Types

GET /api/consumer/resourcesTypes displays all the resource types that are available on the system. 

#### curl Command

The following example displays all available resource types. 

```text

```

#### JSON Output

The following JSON output is returned based on the command input. 

```text

```

#### Input

Use the supported input parameters to control the command output. 

| Parameter  | Description  |
| :--- | :--- |
| URL  | https://$vRA/catalog-service/api/consumer/resourceTypes  |
| $vRA | Specifies the appliance name and fully qualified domain name, or IP address of the vRealize Automation server.  |
| $token | Specifies a valid HTTP bearer token with necessary credentials.  |

#### Output

The command output contains property names and values based on the command input parameters. 

| Property  | Description  |
| :--- | :--- |
| id  | Specifies the unique identifier of this resource.  |
| iconId  | Specifies an icon for this request based on the requested object type.  |
| resourceTypeRef  | Specifies the resource type.  |
| name  | Specifies the resource name.  |
| description  | Specifies the resource description.  |
| status  | Specifies the resource status.  |
| catalogItem  | Specifies the catalog item that defines the service this resource is based on.  |
| requestId  | Specifies the request ID that provisioned this resource.  |
| providerBinding  | Specifies the provider binding.  |
| owners  | Species the owners of this resource.  |
| organization  | Specifies the subtenant or tenant that owns this resource.  |
| dateCreated  | Specifies the data and time at which the resource was created.  |
| lastUpdated  | Specifies the date and time at which the resource was most recently modified.  |
| hasLease  | Returns true if the resource is subject to a lease.  |
| lease  | Displays the resource's current lease as start and end time stamps.  |
| leaseForDisplay  | Specifies the resource's current lease, \#getLease, with time units synchronized with \#getCosts.  |
| hasCosts  | Returns true if the resource is subject to per-time price.  |
| costs  | Displays an optional rate of the price charges for the resource. This parameter is deprecated.  |
| costToDate  | Displays an optional rate of the current price charges for the resource. This parameter is deprecated.  |
| totalCost  | Displays an optional rate of the price charges for the entire lease period. This parameter is deprecated.  |
| expenseMonthToDate  | The expense of the resource from the beginning of the month to the current date. This value is updated daily by vRealize Business for Cloud.  |
| parentResourceRef  | Displays the parent of this resource.  |
| childResources  | Displays the children of this resource.  |
| operations  | Specifies the sequence of available operations that can be performed on this resource.  |
| forms  | Specifies the forms used to render this resource.  |
| resourceData  | Displays the extended provider-defined properties of the resource.  |

#### curl Command

The following example command displays all available resource types. 

```text
curl --insecure -H "Content-Type: application/json" 
-H "Authorization: Bearer $token" 
https://$vRA/catalog-service/api/consumer/resourceTypes
```

#### JSON Output

The following JSON output is returned based on the command input. 

```text
{
  "links" : [ ],
  "content" : [ {
    "@type" : "ResourceType",
    "id" : "Infrastructure.Machine",
    "name" : "Machine",
    "pluralizedName" : "Machines",
    "description" : "The common parent type for all types of machines",
    "primary" : true,
    "schema" : {
      "classId" : "Infrastructure.Machine.Schema",
      "typeFilter" : null
    },
    "forms" : {
      "catalogResourceInfoHidden" : true,
      "details" : {
        "type" : "extension",
        "extensionId" : "csp.places.iaas.resource.details",
        "extensionPointId" : null
      }
```

### Displaying Provisioned Resources by Business Groups You Manage

| Property  | Description  |
| :--- | :--- |
| id  | Specifies the unique identifier of this resource.  |
| iconId  | Specifies an icon for this request based on the requested object type.  |
| resourceTypeRef  | Specifies the resource type.  |
| name  | Specifies the resource name.  |
| description  | Specifies the resource description.  |
| status  | Specifies the resource status.  |
| catalogItem  | Specifies the catalog item that defines the service this resource is based on.  |
| requestId  | Specifies the request ID that provisioned this resource.  |
| providerBinding  | Specifies the provider binding.  |
| owners  | Species the owners of this resource.  |
| organization  | Specifies the subtenant or tenant that owns this resource.  |
| dateCreated  | Specifies the data and time at which the resource was created.  |
| lastUpdated  | Specifies the date and time at which the resource was most recently modified.  |
| hasLease  | Returns true if the resource is subject to a lease.  |
| lease  | Displays the resource's current lease as start and end time stamps.  |
| leaseForDisplay  | Specifies the resource's current lease, \#getLease, with time units synchronized with \#getCosts.  |
| hasCosts  | Returns true if the resource is subject to per-time price.  |
| costs  | Displays an optional rate of the price charges for the resource. This parameter is deprecated.  |
| costToDate  | Displays an optional rate of the current price charges for the resource. This parameter is deprecated.  |
| totalCost  | Displays an optional rate of the price charges for the entire lease period. This parameter is deprecated.  |
| expenseMonthToDate  | The expense of the resource from the beginning of the month to the current date. This value is updated daily by vRealize Business for Cloud.  |
| parentResourceRef  | Displays the parent of this resource.  |
| childResources  | Displays the children of this resource.  |
| operations  | Specifies the sequence of available operations that can be performed on this resource.  |
| forms  | Specifies the forms used to render this resource.  |
| resourceData  | Displays the extended provider-defined properties of the resource. |

The command output contains property names and values based on the command input parameters. 

#### Output

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
      <td style="text-align:left">https://$vRA/catalog-service/api/consumer/resources/type</td>
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
      <td style="text-align:left">$resourceID</td>
      <td style="text-align:left">Specifies a resource ID. See <a href="https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-53D67A18-7FCE-436A-9FB6-6559CAF59386.html#GUID-53D67A18-7FCE-436A-9FB6-6559CAF59386">Display Your Provisioned Resources Example</a> to
        view all of your requests and search for a request ID.</td>
    </tr>
    <tr>
      <td style="text-align:left">managedOnly</td>
      <td style="text-align:left">If true, the returned requests are from the user&apos;s managed subtenants.</td>
    </tr>
    <tr>
      <td style="text-align:left">page</td>
      <td style="text-align:left">Specifies a page number.</td>
    </tr>
    <tr>
      <td style="text-align:left">limit</td>
      <td style="text-align:left">
        <p>Specifies the number of entries to display on a page. Maximum value is
          5000. If not specified, defaults to 20.</p>
        <p>For information regarding limits to the number of elements displayed,
          see <a href="https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-53D67A18-7FCE-436A-9FB6-6559CAF59386.html#GUID-53D67A18-7FCE-436A-9FB6-6559CAF59386__example_FFE1B9414EE14A63A443D618DB1224C5">Retrieve 10,000 Resources Ordered By Name</a>.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">$orderby</td>
      <td style="text-align:left">
        <p>Specifies how to order multiple comma-separated properties sorted in ascending
          or descending order. Values include:</p>
        <ul>
          <li>$orderby=id</li>
          <li>$orderby=name</li>
          <li>$orderby=dateCreated</li>
          <li>$orderby=lastUpdated</li>
          <li>$orderby=status</li>
          <li>$orderby=description</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">top</td>
      <td style="text-align:left">Specifies the number of returned entries from the top of the response
        (total number per page in relation to skip).</td>
    </tr>
    <tr>
      <td style="text-align:left">skip</td>
      <td style="text-align:left">Specifies the number of entries to skip.</td>
    </tr>
    <tr>
      <td style="text-align:left">$filter</td>
      <td style="text-align:left">Contains a Boolean expression to determine if a particular entry is included
        in the response.</td>
    </tr>
  </tbody>
</table>Use the supported input parameters to control the command output. 

#### Input

```text
curl --insecure -H "Content-Type: application/json" 
-H "Authorization: Bearer $token" 
https://$vRA/catalog-service/api/consumer/resources/types/Infrastructure.Machine/?page=1&limit=n&$orderby=id desc&$filter=((organization/subTenant/id eq 'subtenantID_group1') or (organization/subTenant/id eq ''subtenantID_group2') … )"
```

The following JSON output is returned based on the command input. 

```text
rest get catalog-service --u  "consumer/resources/types/Infrastructure.Machine/?page=1&limit=2&$orderby=dateCreated desc&$filter=((organization/subTenant/id eq 'eab762cb-6e75-4379-83ef-171a71c9f00e') or (organization/subTenant/id eq 'fa995528-e289-455e-a0e6-c2da8b0e1bf9') or (organization/subTenant/id eq '699efe66-fe6e-4e34-96e8-52a34f338d20') or (organization/subTenant/id eq '4d949784-e93e-4538-accb-6a0a464e4a4b'))"
```

In the following command input, the subtenant IDs correspond to business groups that are managed by the user who is logged-in. 

#### JSON Output

```text
{
  "links" : [ ],
  "content" : [ {
    "@type" : "ConsumerResource",
    "id" : "3bfde906-81b9-44c3-8c2d-07d2c9768168",
    "iconId" : "cafe_default_icon_genericCatalogResource",
    "resourceTypeRef" : {
      "id" : "Infrastructure.Virtual",
      "label" : "Virtual Machine"
    },
    "name" : "test2",
    "description" : null,
    "status" : "ACTIVE",
    "catalogResource" : {
      "id" : "e2f397be-72ad-4ec4-a688-c017560fa1a3",
      "label" : "test-blueprint"
    },
    "requestId" : "b013d2fa-4ba4-416c-b46b-98bb8cc7b076",
    "providerBinding" : {
      "bindingId" : "8a4581a0-84f9-4e80-9af6-75d79633e382",
      "providerRef" : {
        "id" : "6918cd49-b737-467f-94bf-d14d52c78fba",
        "label" : "iaas-service"
      }
    },
    "owners" : [ {
      "tenantName" : "MYCOMPANY",
      "ref" : "fritz@example.mycompany.com",
      "type" : "USER",
      "value" : "Fritz Arbeiter"
    } ],
    "organization" : {
      "tenantRef" : "MYCOMPANY",
      "tenantLabel" : "QETenant",
      "subtenantRef" : "eab762cb-6e75-4379-83ef-171a71c9f00e",
      "subtenantLabel" : "MyTestAgentBusinessGroup"
    },
    "dateCreated" : "2014-09-19T21:19:37.541Z",
    "lastUpdated" : "2014-09-19T21:19:40.888Z",
    "hasLease" : true,
    "lease" : {
      "start" : "2014-09-19T21:18:57.000Z"
    },
    "leaseForDisplay" : null,
    "hasCosts" : true,
    "costs" : {
      "leaseRate" : {
        "type" : "moneyTimeRate",
        "cost" : {
          "type" : "money",
          "currencyCode" : "USD",
          "amount" : 0.0
        },
        "basis" : {
          "type" : "timeSpan",
          "unit" : "DAYS",
          "amount" : 1
        }
      }
    },
    "costToDate" : {
      "type" : "money",
      "currencyCode" : "USD",
      "amount" : 0.0
    },
    "totalCost" : null,
    "childResources" : [ ],
    "operations" : [ {
      "name" : "Reprovision",
      "description" : "Reprovision a machine.",
      "iconId" : "machineReprovision.png",
      "type" : "ACTION",
      "id" : "a1caee9b-d67f-41e8-a7b3-131616a0f6ac",
      "extensionId" : null,
      "providerTypeId" : "com.mycompany.csp.iaas.blueprint.service",
      "bindingId" : "Infrastructure.Machine.Action.Reprovision",
      "hasForm" : false,
      "formScale" : null
    } ],
    "forms" : {
      "catalogResourceInfoHidden" : true,
      "details" : {
        "type" : "extension",
        "extensionId" : "csp.places.iaas.resource.details",
        "extensionPointId" : null
      }
    },
    "resourceData" : {
      "entries" : [ {
        "key" : "Expire",
        "value" : {
          "type" : "boolean",
          "value" : true
        }
      }, {
        "key" : "MachineGroupName",
        "value" : {
          "type" : "string",
          "value" : "MyTestAgentBusinessGroup"
        }
      }, {
        "key" : "NETWORK_LIST",
        "value" : {
          "type" : "multiple",
          "elementTypeId" : "COMPLEX",
          "resources" : [ {
            "type" : "complex",
            "componentTypeId" : "com.mycompany.csp.component.iaas.proxy.provider",
            "componentId" : null,
            "classId" : "vra.api.model.NetworkViewModel",
            "typeFilter" : null,
            "values" : {
              "entries" : [ {
                "key" : "NETWORK_MAC_ADDRESS",
                "value" : {
                  "type" : "string",
                  "value" : "56:52:4d:e7:46:d4"
                }
              }, {
                "key" : "NETWORK_NAME",
                "value" : {
                  "type" : "string",
                  "value" : "Test Agent-network-1"
                }
              } ]
            }
          } ]
        }
      }, {
        "key" : "SNAPSHOT_LIST",
        "value" : {
          "type" : "multiple",
          "elementTypeId" : "COMPLEX",
          "resources" : [ ]
        }
      }, {
        "key" : "ConnectViaRdp",
        "value" : {
          "type" : "boolean",
          "value" : true
        }
      }, {
        "key" : "MachineStatus",
        "value" : {
          "type" : "string",
          "value" : "On"
        }
      }, {
        "key" : "PowerOff",
        "value" : {
          "type" : "boolean",
          "value" : true
        }
      }, {
        "key" : "DISK_VOLUMES",
        "value" : {
          "type" : "multiple",
          "elementTypeId" : "COMPLEX",
          "resources" : [ {
            "type" : "complex",
            "componentTypeId" : "com.mycompany.csp.component.iaas.proxy.provider",
            "componentId" : null,
            "classId" : "vra.api.model.DiskInputModel",
            "typeFilter" : null,
            "values" : {
              "entries" : [ {
                "key" : "DISK_CAPACITY",
                "value" : {
                  "type" : "integer",
                  "value" : 1
                }
              }, {
                "key" : "DISK_DRIVE",
                "value" : {
                  "type" : "string",
                  "value" : "c"
                }
              }, {
                "key" : "DISK_INPUT_ID",
                "value" : {
                  "type" : "string",
                  "value" : "DISK_INPUT_ID1"
                }
              } ]
            }
          } ]
        }
      }, {
        "key" : "MachineBlueprintName",
        "value" : {
          "type" : "string",
          "value" : "test-blueprint"
        }
      }, {
        "key" : "Suspend",
        "value" : {
          "type" : "boolean",
          "value" : true
        }
      }, {
        "key" : "Reboot",
        "value" : {
          "type" : "boolean",
          "value" : true
        }
      }, {
        "key" : "Reprovision",
        "value" : {
          "type" : "boolean",
          "value" : true
        }
      }, {
        "key" : "MachineStorage",
        "value" : {
          "type" : "integer",
          "value" : 1
        }
      }, {
        "key" : "MachineDailyCost",
        "value" : {
          "type" : "decimal",
          "value" : 0.0
        }
      }, {
        "key" : "Destroy",
        "value" : {
          "type" : "boolean",
          "value" : true
        }
      }, {
        "key" : "MachineType",
        "value" : {
          "type" : "string",
          "value" : "Virtual"
        }
      }, {
        "key" : "InstallTools",
        "value" : {
          "type" : "boolean",
          "value" : true
        }
      }, {
        "key" : "Shutdown",
        "value" : {
          "type" : "boolean",
          "value" : true
        }
      }, {
        "key" : "ChangeLease",
        "value" : {
          "type" : "boolean",
          "value" : true
        }
      }, {
        "key" : "machineId",
        "value" : {
          "type" : "string",
          "value" : "8a4581a0-84f9-4e80-9af6-75d79633e382"
        }
      }, {
        "key" : "MachineMemory",
        "value" : {
          "type" : "integer",
          "value" : 0
        }
      }, {
        "key" : "MachineGuestOperatingSystem"
      }, {
        "key" : "MachineName",
        "value" : {
          "type" : "string",
          "value" : "test2"
        }
      }, {
        "key" : "MachineDestructionDate"
      }, {
        "key" : "MachineCPU",
        "value" : {
          "type" : "integer",
          "value" : 1
        }
      }, {
        "key" : "MachineInterfaceType",
        "value" : {
          "type" : "string",
          "value" : "Test"
        }
      }, {
        "key" : "MachineReservationName",
        "value" : {
          "type" : "string",
          "value" : "Test Agent-Res-1"
        }
      }, {
        "key" : "Reconfigure",
        "value" : {
          "type" : "boolean",
          "value" : true
        }
      }, {
        "key" : "EXTERNAL_REFERENCE_ID"
      }, {
        "key" : "MachineExpirationDate"
      }, {
        "key" : "Reset",
        "value" : {
          "type" : "boolean",
          "value" : true
        }
      } ]
    }
  } ],
  "metadata" : {
    "size" : 2,
    "totalElements" : 1,
    "totalPages" : 1,
    "number" : 1,
    "offset" : 0
  }
}
```

The following example displays the provisioned resources of one or more business groups. 

#### curl Command

GET /api/consumer/resources/types/{resourceTypeId} displays all of the provisioned resources that are owned by the business groups that you manage. You can optionally filter the list by business group name. 

### Viewing Machine Details 

GET /api/consumer/requests/{id}/resourceViews displays the machine details for a provisioned machine. 

#### curl Command

The following example displays machine details for a provisioned machine with resourceID=7aaf9baf-aa4e-47c4-997b-edd7c7983a5b. 

```text
curl --insecure -H "Content-Type: application/json" 
-H "Authorization: Bearer $token” 
http://$vRA/catalog-service/api/consumer/requests/7aaf9baf-aa4e-47c4-997b-edd7c7983a5b/resourceViews
```

#### JSON Output

The following JSON output is returned based on the command input. 

```text
{
    "links": [],
    "content": [
        {
            "@type": "CatalogResourceView",
            "links": [
                {
                    "@type": "link",
                    "rel": "GET: Catalog Item",
                    "href": "https://$vRA/catalog-service/api/consumer/entitledCatalogItemViews/7c8275d6-1bd6-452a-97c4-d6c053e4baa4"
                },
                {
                    "@type": "link",
                    "rel": "GET: Request",
                    "href": "https://$vRA/catalog-service/api/consumer/requests/7aaf9baf-aa4e-47c4-997b-edd7c7983a5b"
                },
                {
                    "@type": "link",
                    "rel": "GET Template: {com.vmware.csp.component.cafe.composition@resource.action.deployment.destroy.name}",
                    "href": "https://$vRA/catalog-service/api/consumer/resources/c4d3db3e-e397-44ff-a1c9-0ecebdba12f4/actions/416e6bb1-3357-448b-8396-e268d5f7343b/requests/template"
                },
                {
                    "@type": "link",
                    "rel": "POST: {com.vmware.csp.component.cafe.composition@resource.action.deployment.destroy.name}",
                    "href": "https://$vRA/catalog-service/api/consumer/resources/c4d3db3e-e397-44ff-a1c9-0ecebdba12f4/actions/416e6bb1-3357-448b-8396-e268d5f7343b/requests"
                },
                {
                    "@type": "link",
                    "rel": "GET: Child Resources",
                    "href": "https://$vRA/catalog-service/api/consumer/resourceViews?managedOnly=false&withExtendedData=true&withOperations=true&%24filter=parentResource%20eq%20%27c4d3db3e-e397-44ff-a1c9-0ecebdba12f4%27"
                }
            ],
            "resourceId": "c4d3db3e-e397-44ff-a1c9-0ecebdba12f4",
            "iconId": "cafe_default_icon_genericCatalogItem",
            "name": "Linux-80813151",
            "description": null,
            "status": null,
            "catalogItemId": "7c8275d6-1bd6-452a-97c4-d6c053e4baa4",
            "catalogItemLabel": "Linux",
            "requestId": "7aaf9baf-aa4e-47c4-997b-edd7c7983a5b",
            "resourceType": "{com.vmware.csp.component.cafe.composition@resource.type.deployment.name}",
            "owners": [
                "Connie Summers"
            ],
            "businessGroupId": "c0683388-6db2-4cb5-9033-b24d15ad3766",
            "tenantId": "mycompany",
            "dateCreated": "2015-07-29T13:51:36.368Z",
            "lastUpdated": "2015-07-29T13:55:35.963Z",
            "lease": null,
            "costs": null,
            "costToDate": null,
            "totalCost": null,
            "parentResourceId": null,
            "hasChildren": true,
            "data": {}
        }
    ],
    "metadata": {
        "size": 20,
        "totalElements": 1,
        "totalPages": 1,
        "number": 1,
        "offset": 0
    }
}
```

#### Using the API to Get Deployment Details

To view deployed machine details, append `/resourceViews` to the request details URI that you generated when you retrieved request details. 

```text
http://$vRA/catalog-service/api/consumer/requests/$requestId/resourceViews
```

See [Syntax for Viewing Details of a Machine Request](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-D4D6665B-EA83-479A-B732-DB9EDAC185E0.html#GUID-D4D6665B-EA83-479A-B732-DB9EDAC185E0). 

In addition to general information about the provisioned deployment such as its name, description, and ID, the response contains additional HATEOAS links that enable you to obtain additional details and information. 

<table>
  <thead>
    <tr>
      <th style="text-align:left">HATEOAS Link Functions as Defined by rel Field</th>
      <th style="text-align:left"></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">Link</td>
      <td style="text-align:left">Description</td>
    </tr>
    <tr>
      <td style="text-align:left">GET: Catalog Item</td>
      <td style="text-align:left">URI to get the catalog item details (as described in sections 3.2.1 and
        3.2.2) from which this catalog item was provisioned.</td>
    </tr>
    <tr>
      <td style="text-align:left">GET: Request</td>
      <td style="text-align:left">URI to get the request details that provisioned this item.</td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p>GET:Template</p>
        <p>{com.vmware.csp.component.cafe.composition@resource.action.deployment.$actionName</p>
      </td>
      <td style="text-align:left">URI to get a template request for a specific action that you can perform
        on this resource. Typically, on a deployment the action will be Delete.</td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p>POST:</p>
        <p>{com.vmware.csp.component.cafe.composition@resource.action.deployment.$actionName</p>
      </td>
      <td style="text-align:left">URI to which to post the request to perform an action, based on the corresponding
        template.</td>
    </tr>
    <tr>
      <td style="text-align:left">GET: Child Resources</td>
      <td style="text-align:left">If the deployment contains child resources (nodes specified in the composite
        blueprint), this is the URI to get a list of the resourceViews for the
        children of this deployment.</td>
    </tr>
  </tbody>
</table>#### Input

Use the supported input parameters to control the command output. 

| Parameter  | Description  |
| :--- | :--- |
| URL  | https://$vRA/catalog-service/api/consumer/resources/$resourceId  |
| $vRA | Specifies the appliance name and fully qualified domain name, or IP address of the vRealize Automation server.  |
| $token | Specifies a valid HTTP bearer token with necessary credentials.  |
| $resourceID | Specifies a resource ID. See [Display Your Provisioned Resources Example](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-53D67A18-7FCE-436A-9FB6-6559CAF59386.html#GUID-53D67A18-7FCE-436A-9FB6-6559CAF59386) to view all of your requests and search for a request ID.  |
| managedOnly | If true, the returned requests are from the user's managed subtenants.  |
| page | Specifies a page number.  |
| limit | Specifies the number of entries to display on a page.  |
| $orderby | Specifies how to order multiple comma-separated properties sorted in ascending or descending order.  |
| $top | Specifies the number of returned entries from the top of the response \(total number per page in relation to skip\).  |
| $skip | Specifies the number of entries to skip.  |
| filter | Contains a Boolean expression to determine if a particular entry is included in the response.  |

#### Output

The command output contains property names and values based on the command input parameters. 

| Property  | Description  |
| :--- | :--- |
| id  | Specifies the unique identifier of this resource.  |
| iconId  | Specifies an icon for this request based on the requested object type.  |
| resourceTypeRef  | Specifies the resource type.  |
| name  | Specifies the resource name.  |
| description  | Specifies the resource description.  |
| status  | Specifies the resource status.  |
| catalogItem  | Specifies the catalog item that defines the service this resource is based on.  |
| requestId  | Specifies the request ID that provisioned this resource.  |
| providerBinding  | Specifies the provider binding.  |
| owners  | Species the owners of this resource.  |
| organization  | Specifies the subtenant or tenant that owns this resource.  |
| dateCreated  | Specifies the data and time at which the resource was created.  |
| lastUpdated  | Specifies the date and time at which the resource was most recently modified.  |
| hasLease  | Returns true if the resource is subject to a lease.  |
| lease  | Displays the resource's current lease as start and end time stamps.  |
| leaseForDisplay  | Specifies the resource's current lease, \#getLease, with time units synchronized with \#getCosts.  |
| hasCosts  | Returns true if the resource is subject to per-time price.  |
| costs  | Displays an optional rate of the price charges for the resource. This parameter is deprecated.  |
| costToDate  | Displays an optional rate of the current price charges for the resource. This parameter is deprecated.  |
| totalCost  | Displays an optional rate of the price charges for the entire lease period. This parameter is deprecated.  |
| expenseMonthToDate  | The expense of the resource from the beginning of the month to the current date. This value is updated daily by vRealize Business for Cloud.  |
| parentResourceRef  | Displays the parent of this resource.  |
| childResources  | Displays the children of this resource.  |
| operations  | Specifies the sequence of available operations that can be performed on this resource.  |
| forms  | Specifies the forms used to render this resource.  |
| resourceData  | Displays the extended provider-defined properties of the resource. |

