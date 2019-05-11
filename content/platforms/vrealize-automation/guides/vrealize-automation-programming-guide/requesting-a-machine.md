# Requesting a Machine

You use the catalog service to perform tasks related to requesting a machine. 

The Catalog service is comprised APIs for the consumer, service providers, and service administrators. It is designed to be used by consumers and providers of the service catalog. For example, a consumer would request a catalog item such as a machine. The service provider would fulfill the request. 

The catalog service includes Hypermedia as the Engine of Application State \(HATEOAS\) links. The links function as templates that you can use to complete common tasks supported by the API. 

For example, if you submit a template request for a given context, such as: `catalog-service/api/consumer/entitledCatalogItems/dc808d12-3786-4f7c-b5a1-d5f997c8ad66/requests/template`. You use the returned template, either as-is or modified, to create a request that you POST or PUT to the target API, such as: `catalog-service/api/consumer/entitledCatalogItems/dc808d12-3786-4f7c-b5a1-d5f997c8ad66/requests`

List all shared catalog items in the catalog. 

## Making a Request 

To request a machine, you first list all shared catalog items to find the machine, then make the request for that item using a template. 

### Prerequisites

* Log in to vRealize Automation as a consumer and current business group user. 
* Verify that the appliance name and fully qualified domain name of the vRealize Automation instance are available. 
* Verify that you have a valid HTTP bearer token that matches your login credentials. See [REST API Authentication](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-05B9796E-1C20-4860-B77B-1E7F4BD7E556.html#GUID-05B9796E-1C20-4860-B77B-1E7F4BD7E556). 

### Procedure

1. List all shared catalog items in the catalog. 

   ```text
   curl --insecure -H "Accept: application/json" -H "Content-Type: application/json" -H "Authorization: Bearer $token" https://$vRA/catalog-service/api/consumer/entitledCatalogItemViews
   ```

   For details regarding input and output for this request, see [Syntax for Listing Shared and Private Catalog Items](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-DB5FACB2-233E-4D96-BA46-E053A1142608.html#GUID-DB5FACB2-233E-4D96-BA46-E053A1142608). 

2. Examine the response to find the catalogItemId
3. Get a template request for a catalog item. 

   Use the catalogItemId to submit the template request for this catalog item. In this example, the catalogItemId is dc808d12-3786-4f7c-b5a1-d5f997c8ad66. 

   ```text
   curl --insecure -H "Accept: application/json" -H "Content-Type: application/json" -H "Authorization: Bearer $token" https://$vRA/catalog-service/api/consumer/entitledCatalogItems/dc808d12-3786-4f7c-b5a1-d5f997c8ad66/requests/template
   ```

   For details regarding input and output for this request, see [Syntax for Getting a Template Request for a Catalog Item](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-C353A9E9-B2B6-43D0-89A5-B59B11522AC8.html#GUID-C353A9E9-B2B6-43D0-89A5-B59B11522AC8). 

   A template request for the catalog item is created. The fields and default values are populated based on the configuration of the underlying blueprint. By default, requestMachine.json is the name of the template request. 

4. Review and edit the template request. 

   Review the contents of the template request and edit the values if you want to change them from the default prior to submitting the request for a machine. For example, you can specify a value for the description field or change the values for the machine resources if the blueprint allows for a range. 

5. Submit the request for a machine. 

   ```text
   curl --insecure -H "Content-Type: application/json"
   -H "Authorization: Bearer $token”
   https://$vRA/catalog-service/api/consumer/entitledCatalogItems/dc808d12-3786-4f7c-b5a1-d5f997c8ad66/requests --verbose --data
   @C:/Temp/requestMachine.json
   {
        $contentsOfTemplateFromPrecedingSections
   }
   ```

   For details regarding input and output for this request see [Syntax for Requesting a Machine](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-8F3CF01B-4FA1-4CAA-8DB2-81E029C1F2B9.html#GUID-8F3CF01B-4FA1-4CAA-8DB2-81E029C1F2B9). 

6. **\(Optional\)** View the details of your request. 

   You can perform a GET on the URI in the Location header to retrieve the updated request details. In this example, the URI-in-Location-header is 7aaf9baf-aa4e-47c4-997b-edd7c7983a5b. 

   ```text
   curl --insecure -H "Accept: application/json" -H "Content-Type: application/json" -H "Authorization: Bearer $token" https://$vRA/catalog-service/api/consumer/requests/7aaf9baf-aa4e-47c4-997b-edd7c7983a5b
   ```

   For details regarding input and output for this request, see [Syntax for Viewing Details of a Machine Request](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-D4D6665B-EA83-479A-B732-DB9EDAC185E0.html#GUID-D4D6665B-EA83-479A-B732-DB9EDAC185E0).

## Catalog Service Examples

Syntax for each service example lists input parameters, output parameters, and curl commands.

### Listing Shared and Private Catalog Items

GET /api/consumer/entitledCatalogItemViews retrieves a list of all shared viewable catalog items for the current user. Shared catalog items do not belong to a specific business group. This service also retrieves a list of all shared and private catalog items that can be viewed, including their business groups. 

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
      <td style="text-align:left">https://$vRA/catalog-service/api/consumer/entitledCatalogItemViews</td>
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
      <td style="text-align:left">page number</td>
      <td style="text-align:left">The page number. Default is 1.</td>
    </tr>
    <tr>
      <td style="text-align:left">limit</td>
      <td style="text-align:left">The number of entries per page. The default is 20.</td>
    </tr>
    <tr>
      <td style="text-align:left">$orderby</td>
      <td style="text-align:left">
        <p>Multiple comma-separated properties sorted in ascending or descending
          order. Valid OData properties include the following:</p>
        <ul>
          <li>name - filter based on catalog item name.</li>
          <li>status - filter based on catalog item status.</li>
          <li>service/id - filter based on catalog item service id.</li>
          <li>service/name - filter based on catalog item service name.</li>
          <li>organization/subTenant/id - filter based on catalog item business group
            ID, which you can find in the catalogItem payload under organization &gt;
            subtenantRef</li>
          <li>organization/subTenant/name - filter based on catalog item business group
            name, which you can find in catalogItem payload under organization &gt;subtenantLabel</li>
          <li>outputResourceType/id - filter based on catalog item output resource type
            ID, for example : Infrastructure.Virtual</li>
          <li>outputResourceType/name - Filter based on catalog item output resource
            type name, for example: &quot;VirtualMavhine&quot;.</li>
          <li>catalogItemType/id - filter based on catalog item type ID, for example:
            &quot;Infrastructure.Virtual&quot;.</li>
          <li>catalogItemType/name - filter based on catalog item type name, for example:
            &quot;VirtualMachine&quot;.</li>
          <li>icon/id - filter based on catalog item icon ID.</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">$top</td>
      <td style="text-align:left">Sets the number of returned entries from the top of the response</td>
    </tr>
    <tr>
      <td style="text-align:left">$skip</td>
      <td style="text-align:left">Sets the number of entries to skip.</td>
    </tr>
    <tr>
      <td style="text-align:left">$filter</td>
      <td style="text-align:left">
        <p>Boolean expression for whether a particular entry should be included in
          the response. Valid OData properties include the following:</p>
        <ul>
          <li>name - filter based on catalog item name.</li>
          <li>status - filter based on catalog item status.</li>
          <li>service/id - filter based on catalog item service id.</li>
          <li>service/name - filter based on catalog item service name.</li>
          <li>organization/subTenant/id - filter based on catalog item business group
            ID, which you can find in the catalogItem payload under organization &gt;
            subtenantRef</li>
          <li>organization/subTenant/name - filter based on catalog item business group
            name, which you can find in catalogItem payload under organization &gt;subtenantLabel</li>
          <li>outputResourceType/id - filter based on catalog item output resource type
            ID, for example : Infrastructure.Virtual</li>
          <li>outputResourceType/name - Filter based on catalog item output resource
            type name, for example: &quot;VirtualMavhine&quot;.</li>
          <li>catalogItemType/id - filter based on catalog item type ID, for example:
            &quot;Infrastructure.Virtual&quot;.</li>
          <li>catalogItemType/name - filter based on catalog item type name, for example:
            &quot;VirtualMachine&quot;.</li>
          <li>icon/id - filter based on catalog item icon ID.</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">serviceId</td>
      <td style="text-align:left">(Optional) Query parameter to filter the returned catalog items by one
        specific service.</td>
    </tr>
    <tr>
      <td style="text-align:left">onBehalfOf</td>
      <td style="text-align:left">(Optional) Query parameter that provides the value of the user ID when
        making a request on behalf of another user.</td>
    </tr>
  </tbody>
</table>#### Output

The command output contains property names and values based on the command input parameters. 

| Property  | Description  |
| :--- | :--- |
| outputResourceTypeRef  | Specifies the type of the resource that results from requesting the catalog item.  |
| catalogItemId  | Specifies the catalog item identifier.  |
| name  | Specifies the user friendly name of the catalog item. Specifies the property type is string.  |
| description  | Specifies a short description of the catalog item. Specifies the property type is string.  |
| catalogItemTypeRef  | Specifies the type of the catalog item.  |
| serviceRef  | Specifies the catalog service that contains the catalog item.  |
| iconId  | Specifies the associated icon representing this item.  |
| isNoteworthy  | Specifies if the catalog item should be highlighted to users for a period of time.  |
| dateCreated  | Specifies the date that this item was created in the catalog.  |
| lastUpdatedDate  | Specifies the date that this item was last updated in the catalog.  |
| entitledOrganizations  | Specifies the organizations in which the catalog item can be consumed by the current user.  |

#### curl Command to List All Shared Catalog Items

The following example command retrieves information about all shared catalog items of type `ConsumerEntitledCatalogItemView`. 

```text
curl --insecure -H "Content-Type: application/json" 
-H "Authorization: Bearer $token”  https://$vRA/catalog-service/api/consumer/entitledCatalogItemViews
```

If backward compatibility is required, use the following example command instead. 

```text
curl --insecure -H "Content-Type: application/json" 
-H "Authorization: Bearer $token” https://$vRA/catalog-service/api/consumer/entitledCatalogItems
```

The following JSON output is returned based on the command input. 

```text
{
    "links": [],
    "content": [
        {
            "@type": "ConsumerEntitledCatalogItemView",
            "links": [
                {
                    "@type": "link",
                    "rel": "GET: Request Template",
                    "href": "https://$vRA/catalog-service/api/consumer/entitledCatalogItems/7c8275d6-1bd6-452a-97c4-d6c053e4baa4/requests/template"
                },
                {
                    "@type": "link",
                    "rel": "POST: Submit Request",
                    "href": "https://$vRA/catalog-service/api/consumer/entitledCatalogItems/7c8275d6-1bd6-452a-97c4-d6c053e4baa4/requests"
                }
            ],
            "entitledOrganizations": [
                {
                    "tenantRef": "mycompany",
                    "tenantLabel": "mycompany",
                    "subtenantRef": "c0683388-6db2-4cb5-9033-b24d15ad3766",
                    "subtenantLabel": "Demo Group"
                }
            ],
            "catalogItemId": "dc808d12-3786-4f7c-b5a1-d5f997c8ad66",
            "name": "Linux",
            "description": "Linux blueprint for API demo",
            "isNoteworthy": false,
            "dateCreated": "2015-07-29T03:54:28.141Z",
            "lastUpdatedDate": "2015-07-29T12:46:56.405Z",
            "iconId": "cafe_default_icon_genericCatalogItem",
            "catalogItemTypeRef": {
                "id": "com.vmware.csp.component.cafe.composition.blueprint",
                "label": "Composite Blueprint"
            },
            "serviceRef": {
                "id": "057d4095-95f1-47da-b14b-641ac9010c97",
                "label": "Infrastructure Services"
            },
            "outputResourceTypeRef": {
                "id": "composition.resource.type.deployment",
                "label": "Deployment"
            }
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

#### curl Command to Locate the Details of a Specific Catalog Item

To search for specific catalog item, add the `$catalogItemId`. The following example command retrieves information about a catalog item with the name `$catalogItemName`. 

```text
curl --insecure -H "Content-Type: application/json" 
-H "Authorization: Bearer $token” https://$vRA/catalog-service/api/consumer/entitledCatalogItemViews?$filter=name+eq+%27$catalogItemName%27
```

### Getting Information for a Catalog Item

GET /api/consumer/entitledCatalogItemViews/{id} gets information about a specific catalog item. 

#### REST API Catalog Service

The REST API supports OData filtering. For more information about supported OData filters, refer to the vRealize Automation API Reference, particularly the REST API Tips page located at https://$vRA/component-registry/services/docs/odata.html. 

For specific information about catalog service filters, see the "Important Notes About catalog-service and OData Queries" topic located at https://$vRA/catalog-service/api/docs/index.html. 

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
      <td style="text-align:left">https://$vRA/catalog-service/api/consumer/entitledCatalogItemViews/{id}</td>
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
      <td style="text-align:left">page number</td>
      <td style="text-align:left">The page number. Default is 1.</td>
    </tr>
    <tr>
      <td style="text-align:left">limit</td>
      <td style="text-align:left">The number of entries per page. The default is 20.</td>
    </tr>
    <tr>
      <td style="text-align:left">$orderby</td>
      <td style="text-align:left">
        <p>Multiple comma-separated properties sorted in ascending or descending
          order. Valid OData properties include the following:</p>
        <ul>
          <li>name - filter based on catalog item name.</li>
          <li>status - filter based on catalog item status.</li>
          <li>service/id - filter based on catalog item service id.</li>
          <li>service/name - filter based on catalog item service name.</li>
          <li>organization/subTenant/id - filter based on catalog item business group
            ID, which you can find in the catalogItem payload under organization &gt;
            subtenantRef</li>
          <li>organization/subTenant/name - filter based on catalog item business group
            name, which you can find in catalogItem payload under organization &gt;subtenantLabel</li>
          <li>outputResourceType/id - filter based on catalog item output resource type
            ID, for example : Infrastructure.Virtual</li>
          <li>outputResourceType/name - Filter based on catalog item output resource
            type name, for example: &quot;VirtualMavhine&quot;.</li>
          <li>catalogItemType/id - filter based on catalog item type ID, for example:
            &quot;Infrastructure.Virtual&quot;.</li>
          <li>catalogItemType/name - filter based on catalog item type name, for example:
            &quot;VirtualMachine&quot;.</li>
          <li>icon/id - filter based on catalog item icon ID.</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">$top</td>
      <td style="text-align:left">Sets the number of returned entries from the top of the response</td>
    </tr>
    <tr>
      <td style="text-align:left">$skip</td>
      <td style="text-align:left">Sets the number of entries to skip.</td>
    </tr>
    <tr>
      <td style="text-align:left">$filter</td>
      <td style="text-align:left">
        <p>Boolean expression for whether a particular entry should be included in
          the response. Valid OData properties include the following:</p>
        <ul>
          <li>name - filter based on catalog item name.</li>
          <li>status - filter based on catalog item status.</li>
          <li>service/id - filter based on catalog item service id.</li>
          <li>service/name - filter based on catalog item service name.</li>
          <li>organization/subTenant/id - filter based on catalog item business group
            ID, which you can find in the catalogItem payload under organization &gt;
            subtenantRef</li>
          <li>organization/subTenant/name - filter based on catalog item business group
            name, which you can find in catalogItem payload under organization &gt;subtenantLabel</li>
          <li>outputResourceType/id - filter based on catalog item output resource type
            ID, for example : Infrastructure.Virtual</li>
          <li>outputResourceType/name - Filter based on catalog item output resource
            type name, for example: &quot;VirtualMavhine&quot;.</li>
          <li>catalogItemType/id - filter based on catalog item type ID, for example:
            &quot;Infrastructure.Virtual&quot;.</li>
          <li>catalogItemType/name - filter based on catalog item type name, for example:
            &quot;VirtualMachine&quot;.</li>
          <li>icon/id - filter based on catalog item icon ID.</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">serviceId</td>
      <td style="text-align:left">(Optional) Query parameter to filter the returned catalog items by one
        specific service.</td>
    </tr>
    <tr>
      <td style="text-align:left">onBehalfOf</td>
      <td style="text-align:left">(Optional) Query parameter that provides the value of the user ID when
        making a request on behalf of another user.</td>
    </tr>
  </tbody>
</table>#### Output

The command output contains property names and values based on the command input parameters. 

| Property  | Description  |
| :--- | :--- |
| outputResourceTypeRef  | Specifies the type of the resource that results from requesting the catalog item.  |
| catalogItemId  | Specifies the catalog item identifier.  |
| name  | Specifies the user friendly name of the catalog item. Specifies the property type is string.  |
| description  | Specifies a short description of the catalog item. Specifies the property type is string.  |
| catalogItemTypeRef  | Specifies the type of the catalog item.  |
| serviceRef  | Specifies the catalog service that contains the catalog item.  |
| iconId  | Specifies the associated icon representing this item.  |
| isNoteworthy  | Specifies if the catalog item should be highlighted to users for a period of time.  |
| dateCreated  | Specifies the date that this item was created in the catalog.  |
| lastUpdatedDate  | Specifies the date that this item was last updated in the catalog.  |
| entitledOrganizations  | The list of organizations in which the current user can consume the catalog item.  |

#### curl Command to Get Information for a Catalog Item

The following example command retrieves information catalog item with the name `$filter=name+eq+%27$catalogItemName%27`. 

```text
curl --insecure -H "Content-Type: application/json" 
-H "Authorization: Bearer $token”  https://$vRA/catalog-service/api/consumer/entitledCatalogItemViews?$filter=name+eq+%27$catalogItemName%27
```

The following JSON output is returned based on the command input. 

```text
{
    "links": [],
    "content": [
        {
            "@type": "ConsumerEntitledCatalogItemView",
            "links": [
                {
                    "@type": "link",
                    "rel": "GET: Request Template",
                    "href": "https://$vRA/catalog-service/api/consumer/entitledCatalogItems/7c8275d6-1bd6-452a-97c4-d6c053e4baa4/requests/template"
                },
                {
                    "@type": "link",
                    "rel": "POST: Submit Request",
                    "href": "https://$vRA/catalog-service/api/consumer/entitledCatalogItems/7c8275d6-1bd6-452a-97c4-d6c053e4baa4/requests"
                }
            ],
            "entitledOrganizations": [
                {
                    "tenantRef": "mycompany",
                    "tenantLabel": "mycompany",
                    "subtenantRef": "c0683388-6db2-4cb5-9033-b24d15ad3766",
                    "subtenantLabel": "Demo Group"
                }
            ],
            "catalogItemId": "7c8275d6-1bd6-452a-97c4-d6c053e4baa4",
            "name": "Linux",
            "description": "Linux blueprint for API demo",
            "isNoteworthy": false,
            "dateCreated": "2015-07-29T03:54:28.141Z",
            "lastUpdatedDate": "2015-07-29T12:46:56.405Z",
            "iconId": "cafe_default_icon_genericCatalogItem",
            "catalogItemTypeRef": {
                "id": "com.vmware.csp.component.cafe.composition.blueprint",
                "label": "Composite Blueprint"
            },
            "serviceRef": {
                "id": "057d4095-95f1-47da-b14b-641ac9010c97",
                "label": "Infrastructure Services"
            },
            "outputResourceTypeRef": {
                "id": "composition.resource.type.deployment",
                "label": "Deployment"
            }
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

### Getting a Template Request for a Catalog Item

GET /api/consumer/entitledCatalogItems/{id}/requests/template retrieves a template request for a specific catalog item. VMware supplies a number of templates to help you create different types of machine requests. 

#### Overview

In the entitledCatalogItemViews response, a link field contains a value similar to the following. 

```text
{ 
            "@type":"link",
            "href":"https://$vRA/catalog-service/api/consumer/entitledCatalogItems/dc808d12-3786-4f7c-b5a1-d5f997c8ad66/requests/template",
            "rel":"GET: Request Template"
        }
```

This URL is a HATEOAS link for a template request for this catalog item. The `rel` field provides a description of the link \(request template\) and indicates the HTTP method to use with the URI in the `href` field \(GET\). By using these HATEOAS links, you can make follow-on API calls without having to consult the API documentation for the URI syntax or construct the links programmatically. 

#### Review and Edit the Template Request

The returned template request is specific to the applicable catalog item. The fields and default values are populated based on the configuration of the underlying blueprint. 

You can review the contents of the template and optionally edit the values if you want to change them from the default prior to submitting the request. For example, you can specify a value for the description field or change the values for the machine resources if the blueprint allows for a range. 

#### Input

Use the supported input parameters to control the command output. 

| Parameter  | Description  |
| :--- | :--- |
| id  | The UUID of the catalog item.  |

#### Output

The command output contains property names and values based on the command input parameters. 

| Property  | Description  |
| :--- | :--- |
| entitledOrganizations  | The list of organizations in which the current user can consume the catalog item.  |
| catalogItemId  | Specifies the catalog item identifier.  |

#### curl Command to Get a Template Request for a Catalog Item

The following example command retrieves a template request for the catalog item with ID `dc808d12-3786-4f7c-b5a1-d5f997c8ad66`. 

```text
curl --insecure -H "Content-Type: application/json" 
-H "Authorization: Bearer $token”  https://$vRA/catalog-service/api/consumer/entitledCatalogItems/dc808d12-3786-4f7c-b5a1-d5f997c8ad66/requests/template
```

The following JSON output is returned based on the command input. Note:

Price is referred to as `cost` in API commands and output. 

```text
{
    "type": "com.vmware.vcac.catalog.domain.request.CatalogItemProvisioningRequest",
    "catalogItemId": "7c8275d6-1bd6-452a-97c4-d6c053e4baa4",
    "requestedFor": "csummers@example.com",
    "businessGroupId": "c0683388-6db2-4cb5-9033-b24d15ad3766",
    "description": null,
    "reasons": null,
    "data": {
        "Existing_Network_1": {
            "componentTypeId": "com.vmware.csp.component.cafe.composition",
            "componentId": null,
            "classId": "Blueprint.Component.Declaration",
            "typeFilter": "LinuxDemo*Existing_Network_1",
            "data": {
                "_cluster": 1,
                "_hasChildren": false,
                "description": null,
                "name": "Existing Network",
                "networkname": "Existing Network",
                "subnetmask": "255.255.255.0"
            }
        },
        "vSphere-Linux": {
            "componentTypeId": "com.vmware.csp.component.cafe.composition",
            "componentId": null,
            "classId": "Blueprint.Component.Declaration",
            "typeFilter": "LinuxDemo*vSphere-Linux",
            "data": {
                "Cafe.Shim.VirtualMachine.MaxCost": 0,
                "Cafe.Shim.VirtualMachine.MinCost": 0,
                "_cluster": 1,
                "_hasChildren": false,
                "action": "FullClone",
                "allow_storage_policies": false,
                "archive_days": 0,
                "blueprint_type": "1",
                "cpu": 1,
                "custom_properties": [],
                "daily_cost": 0,
                "datacenter_location": null,
                "description": null,
                "disks": [
                    {
                        "componentTypeId": "com.vmware.csp.iaas.blueprint.service",
                        "componentId": null,
                        "classId": "Infrastructure.Compute.Machine.MachineDisk",
                        "typeFilter": null,
                        "data": {
                            "capacity": 6,
                            "id": 0,
                            "initial_location": "",
                            "is_clone": false,
                            "label": "",
                            "storage_reservation_policy": "",
                            "userCreated": true,
                            "volumeId": 0
                        }
                    }
                ],
                "display_location": false,
                "guest_customization_specification": null,
                "lease_days": 0,
                "machine_actions": [
                    "DESTROY",
                    "POWER_ON",
                    "CONNECT_RDP_SSH",
                    "REPROVISION",
                    "POWER_CYCLE",
                    "EXPIRE",
                    "SUSPEND",
                    "CONNECT_REMOTE_CONSOLE",
                    "CONNECT_USING_VDI"
                ],
                "machine_prefix": {
                    "componentId": null,
                    "classId": "Infrastructure.Compute.MachinePrefix",
                    "id": "Use group default"
                },
                "max_network_adapters": 0,
                "max_per_user": 0,
                "max_volumes": 60,
                "memory": 4096,
                "nics": [
                    {
                        "componentTypeId": "com.vmware.csp.iaas.blueprint.service",
                        "componentId": null,
                        "classId": "Infrastructure.Compute.Machine.Nic",
                        "typeFilter": null,
                        "data": {
                            "address": "",
                            "assignment_type": "DHCP",
                            "custom_properties": null,
                            "id": 0,
                            "load_balancing": "",
                            "network_profile": "Existing Network"
                        }
                    }
                ],
                "number_of_instances": 1,
                "os_arch": "x86_64",
                "os_distribution": null,
                "os_type": "Linux",
                "os_version": null,
                "platform_name": "vsphere",
                "platform_type": "virtual",
                "property_groups": [
                    null
                ],
                "provisioning_workflow": {
                    "componentId": null,
                    "classId": "Infrastructure.Compute.ProvisioningWorkflow",
                    "id": "CloneWorkflow"
                },
                "reservation_policy": {
                    "componentId": null,
                    "classId": "Infrastructure.Reservation.Policy.ComputeResource",
                    "id": "None"
                },
                "security_groups": [],
                "security_tags": [],
                "source_machine": null,
                "source_machine_external_snapshot": null,
                "source_machine_name": "cbpcentos_63_x86",
                "source_machine_vmsnapshot": null,
                "storage": 6
            }
        }
    }
}
```

### Requesting a Machine

POST /api/consumer/entitledCatalogItems/{id}/requests submits a request for a specific catalog item with input provided in a JSON file. 

#### Prepare your Request

From the entitledCatalogItemViews response, locate the link field that contains a value similar to the following: 

```text

```

Use the information in this response to edit the template construct the URI to request the desired catalog item using a POST command. 

#### Input

Use the supported input parameters to control the command output. 

| Parameter  | Description  |
| :--- | :--- |
| URL  | https://$vRA/catalog-service/api/consumer/entitledCatalogItems/$catalogId/requests  |
| $vRA | Specifies the appliance name and fully qualified domain name, or IP address of the vRealize Automation server.  |
| $token | Specifies a valid HTTP bearer token with necessary credentials.  |
| catalogItemId  | The identifier of a catalog item. Typically, this is provided by users via the REST URI when making an entitledCatalogItem provisioning request.  |
| requestedFor  | The user for whom this request is being made. Must be the fully qualified user ID. Typically this is provided by the REST URI when making an entitledCatalogItem provisioning request.  |
| businessGroupId  | The business group identifier associated with the request. Typically this is provided via the REST URI when making the request.  |
| description  | The catalog item description.  |
| reasons  |  |
| data  | Context-specific properties. Obtain the consumerEntitledCatalogItem template request to identify the properties available for a given context.  |

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
      <td style="text-align:left">version</td>
      <td style="text-align:left">Displays the object version number.</td>
    </tr>
    <tr>
      <td style="text-align:left">state</td>
      <td style="text-align:left">Specifies the item state, such as submitted.</td>
    </tr>
    <tr>
      <td style="text-align:left">approvalStatus</td>
      <td style="text-align:left">Specifies a status indicating whether this request has been approved,
        rejected, or is still pending some form of approval.</td>
    </tr>
    <tr>
      <td style="text-align:left">waitingStatus</td>
      <td style="text-align:left">Specifies a status indicating whether this request is waiting on any external
        users or services before it is able to progress.</td>
    </tr>
    <tr>
      <td style="text-align:left">requestNumber</td>
      <td style="text-align:left">Specifies a more user-friendly identifier for this request.</td>
    </tr>
    <tr>
      <td style="text-align:left">executionStatus</td>
      <td style="text-align:left">Specifies the current execution status of the request.</td>
    </tr>
    <tr>
      <td style="text-align:left">stateName</td>
      <td style="text-align:left">Specifies the localized state name.</td>
    </tr>
    <tr>
      <td style="text-align:left">phase</td>
      <td style="text-align:left">Specifies the current phase of the request, which is more coarse grained
        and easier for users to understand.</td>
    </tr>
    <tr>
      <td style="text-align:left">id</td>
      <td style="text-align:left">Specifies the unique identifier of this resource.</td>
    </tr>
    <tr>
      <td style="text-align:left">iconId</td>
      <td style="text-align:left">Specifies an icon for this request based on the requested object type.</td>
    </tr>
    <tr>
      <td style="text-align:left">description</td>
      <td style="text-align:left">Contains a brief description of this request.</td>
    </tr>
    <tr>
      <td style="text-align:left">reasons</td>
      <td style="text-align:left">Specifies the business reasons entered by the requestor or owner of this
        request.</td>
    </tr>
    <tr>
      <td style="text-align:left">requestedFor</td>
      <td style="text-align:left">Specifies the ID of the user for whom this request is logged.</td>
    </tr>
    <tr>
      <td style="text-align:left">requestedBy</td>
      <td style="text-align:left">Specifies the ID of the user who actually submitted the request</td>
    </tr>
    <tr>
      <td style="text-align:left">organization</td>
      <td style="text-align:left">Subtenant and/or tenant owner of this request.</td>
    </tr>
    <tr>
      <td style="text-align:left">requestorEntitlementId</td>
      <td style="text-align:left">Specified the value of the requestorEntitlement setting.</td>
    </tr>
    <tr>
      <td style="text-align:left">preApprovalId</td>
      <td style="text-align:left">Specifies the ID of the preApproval setting.</td>
    </tr>
    <tr>
      <td style="text-align:left">postApprovalId</td>
      <td style="text-align:left">Specifies the ID of the approval generated for the post-provisioning workflow
        step.</td>
    </tr>
    <tr>
      <td style="text-align:left">dateCreated</td>
      <td style="text-align:left">Specifies the date when this request was sent to the catalog.</td>
    </tr>
    <tr>
      <td style="text-align:left">lastUpdated</td>
      <td style="text-align:left">Specifies the date when this request was last updated.</td>
    </tr>
    <tr>
      <td style="text-align:left">dateSubmitted</td>
      <td style="text-align:left">Specifies the date when this request was first submitted.</td>
    </tr>
    <tr>
      <td style="text-align:left">dateApproved</td>
      <td style="text-align:left">Specifies the date when this request was approved.</td>
    </tr>
    <tr>
      <td style="text-align:left">dateCompleted</td>
      <td style="text-align:left">Specifies the date when this request was completed.</td>
    </tr>
    <tr>
      <td style="text-align:left">quote</td>
      <td style="text-align:left">Contains a quote made by the provider defining the estimated price(es)
        associated with the request and/or any resources provisioned as a result
        of the request.</td>
    </tr>
    <tr>
      <td style="text-align:left">requestCompletion</td>
      <td style="text-align:left">Contains additional request completion information.</td>
    </tr>
    <tr>
      <td style="text-align:left">requestData</td>
      <td style="text-align:left">Contains a map of the provider-specific field-value pairs collected for
        this request.</td>
    </tr>
    <tr>
      <td style="text-align:left">retriesRemaning</td>
      <td style="text-align:left">
        <p>Specifies the number of attempts remaining to move this request from its
          current state to the next state in the request workflow.</p>
        <p>Some state transitions require calls to external services. These calls
          may fail due to transient errors such as momentary network errors. In these
          cases, the catalog will retry the call a number of times before failing.</p>
        <p>This property defines the number of retries remaining for the current
          state transition. When it reaches 0, the catalog will stop retrying and
          mark the request as failed. This property is reset to the default number
          of retries for every new operation that is triggered.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">requestedItemName</td>
      <td style="text-align:left">Specifies the item name.</td>
    </tr>
    <tr>
      <td style="text-align:left">requestedItemDescription</td>
      <td style="text-align:left">Specifies the item description.</td>
    </tr>
    <tr>
      <td style="text-align:left">components</td>
      <td style="text-align:left">Returns the list of components associated with the request. The provider
        supplies this list of components after request initialization.</td>
    </tr>
  </tbody>
</table>#### curl Command to Request a Machine

To construct your request, refer to the entitledCatalogItemViews response received when you ran the request described in [Syntax for Getting a Template Request for a Catalog Item](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-C353A9E9-B2B6-43D0-89A5-B59B11522AC8.html#GUID-C353A9E9-B2B6-43D0-89A5-B59B11522AC8), locate a link field that contains a value similar to the following: 

```text
	{ 
            "@type":"link",
            "href":"https://$vRA/catalog-service/api/consumer/entitledCatalogItems/f89fcbbf-7716-4a61-addd-a822dd4206f6/requests",
            "rel":"POST: Submit Request"
        }
```

The following example command submits a machine request using appropriately edited template content from the entitledCatalogItemViews response. 

```text
curl --insecure -H "Content-Type: application/json" 
-H "Authorization: Bearer $token” 
https://$vRA/catalog-service/api/consumer/entitledCatalogItems/f89fcbbf-7716-4a61-addd-a822dd4206f6/requests
{
     $contentsOfTemplateFromPrecedingSections
}
```

#### Output with Request and Response Headers

The following sample displays the request and response headers and the command output. Use the indicated JSON text file or inline text as input. 

```text
{
Accept = application/json
Content-Type = application/json
Content-Length = 2806
}
Response Headers
{
Date = Wed, 03 Dec 2014 20:58:34 GMT
ETag = "0"
Location = https://$vRA/catalog-service/api/consumer/requests/7aaf9baf-aa4e-47c4-997b-edd7c7983a5b
{
	$requestObjectDetails
}

Content-Type = application/json;charset=UTF-8
Content-Length = 0
Vary = Accept-Encoding,User-Agent
Keep-Alive = timeout=15, max=100
Connection = Keep-Alive
}
null
```

### Viewing Details of a Machine Request

GET /api/consumer/requests/{requestId} provides the details of a machine request, where requestId is the URI in the Location header. 

#### Request Status

Typically, the request status information is the most important part of request details. The phase field corresponds to the status displayed in the Requests tab in the interface. You can rerun this command multiple times to monitor the state of a machine request. 

| Request Phase Status |  |  |
| :--- | :--- | :--- |
| Phase  | Description  | End State?  |
| UNSUBMITTED  | Request was saved but not submitted.  | No  |
| PENDING\_PRE\_APPROVAL  | Request is subject to approval - pre-provisioning approval required.  | No  |
| IN\_PROGRESS  | Request is in progress, machine is being provisioned.  | No  |
| PENDING\_POST\_APPROVAL  | Request is subject to approval, post-provisioning approval required.  | No  |
| SUCCESSFUL  | Request completed successfully. The machine is available under provisioned resources on the Items tab.  | Yes  |
| FAILED  | Request failed.  | Yes  |
| REJECTED  | Request approval was rejected and will not complete.  | Yes  |

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
      <td style="text-align:left">https://$vRA/catalog-service/api/consumer/requests/$requestId</td>
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
      <td style="text-align:left">$requestId</td>
      <td style="text-align:left">
        <p>Specifies the request ID. See <a href="https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-53D67A18-7FCE-436A-9FB6-6559CAF59386.html#GUID-53D67A18-7FCE-436A-9FB6-6559CAF59386">Display Your Provisioned Resources Example</a> to
          view all of your requests and search for a request ID.</p>
        <p>The required request ID is located at the end of the Location URL in the
          response header.</p>
        <p>The request ID is located in the Location field of the response header
          if you submitted the request with the &#x2013;headers flag.</p>
      </td>
    </tr>
  </tbody>
</table>#### Output

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
      <td style="text-align:left">version</td>
      <td style="text-align:left">Displays the object version number.</td>
    </tr>
    <tr>
      <td style="text-align:left">state</td>
      <td style="text-align:left">Specifies the item state, such as submitted.</td>
    </tr>
    <tr>
      <td style="text-align:left">approvalStatus</td>
      <td style="text-align:left">Specifies a status indicating whether this request has been approved,
        rejected, or is still pending some form of approval.</td>
    </tr>
    <tr>
      <td style="text-align:left">waitingStatus</td>
      <td style="text-align:left">Specifies a status indicating whether this request is waiting on any external
        users or services before it is able to progress.</td>
    </tr>
    <tr>
      <td style="text-align:left">requestNumber</td>
      <td style="text-align:left">Specifies a more user-friendly identifier for this request.</td>
    </tr>
    <tr>
      <td style="text-align:left">executionStatus</td>
      <td style="text-align:left">Specifies the current execution status of the request.</td>
    </tr>
    <tr>
      <td style="text-align:left">stateName</td>
      <td style="text-align:left">Specifies the localized state name.</td>
    </tr>
    <tr>
      <td style="text-align:left">phase</td>
      <td style="text-align:left">Specifies the current phase of the request, which is more coarse grained
        and easier for users to understand.</td>
    </tr>
    <tr>
      <td style="text-align:left">id</td>
      <td style="text-align:left">Specifies the unique identifier of this resource.</td>
    </tr>
    <tr>
      <td style="text-align:left">iconId</td>
      <td style="text-align:left">Specifies an icon for this request based on the requested object type.</td>
    </tr>
    <tr>
      <td style="text-align:left">description</td>
      <td style="text-align:left">Contains a brief description of this request.</td>
    </tr>
    <tr>
      <td style="text-align:left">reasons</td>
      <td style="text-align:left">Specifies the business reasons entered by the requestor or owner of this
        request.</td>
    </tr>
    <tr>
      <td style="text-align:left">requestedFor</td>
      <td style="text-align:left">Specifies the ID of the user for whom this request is logged.</td>
    </tr>
    <tr>
      <td style="text-align:left">requestedBy</td>
      <td style="text-align:left">Specifies the ID of the user who actually submitted the request</td>
    </tr>
    <tr>
      <td style="text-align:left">organization</td>
      <td style="text-align:left">Subtenant and/or tenant owner of this request.</td>
    </tr>
    <tr>
      <td style="text-align:left">requestorEntitlementId</td>
      <td style="text-align:left">Specified the value of the requestorEntitlement setting.</td>
    </tr>
    <tr>
      <td style="text-align:left">preApprovalId</td>
      <td style="text-align:left">Specifies the ID of the preApproval setting.</td>
    </tr>
    <tr>
      <td style="text-align:left">postApprovalId</td>
      <td style="text-align:left">Specifies the ID of the approval generated for the post-provisioning workflow
        step.</td>
    </tr>
    <tr>
      <td style="text-align:left">dateCreated</td>
      <td style="text-align:left">Specifies the date when this request was sent to the catalog.</td>
    </tr>
    <tr>
      <td style="text-align:left">lastUpdated</td>
      <td style="text-align:left">Specifies the date when this request was last updated.</td>
    </tr>
    <tr>
      <td style="text-align:left">dateSubmitted</td>
      <td style="text-align:left">Specifies the date when this request was first submitted.</td>
    </tr>
    <tr>
      <td style="text-align:left">dateApproved</td>
      <td style="text-align:left">Specifies the date when this request was approved.</td>
    </tr>
    <tr>
      <td style="text-align:left">dateCompleted</td>
      <td style="text-align:left">Specifies the date when this request was completed.</td>
    </tr>
    <tr>
      <td style="text-align:left">quote</td>
      <td style="text-align:left">Contains a quote made by the provider defining the estimated price(es)
        associated with the request and/or any resources provisioned as a result
        of the request.</td>
    </tr>
    <tr>
      <td style="text-align:left">requestCompletion</td>
      <td style="text-align:left">Contains additional request completion information.</td>
    </tr>
    <tr>
      <td style="text-align:left">requestData</td>
      <td style="text-align:left">Contains a map of the provider-specific field-value pairs collected for
        this request.</td>
    </tr>
    <tr>
      <td style="text-align:left">retriesRemaning</td>
      <td style="text-align:left">
        <p>Specifies the number of attempts remaining to move this request from its
          current state to the next state in the request workflow.</p>
        <p>Some state transitions require calls to external services. These calls
          may fail due to transient errors such as momentary network errors. In these
          cases, the catalog will retry the call a number of times before failing.</p>
        <p>This property defines the number of retries remaining for the current
          state transition. When it reaches 0, the catalog will stop retrying and
          mark the request as failed. This property is reset to the default number
          of retries for every new operation that is triggered.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">requestedItemName</td>
      <td style="text-align:left">Specifies the item name.</td>
    </tr>
    <tr>
      <td style="text-align:left">requestedItemDescription</td>
      <td style="text-align:left">Specifies the item description.</td>
    </tr>
    <tr>
      <td style="text-align:left">components</td>
      <td style="text-align:left">Returns the list of components associated with the request. The provider
        supplies this list of components after request initialization.</td>
    </tr>
  </tbody>
</table>#### curl Command to View the Details of the Machine Request

The following example command displays details of a request. 

```text
curl --insecure -H "Content-Type: application/json" -H "Authorization: Bearer $token” 
https://$vRA/catalog-service/api/consumer/requests/7aaf9baf-aa4e-47c4-997b-edd7c7983a5b
```

The following sample output contains information about the catalog item request 7aaf9baf-aa4e-47c4-997b-edd7c7983a5b. 

```text
{
    "@type": "CatalogItemRequest",
    "id": "7aaf9baf-aa4e-47c4-997b-edd7c7983a5b",
    "iconId": "cafe_default_icon_genericCatalogItem",
    "version": 6,
    "requestNumber": 8,
    "state": "SUCCESSFUL",
    "description": "API test",
    "reasons": null,
    "requestedFor": "csummers@example.com",
    "requestedBy": "csummers@example.com",
    "organization": {
        "tenantRef": "mycompany",
        "tenantLabel": "mycompany",
        "subtenantRef": "c0683388-6db2-4cb5-9033-b24d15ad3766",
        "subtenantLabel": "Demo Group"
    },
    "requestorEntitlementId": "1b409157-152c-43c4-b4cc-1cdef7f6adf8",
    "preApprovalId": null,
    "postApprovalId": null,
    "dateCreated": "2015-07-29T13:50:33.689Z",
    "lastUpdated": "2015-07-29T13:55:35.951Z",
    "dateSubmitted": "2015-07-29T13:50:33.689Z",
    "dateApproved": null,
    "dateCompleted": "2015-07-29T13:55:35.949Z",
    "quote": {},
    "requestCompletion": {
        "requestCompletionState": "SUCCESSFUL",
        "completionDetails": null
    },
    "requestData": {
    	$detailsOfSubmittedRequest
    },
    "retriesRemaining": 3,
    "requestedItemName": "Linux",
    "requestedItemDescription": "Linux blueprint for API demo",
    "stateName": "Successful",
    "approvalStatus": "POST_APPROVED",
    "executionStatus": "STOPPED",
    "waitingStatus": "NOT_WAITING",
    "phase": "SUCCESSFUL",
    "catalogItemRef": {
        "id": "7c8275d6-1bd6-452a-97c4-d6c053e4baa4",
        "label": "Linux"
    }
}
```

Note: In the request details, the phase field corresponds to the status that is displayed in the Requests tab in the user interface.

