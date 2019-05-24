# Managing Provisioned Deployments

You use the catalog service to manage provisioned deployments. 

The catalog service is designed to be used by consumers of the service catalog. For example, a consumer might want to list all provisioned resources then submit a request to power off a resource.

### Prerequisites

* Log in to vRealize Automation as a business group manager. 
* Verify that the appliance name and fully qualified domain name of the vRealize Automation instance are available. 
* Verify that you have a valid HTTP bearer token that matches your login credentials. See [REST API Authentication](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-05B9796E-1C20-4860-B77B-1E7F4BD7E556.html#GUID-05B9796E-1C20-4860-B77B-1E7F4BD7E556). 

### Procedure

1. Display a list of all provisioned resources. 

   ```text
   $curl --insecure -s -H" Content-Type: multipart/form-data" -H "Authorization: Bearer $token" http://$vRA/catalog-service/api/consumer/requests/7aaf9baf-aa4e-47c4-997b-edd7c7983a5b/resourceViews
   ```

   For details regarding input and output of this sample, see [Syntax for Getting Deployment Details](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-3C2A8DEF-FF41-4046-A755-464A3280CFF0.html#GUID-3C2A8DEF-FF41-4046-A755-464A3280CFF0). 

2. Examine the response for the HATEOAS links that you need to obtain additional information about specific deployed resources. 
3. Use the `GET: Child Resources` HATEOAS link to retrieve a list of child nodes of a deployment. 

   ```text
   $curl --insecure -s -H" Content-Type: multipart/form-data" -H "Authorization: Bearer $token" https:// $vRA/catalog-service/api/consumer/resourceViews?managedOnly=false&withExtendedData=true&withOperations=true&%24filter=parentResource%20eq%20%27c4d3db3e-e397-44ff-a1c9-0ecebdba12f4%27
   ```

   For details regarding input and output of this sample, see [Syntax for Navigating to the Children of a Deployed Resource](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-F43B3AEB-7F0C-434F-A124-406D218ED918.html#GUID-F43B3AEB-7F0C-434F-A124-406D218ED918). 

Note: The vRealize Automation REST API does not support custom resource actions template API calls. However, you can perform custom resource actions programmatically by using the vRealize CloudClient. 

## Powering Off

You use the catalog service to perform a power off action. For simple actions that require no user input, the process is straightforward. 

### About this Task

This command leverages the links for the power off action from the command used in the [Syntax for Navigating to the Children of a Deployed Resource](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-F43B3AEB-7F0C-434F-A124-406D218ED918.html#GUID-F43B3AEB-7F0C-434F-A124-406D218ED918) example. 

```text
{
   "@type": "link",
   "rel": "GET Template: {...iaas.proxy.provider@resource.action.name.machine.PowerOff}",
   "href": "https://$vRA/api/consumer/resources/dd3...a4a/actions/02ba...e38/requests/template"
},
{
   "@type": "link",
   "rel": "POST: {com.vmware..iaas.proxy.provider@resource.action.name.machine.PowerOff}",
    "href": "https://$vRA/api/consumer/resources/dd3...a4a/actions/02b...e38/requests"
}
```

### Procedure

1. Get the template for the resource action request. 

   ```text
   $curl --insecure -s -H" Content-Type: multipart/form-data" -H "Authorization: Bearer $token" https://$vRA/catalog-service/api/consumer/resources/dd37b7a1-829c-4773-b5be-b229453eca4a/actions/02bad06d-f92b-4cf8-b964-37bb5d57be38/requests/template
   ```

2. Examine the response. 

   ```text
 
   HTTP/1.1 200 OK   
   Server: Apache-Coyote/1.1   
   Cache-Control: no-cache, no-store   
   Pragma: no-cache   
   Expires: Sat, 01 August 2015 23:04:50 GMT
   Content-Type: application/json;charset=UTF-8   
   Date: Sat, 01 August 2015 13:04:50 GMT
   {
     "type": "com.vmware.vcac.catalog.domain.request.CatalogResourceRequest",
     "resourceId": "dd37b7a1-829c-4773-b5be-b229453eca4a",
     "actionId": "02bad06d-f92b-4cf8-b964-37bb5d57be38",
     "description": null,
     "data": {
       "description": null,
       "reasons": null
     }
   }
   ```

3. Use a POST command to send the template without modification to the corresponding URI. 

   ```text
   $curl --insecure -s -H" Content-Type: multipart/form-data" -H "Authorization: Bearer $token"https://$vRA/catalog-service/api/consumer/resources/dd37b7a1-829c-4773-b5be-b229453eca4a/actions/02bad06d-f92b-4cf8-b964-37bb5d57be38/requests 
   {
     "type": "com.vmware.vcac.catalog.domain.request.CatalogResourceRequest",
     "resourceId": "dd37b7a1-829c-4773-b5be-b229453eca4a",
     "actionId": "02bad06d-f92b-4cf8-b964-37bb5d57be38",
     "description": null,
     "data": {
       "description": null,
       "reasons": null
     }
   }
   ```

### Results

This POST command returns a response indicating success or failure, such as `HTTP/1.1 201 CREATED` for success.

For additional posts and articles that illustrate methods for performing actions by using the vRealize Automation REST API or vRealize CloudClient tool, see the [Executing Day 2 Actions with the vRA 7 REST API](http://www.vmtocloud.com/executing-day-2-actions-with-the-vra-7-rest-api/) blog post.

## Changing a Lease

You use the catalog service to change a lease. For actions that require user input, you may need to edit the template prior to submitting the request. 

### About this task

This command leverages the links for the change lease action from the command used in the [Syntax for Navigating to the Children of a Deployed Resource](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-F43B3AEB-7F0C-434F-A124-406D218ED918.html#GUID-F43B3AEB-7F0C-434F-A124-406D218ED918) example. 

```text
{
   "@type": "link",
   "rel": "GET Template: {com.vmware...iaas.proxy.provider@resource...ChangeLease}",
   "href": "https://$vRA/api/consumer/resources/dd3...a4a/actions/b5739e30-.../requests/template"
},
{
   "@type": "link",
   "rel": "POST: {com.vmware...iaas.proxy.provider@resource.action.name.machine.ChangeLease}",
   "href": "https://$vRA/api/consumer/resources/dd3...a4a/actions/b5739e30-.../requests"
},
```

### Procedure

1. Get the template for the resource action request. 

   ```text
   $curl --insecure -s -H" Content-Type: multipart/form-data" -H "Authorization: Bearer $token" https://$vRA/catalog-service/api/consumer/resources/dd37b7a1-829c-4773-b5be-b229453eca4a/actions/b5739e30-871d-48c7-9012-f2a7cf431dc1/requests/template
   ```

2. Examine the response. 

   ```text
   HTTP/1.1 200 OK   
   Server: Apache-Coyote/1.1   
   Cache-Control: no-cache, no-store   
   Pragma: no-cache   
   Expires: Sat, 01 August 2015 23:04:50 GMT
   Content-Type: application/json;charset=UTF-8   
   Date: Sat, 01 August 2015 13:04:50 GMT
   {
     "type": "com.vmware.vcac.catalog.domain.request.CatalogResourceRequest",
     "resourceId": "dd37b7a1-829c-4773-b5be-b229453eca4a",
     "actionId": "b5739e30-871d-48c7-9012-f2a7cf431dc1",
     "description": null,
     "data": {"provider-ExpirationDate": "2015-07-29T16:44:13.846Z"}
   }
   ```

3. Edit the template as desired. The template is populated with default values. In this example, the value of provider-ExpirationDate is set to the time at which the template was requested in UTC. Edit this value \(for example, to change the expiration to a month from now\). 
4. Use a POST command to send the template without modification to the corresponding URI. 

   ```text
   $curl --insecure -s -H" Content-Type: multipart/form-data" -H "Authorization: Bearer $token" https://$vRA/catalog-service/api/consumer/resources/dd37b7a1-829c-4773-b5be-b229453eca4a/actions/b5739e30-871d-48c7-9012-f2a7cf431dc1/requests
   Accept: application/json
   Content-Type: application/json 
   Authorization: Bearer $token
   {
     "type": "com.vmware.vcac.catalog.domain.request.CatalogResourceRequest",
     "resourceId": "dd37b7a1-829c-4773-b5be-b229453eca4a",
     "actionId": "b5739e30-871d-48c7-9012-f2a7cf431dc1",
     "description": null,
     "data": {"provider-ExpirationDate": "2015-08-29T16:44:13.846Z"}
   }
   ```

### Results

This POST command returns a response indicating success or failure, such as `HTTP/1.1 201 CREATED` for success.

## Catalog Service Examples for Managing Provisioned Deployments

### Getting Deployment Details

GET /api/consumer/requests/{id}/resourceViews retrieves resources provisioned by a given request. 

#### Accessing Links to Provisioned Items

You can access links to provisioned items from a given request by appending `/resourceViews` to the request details URI. For instance, you can edit the example request URI from as follows: 

```text
http://$vRA/catalog-service/api/consumer/requests/$requestId/resourceViews
```

In addition to the general information about the provisioned deployment returned in the response, such as its name, description and ID, the response contains additional HATEOAS links. 

<table>
  <thead>
    <tr>
      <th style="text-align:left">HATEOAS Link Deployment Details Functions</th>
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
      <td style="text-align:left">URI to get the catalog item details from which this catalog item was provisioned.
        See <a href="https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-D4D6665B-EA83-479A-B732-DB9EDAC185E0.html#GUID-D4D6665B-EA83-479A-B732-DB9EDAC185E0">Syntax for Viewing Details of a Machine Request</a>.</td>
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
        on this resource. Typically, on a deployment, the action will be Delete.</td>
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
      <td style="text-align:left">If the deployment contains child resources, such as nodes specified in
        the composite blueprint, this is the URI to get a list of the <code>resourceViews</code> for
        the children of this deployment.</td>
    </tr>
  </tbody>
</table>#### Input

Use the supported input parameters to control the command output. 

| Parameter  | Description  |
| :--- | :--- |
| URL  | https://$vRA/catalog-service/api/consumer/resources/$resourceId  |
| $vRA | Specifies the appliance name and fully qualified domain name, or IP address of the vRealize Automation server.  |
| $token | Specifies a valid HTTP bearer token with necessary credentials.  |
| id  | UUID of a request.  |
| page  | Specifies a page number.  |
| limit  | Specifies the number of entries to display on a page.  |
| $orderby | Specifies how to order multiple comma-separated properties sorted in ascending or descending order.  |
| $top | Specifies the number of returned entries from the top of the response \(total number per page in relation to skip\).  |
| $skip | Specifies the number of entries to skip.  |
| filter | Contains a Boolean expression to determine if a particular entry is included in the response.  |

#### Output

The command output contains property names and values based on the command input parameters. Note:

Price is referred to as `cost` in API commands and output. 

| Output Parameters |  |
| :--- | :--- |
| Property  | Description  |
| resourceId  | The unique identifier of the resource.  |
| iconId  | Specifies an icon for this request based on the requested object type.  |
| name  | The user friendly name of the resource.  |
| description  | An extended user friendly description of the resource.  |
| status  | The status of the resource. For example, On, Off, etc.  |
| catalogItemId  | The identifier of the catalog item associated with this provisioned resource.  |
| catalogItemLabel  | The label of the catalog item associated with this provisioned resource.  |
| requestId  | The unique identifier of the request that created this provisioned resource.  |
| businessGroupId  | The unique identifier of the business group that owns this resource.  |
| tenantId  | The unique identifier of the tenant that owns this resource.  |
| owners  | The owner of this resource.  |
| resourceType  | The type identifier of this resource. For example, Virtual Machine.  |
| parentResourceId  | The unique identifier of the parent resource. Used for child resources of a multi-machine resource.  |
| hasChildren  | Returns trun if this resource has child resources. Used if this is a multi-machine resource.  |
| dateCreated  | The date and time at which the resource was created.  |
| lastUpdated  | The date and time at which the resource was most recently modified.  |
| lease  | The current lease of the resource.  |
| costs  | An optional rate card of the prices and charges levied against the resource. This parameter is deprecated.  |
| costToDate  | An optional rate card of the existing prices and charges levied against the resource. This parameter is deprecated  |
| totalCost  | An optional rate card of the pricess and charges levied for the entire lease period. This parameter is deprecated.  |
| expenseMonthToDate  | The expense of the resource from the beginning of the month to the current date.  |
| data  | The extended, provider defined properties of the resource.  |

#### Example Curl Command

This example retrieves all children of the resource with an ID of 7aaf9baf-aa4e-47c4-997b-edd7c7983a5b. 

```text
$curl --insecure -s -H" Content-Type: multipart/form-data" -H "Authorization: Bearer $token" http://$vRA/catalog-service/api/consumer/requests/7aaf9baf-aa4e-47c4-997b-edd7c7983a5b/resourceViews
```

#### JSON Output

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

### Navigating to the Children of a Deployed Resource

GET /api/consumer/resourceViews retrieves a list of the child nodes of a deployment, including virtual machines, networks, and other objects you may have configured on the design canvas. 

#### Using the REST API to Get Additional Deployment Information

In addition to general information about the provisioned resource, the response contains additional HATEOAS links that enable you to obtain additional details and information about each returned child resource. 

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
      <td style="text-align:left">GET: Parent Resource</td>
      <td style="text-align:left">URI to get the <code>resourceView</code> for the parent item. See <a href="https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-3C2A8DEF-FF41-4046-A755-464A3280CFF0.html#GUID-3C2A8DEF-FF41-4046-A755-464A3280CFF0">Syntax for Getting Deployment Details</a>.</td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p>GET:Template</p>
        <p>{com.vmware.csp.component.cafe.composition@resource.action.deployment.$actionName</p>
      </td>
      <td style="text-align:left">URI to get a template request for a specific action that you can perform
        on this resource.</td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p>POST:</p>
        <p>{com.vmware.csp.component.cafe.composition@resource.action.deployment.$actionName</p>
      </td>
      <td style="text-align:left">URI to which to post the request to perform an action, based on the corresponding
        template.</td>
    </tr>
  </tbody>
</table>#### Input

Use the supported input parameters to control the command output. 

| Parameter  | Description  |
| :--- | :--- |
| URL  | https://$vRA/catalog-service/api/consumer/resources/$resourceId  |
| $vRA | Specifies the appliance name and fully qualified domain name, or IP address of the vRealize Automation server.  |
| $token | Specifies a valid HTTP bearer token with necessary credentials.  |
| $resourceID | Specifies a resource ID. See [Syntax for Getting Deployment Details](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-3C2A8DEF-FF41-4046-A755-464A3280CFF0.html#GUID-3C2A8DEF-FF41-4046-A755-464A3280CFF0) to view all of your requests and search for a request ID.  |
| managedOnly | If true, the returned requests are from the user's managed subtenants.  |
| page | Specifies a page number.  |
| limit | Specifies the number of entries to display on a page.  |
| $orderby | Specifies how to order multiple comma-separated properties sorted in ascending or descending order.  |
| $top | Specifies the number of returned entries from the top of the response \(total number per page in relation to skip\).  |
| $skip | Specifies the number of entries to skip.  |
| filter | Contains a Boolean expression to determine if a particular entry is included in the response.  |

#### Output

The command output contains property names and values based on the command input parameters. Note:

Price is referred to as `cost` in API commands and output. 

| Output Parameters |  |
| :--- | :--- |
| Property  | Description  |
| resourceId  | The unique identifier of the resource.  |
| iconId  | Specifies an icon for this request based on the requested object type.  |
| name  | The user friendly name of the resource.  |
| description  | An extended user friendly description of the resource.  |
| status  | The status of the resource. For example, On, Off, etc.  |
| catalogItemId  | The identifier of the catalog item associated with this provisioned resource.  |
| catalogItemLabel  | The label of the catalog item associated with this provisioned resource.  |
| requestId  | The unique identifier of the request that created this provisioned resource.  |
| businessGroupId  | The unique identifier of the business group that owns this resource.  |
| tenantId  | The unique identifier of the tenant that owns this resource.  |
| owners  | The owner of this resource.  |
| resourceType  | The type identifier of this resource. For example, Virtual Machine.  |
| parentResourceId  | The unique identifier of the parent resource. Used for child resources of a multi-machine resource.  |
| hasChildren  | Returns trun if this resource has child resources. Used if this is a multi-machine resource.  |
| dateCreated  | The date and time at which the resource was created.  |
| lastUpdated  | The date and time at which the resource was most recently modified.  |
| lease  | The current lease of the resource.  |
| costs  | An optional rate card of the prices and charges levied against the resource. This parameter is deprecated.  |
| costToDate  | An optional rate card of the existing prices and charges levied against the resource. This parameter is deprecated.  |
| totalCost  | An optional rate card of the prices and charges levied for the entire lease period. This parameter is deprecated.  |
| expenseMonthToDate  | The expense of the resource from the beginning of the month until the current date. This value is updated daily by vRealize Business for Cloud.  |
| data  | The extended, provider defined properties of the resource.  |

#### Example Curl Command

This example retrieves all children of the resource with an ID of c4d3db3e-e397-44ff-a1c9-0ecebdba12f4%27. 

```text
$curl --insecure -s -H" Content-Type: multipart/form-data" -H "Authorization: Bearer $token" https:// $vRA/catalog-service/api/consumer/resourceViews?managedOnly=false&withExtendedData=true&withOperations=true&%24filter=parentResource%20eq%20%27c4d3db3e-e397-44ff-a1c9-0ecebdba12f4%27
```

#### JSON Output

The validation output displays the validation status of each content item within the package. 

```text
{
    "links": [],
    "content": [
        {
            "@type": "CatalogResourceView",
            "links": [
                {
                    "@type": "link",
                    "rel": "GET: Parent Resource",
                    "href": "https://$vRA/catalog-service/api/consumer/resourceViews/c4d3db3e-e397-44ff-a1c9-0ecebdba12f4"
                },
                {
                    "@type": "link",
                    "rel": "GET Template: {com.vmware.csp.component.iaas.proxy.provider@resource.action.name.machine.ChangeLease}",
                    "href": "https://$vRA/catalog-service/api/consumer/resources/dd37b7a1-829c-4773-b5be-b229453eca4a/actions/b5739e30-871d-48c7-9012-f2a7cf431dc1/requests/template"
                },
                {
                    "@type": "link",
                    "rel": "POST: {com.vmware.csp.component.iaas.proxy.provider@resource.action.name.machine.ChangeLease}",
                    "href": "https://$vRA/catalog-service/api/consumer/resources/dd37b7a1-829c-4773-b5be-b229453eca4a/actions/b5739e30-871d-48c7-9012-f2a7cf431dc1/requests"
                },
                {
                    "@type": "link",
                    "rel": "GET Template: {com.vmware.csp.component.iaas.proxy.provider@resource.action.name.machine.PowerOff}",
                    "href": "https://$vRA/catalog-service/api/consumer/resources/dd37b7a1-829c-4773-b5be-b229453eca4a/actions/02bad06d-f92b-4cf8-b964-37bb5d57be38/requests/template"
                },
                {
                    "@type": "link",
                    "rel": "POST: {com.vmware.csp.component.iaas.proxy.provider@resource.action.name.machine.PowerOff}",
                    "href": "https://$vRA/catalog-service/api/consumer/resources/dd37b7a1-829c-4773-b5be-b229453eca4a/actions/02bad06d-f92b-4cf8-b964-37bb5d57be38/requests"
                }
            ],
            "resourceId": "dd37b7a1-829c-4773-b5be-b229453eca4a",
            "iconId": "cafe_default_icon_genericCatalogItem",
            "name": "DEMO-002",
            "description": null,
            "status": "On",
            "catalogItemId": null,
            "catalogItemLabel": null,
            "requestId": null,
            "resourceType": "{com.vmware.csp.component.iaas.proxy.provider@resource.type.registration.name.Infrastructure.Virtual}",
            "owners": [
                "Connie Summers"
            ],
            "businessGroupId": "c0683388-6db2-4cb5-9033-b24d15ad3766",
            "tenantId": "mycompany",
            "dateCreated": "2015-07-29T13:54:58.804Z",
            "lastUpdated": "2015-07-29T13:55:01.371Z",
            "lease": {
                "start": "2015-07-29T13:51:33.000Z"
            },
            "costs": {
                "leaseRate": {
                    "type": "moneyTimeRate",
                    "cost": {
                        "type": "money",
                        "currencyCode": "USD",
                        "amount": 0
                    },
                    "basis": {
                        "type": "timeSpan",
                        "unit": "DAYS",
                        "amount": 1
                    }
                }
            },
            "costToDate": {
                "type": "money",
                "currencyCode": "USD",
                "amount": 0
            },
            "totalCost": null,
            "parentResourceId": "c4d3db3e-e397-44ff-a1c9-0ecebdba12f4",
            "hasChildren": false,
            "data": {
                "ChangeLease": true,
                "ConnectViaRdp": true,
                "ConnectViaVmrc": true,
                "DISK_VOLUMES": [
                    {
                        "componentTypeId": "com.vmware.csp.component.iaas.proxy.provider",
                        "componentId": null,
                        "classId": "dynamicops.api.model.DiskInputModel",
                        "typeFilter": null,
                        "data": {
                            "DISK_CAPACITY": 6,
                            "DISK_INPUT_ID": "DISK_INPUT_ID1"
                        }
                    },
                    {
                        "componentTypeId": "com.vmware.csp.component.iaas.proxy.provider",
                        "componentId": null,
                        "classId": "dynamicops.api.model.DiskInputModel",
                        "typeFilter": null,
                        "data": {
                            "DISK_CAPACITY": 6,
                            "DISK_INPUT_ID": "DISK_INPUT_ID2"
                        }
                    }
                ],
                "Destroy": true,
                "EXTERNAL_REFERENCE_ID": "vm-38153",
                "Expire": true,
                "IS_COMPONENT_MACHINE": false,
                "MachineBlueprintName": "system_blueprint_vsphere",
                "MachineCPU": 1,
                "MachineDailyCost": 0,
                "MachineDestructionDate": null,
                "MachineExpirationDate": null,
                "MachineGroupName": "Demo Group",
                "MachineGuestOperatingSystem": null,
                "MachineInterfaceDisplayName": "vSphere (vCenter)",
                "MachineInterfaceType": "vSphere",
                "MachineMemory": 4096,
                "MachineName": "DEMO-002",
                "MachineReservationName": "vCenter55",
                "MachineStorage": 12,
                "MachineType": "Virtual",
                "NETWORK_LIST": [
                    {
                        "componentTypeId": "com.vmware.csp.component.iaas.proxy.provider",
                        "componentId": null,
                        "classId": "dynamicops.api.model.NetworkViewModel",
                        "typeFilter": null,
                        "data": {
                            "NETWORK_MAC_ADDRESS": "00:50:56:ba:6b:85",
                            "NETWORK_NAME": "VM Network SQA"
                        }
                    }
                ],
                "PowerOff": true,
                "Reboot": true,
                "Reconfigure": true,
                "Reprovision": true,
                "Reset": true,
                "SNAPSHOT_LIST": [],
                "Shutdown": true,
                "Suspend": true,
                "ip_address": "10.118.194.213",
                "machineId": "f3579990-a3c4-4e17-9593-1f8893636876"
            }
        },
        {
            "@type": "CatalogResourceView",
            "links": [
                {
                    "@type": "link",
                    "rel": "GET: Parent Resource",
                    "href": "https://$vRA/catalog-service/api/consumer/resourceViews/c4d3db3e-e397-44ff-a1c9-0ecebdba12f4"
                },
                {
                    "@type": "link",
                    "rel": "GET Template: {com.vmware.csp.component.network.service@resource.action.destroy.name,[{{com.vmware.csp.component.iaas.proxy.provider@network.network.type.registration.name.Infrastructure.Network.Network.Existing}}]}",
                    "href": "https://$vRA/catalog-service/api/consumer/resources/f735b57a-fe6f-4108-876f-1c1055ca2cec/actions/ec5c522d-7b5b-4d0b-b9f2-1aedf01a2f0c/requests/template"
                },
                {
                    "@type": "link",
                    "rel": "POST: {com.vmware.csp.component.network.service@resource.action.destroy.name,[{{com.vmware.csp.component.iaas.proxy.provider@network.network.type.registration.name.Infrastructure.Network.Network.Existing}}]}",
                    "href": "https://$vRA/catalog-service/api/consumer/resources/f735b57a-fe6f-4108-876f-1c1055ca2cec/actions/ec5c522d-7b5b-4d0b-b9f2-1aedf01a2f0c/requests"
                }
            ],
            "resourceId": "f735b57a-fe6f-4108-876f-1c1055ca2cec",
            "iconId": "cafe_default_icon_genericCatalogItem",
            "name": "Existing Network",
            "description": null,
            "status": null,
            "catalogItemId": null,
            "catalogItemLabel": null,
            "requestId": null,
            "resourceType": "{com.vmware.csp.component.iaas.proxy.provider@network.network.type.registration.name.Infrastructure.Network.Network.Existing}",
            "owners": [
                "Connie Summers"
            ],
            "businessGroupId": "c0683388-6db2-4cb5-9033-b24d15ad3766",
            "tenantId": "mycompany",
            "dateCreated": "2015-07-29T13:55:14.095Z",
            "lastUpdated": "2015-07-29T13:55:17.315Z",
            "lease": null,
            "costs": null,
            "costToDate": null,
            "totalCost": null,
            "parentResourceId": "c4d3db3e-e397-44ff-a1c9-0ecebdba12f4",
            "hasChildren": false,
            "data": {
                "Description": " ",
                "Name": "Existing Network"
            }
        }
    ],
    "metadata": {
        "size": 20,
        "totalElements": 2,
        "totalPages": 1,
        "number": 1,
        "offset": 0
    }
}
```

