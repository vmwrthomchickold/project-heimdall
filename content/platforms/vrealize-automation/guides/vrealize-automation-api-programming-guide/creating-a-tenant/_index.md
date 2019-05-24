---
author: VMware
draft: true
description:
guide: vRealize Automation API Programming Guide
include_toc: true
layout: vrealize_automation_api_programming_guide
title: Creating a Tenant
type: topic
toc: true
weight: 4
---
You use the identity service to create a tenant.

The identity service is comprised of two components: authentication and authorization. The authentication component manages tenants, groups, users, and identity stores. Creating a tenant is an authentication example.

Two use cases show how to create a tenant, either with parameters inline or with input values in a JSON file. After creating a tenant, you can use other service examples to perform additional authentication and authorization functions.

For general information about creating and working with tenants, see Configuring vRealize Automation in the [vRealize Automation information center](https://www.vmware.com/support/pubs/vrealize-automation-pubs.html).

## Prerequisites

Satisfy the following conditions before performing any tasks for this use case.

* Log in to vRealize Automation as a system administrator and a tenant administrator.
* Verify that the appliance name and fully qualified domain name of the vRealize Automation instance are available.
* Verify that you have a valid HTTP bearer token that matches your login credentials. See [REST API Authentication](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-05B9796E-1C20-4860-B77B-1E7F4BD7E556.html#GUID-05B9796E-1C20-4860-B77B-1E7F4BD7E556).

## Creating a Tenant with Inline Parameters

To create a tenant with parameters inline, you first display all available tenants then request a new tenant with input parameters specified inline.

### Prerequisites

In addition to the [Prerequisites for Creating a Tenant](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-4A7D456F-3C38-4781-9930-4C6B181859A3.html#GUID-4A7D456F-3C38-4781-9930-4C6B181859A3), verify that you have parameter values for the new tenant.

### Procedure

1. Use the identity service to display all the available tenants.

   ```text
   curl --insecure -H "Accept:application/json" -H "Authorization: Bearer $token" https://$vRA/identity/api/tenants
   ```

   For details regarding input and output of this sample, see [Syntax for Displaying Your Current Tenants](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-0A32B5C8-C915-4DFC-A9C5-1EB1AE3C6118.html#GUID-0A32B5C8-C915-4DFC-A9C5-1EB1AE3C6118).

2. Examine the response to verify that the tenant you plan to create is not listed.

   See the output of the request to display all tenants [Create a Tenant With Parameters Inline](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-C984C57E-F562-46DC-97AF-C9D467BB4047.html#GUID-C984C57E-F562-46DC-97AF-C9D467BB4047__example_058564C3FC174D7ABAB98B0C2E6703E8).

3. Submit a request for a new tenant with parameters inline.

   ```text
   curl -X PUT --insecure -H "Accept:application/json" -H "Content-Type: application/json" -H "Authorization: Bearer $token" https://$vRA/identity/api/tenants/rainpole --data '{"@type":"Tenant","id":"rainpole","urlName":"rainpole","name":"rainpoleTenant","description":"New Custom Tenant","contactEmail":"admin@vmware.com","defaultTenant":false}'For details regarding input and output of this sample, see Syntax for Requesting a New Tenant
   ```

4. Use the identity service to display all the available tenants again.

   ```text
   curl --insecure -H "Accept:application/json" -H "Authorization: Bearer $token" https://$vRA/identity/api/tenants
   ```

5. Examine the response to verify that the tenant you requested is listed.

   See the output of the request to verify the new tenant is created [Create a Tenant With Parameters Inline](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-C984C57E-F562-46DC-97AF-C9D467BB4047.html#GUID-C984C57E-F562-46DC-97AF-C9D467BB4047__example_058564C3FC174D7ABAB98B0C2E6703E8).

### Create a Tenant With Parameters Inline

The following sample output for [Step 1](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-C984C57E-F562-46DC-97AF-C9D467BB4047.html#GUID-C984C57E-F562-46DC-97AF-C9D467BB4047__step_FB085B7CC28441FB819D96DDE2DA7002) lists three tenants.

```text
curl --insecure -H "Accept:application/json" -H "Authorization: Bearer $token" https://$vRA/identity/api/tenants
{
   "links":[],
   "content"[
      {"@type":"Tenant",
       "id":"vsphere.local",
       "urlName":"vsphere.local",
       "name":"vsphere.local",
       "description":null,
       "contactEmail":null,
       "password":"",
       "defaultTenant":true},
      {"@type":"Tenant",
       "id":"qe",
       ...},
      {"@type":"Tenant",
       "id":"management",
       ...}
   ],
   "metadata":{"size":20,"totalElements":3,"totalPages":1,"number":1,"offset":0}
}
```

The following sample output for [Step 3](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-C984C57E-F562-46DC-97AF-C9D467BB4047.html#GUID-C984C57E-F562-46DC-97AF-C9D467BB4047__step_F7597EF217E143E3B14EBF85DD5E9F4F) shows that the tenant named rainpole has been created.

```text
curl -X PUT --insecure -H "Accept:application/json" -H "Content-Type: application/json" -H "Authorization: Bearer $token" https://$vRA/identity/api/tenants/rainpole --data '{"@type":"Tenant","id":"rainpole","urlName":"rainpole","name":"rainpoleTenant","description":"New Custom Tenant","contactEmail":"admin@vmware.com","defaultTenant":false}'
{
   "id":"rainpole",
   "urlName":"rainpole",
   "name":"rainpoleTenant",
   "description":"New Custom Tenant",
   "contactEmail":"admin@vmware.com",
   "defaultTenant":false
}
```

The following sample output for [Step 4](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-C984C57E-F562-46DC-97AF-C9D467BB4047.html#GUID-C984C57E-F562-46DC-97AF-C9D467BB4047__step_84BAF4B2195D4217A6C636E071047ED6) lists four tenants including rainpole.

```text
curl --insecure -H "Accept:application/json" -H "Authorization: Bearer $token" https://$vRA/identity/api/tenants
{
   "links":[],
   "content":[
      {"@type":"Tenant",
       "id":"vsphere.local",
       ...},
      {"@type":"Tenant",
       "id":"qe",
       ...},
      {"@type":"Tenant",
       "id":"management",
       ...},
      {"@type":"Tenant",
       "id":"rainpole",
       ...}
   ],
   "metadata":{"size":20,"totalElements":4,"totalPages":1,"number":1,"offset":0}
}
```

## Create a Tenant With a JSON File

To create a tenant with a JSON file, you first display all available tenants then request a new tenant with input parameters. The input parameters are specified in a separate JSON file that you call from the request.

### Prerequisites

In addition to the [Prerequisites for Creating a Tenant](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-4A7D456F-3C38-4781-9930-4C6B181859A3.html#GUID-4A7D456F-3C38-4781-9930-4C6B181859A3), verify that you have parameter values for the new tenant required for the JSON file input.

### Procedure

1. Use the identity service to display all the available tenants.

   ```text
   curl --insecure -H "Accept:application/json" -H "Authorization: Bearer $token" https://$vRA/identity/api/tenants
   ```

   For details regarding input and output of this sample, see [Syntax for Displaying Your Current Tenants](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-0A32B5C8-C915-4DFC-A9C5-1EB1AE3C6118.html#GUID-0A32B5C8-C915-4DFC-A9C5-1EB1AE3C6118).

2. Examine the response to verify that the tenant you plan to create is not listed.

   See the output of the request to display all tenants [Create a Tenant With a JSON File](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-C7203620-DD77-4AD4-A48E-C19E5B866500.html#GUID-C7203620-DD77-4AD4-A48E-C19E5B866500__example_058564C3FC174D7ABAB98B0C2E6703E8).

3. Create a JSON file for the new tenant request to call.

   The newTenant.json file contains information about the new tenant.

   ```text
   {
      "@type":"Tenant",
      "id":"rainpole",
      "urlName":"rainpole",
      "name":"rainpoleTenant",
      "description":"New Custom Tenant",
      "contactEmail":"admin@vmware.com",
      "defaultTenant":false
   }
   ```

4. Submit a request for a new tenant that calls the JSON file.

   ```text
   curl -X PUT --insecure -H "Accept:application/json" -H "Content-Type:application/json" -H "Authorization: Bearer $token" https://$vRA/identity/api/tenants/rainpole --data @C:/Temp/newTenant.json
   ```

   For details regarding input and output of this sample, see [Syntax for Requesting a New Tenant](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-93970D4C-FA76-499C-A07D-19E42ECBBCF5.html#GUID-93970D4C-FA76-499C-A07D-19E42ECBBCF5)

5. Use the identity service to display all the available tenants again.

   ```text
   curl --insecure -H "Accept:application/json" -H "Authorization: Bearer $token" https://$vRA/identity/api/tenants
   ```

6. Examine the response to verify that the tenant you requested is listed.

   See the output of the request to verify the new tenant is created [Create a Tenant With a JSON File](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-C7203620-DD77-4AD4-A48E-C19E5B866500.html#GUID-C7203620-DD77-4AD4-A48E-C19E5B866500__example_058564C3FC174D7ABAB98B0C2E6703E8).

### Create a Tenant With a JSON File

The following sample output for [Step 1](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-C7203620-DD77-4AD4-A48E-C19E5B866500.html#GUID-C7203620-DD77-4AD4-A48E-C19E5B866500__step_FB085B7CC28441FB819D96DDE2DA7002) lists three tenants.

```text
curl --insecure -H "Accept:application/json" -H "Authorization: Bearer $token" https://$vRA/identity/api/tenants
{
   "links":[],
   "content"[
      {"@type":"Tenant",
       "id":"vsphere.local",
       "urlName":"vsphere.local",
       "name":"vsphere.local",
       "description":null,
       "contactEmail":null,
       "password":"",
       "defaultTenant":true},
      {"@type":"Tenant",
       "id":"qe",
       ...},
      {"@type":"Tenant",
       "id":"management",
       ...}
   ],
   "metadata":{"size":20,"totalElements":3,"totalPages":1,"number":1,"offset":0}
}
```

The following sample output for [Step 4](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-C7203620-DD77-4AD4-A48E-C19E5B866500.html#GUID-C7203620-DD77-4AD4-A48E-C19E5B866500__step_F7597EF217E143E3B14EBF85DD5E9F4F), shows that the tenant named rainpole has been created.

```text
curl -X PUT --insecure -H "Accept:application/json" -H "Content-Type:application/json" -H "Authorization: Bearer $token" https://$vRA/identity/api/tenants/rainpole --data @C:/Temp/newTenant.json
{
   "id": "rainpole",
   "urlName":"rainpole",
   "name":"rainpoleTenant",
   "description":"New Custom Tenant",
   "contactEmail":"admin@vmware.com",
   "password":"",
   "defaultTenant":false
}
```

The following sample output for [Step 5](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-C7203620-DD77-4AD4-A48E-C19E5B866500.html#GUID-C7203620-DD77-4AD4-A48E-C19E5B866500__step_84BAF4B2195D4217A6C636E071047ED6) lists four tenants including rainpole.

```text
curl --insecure -H "Accept:application/json" -H "Authorization: Bearer $token" https://$vRA/identity/api/tenants
{
   "links":[],
   "content":[
      {"@type":"Tenant",
       "id":"vsphere.local",
       ...},
      {"@type":"Tenant",
       "id":"qe",
       ...},
      {"@type":"Tenant",
       "id":"management",
       ...},
      {"@type":"Tenant",
       "id":"rainpole",
       ...}
   ],
   "metadata":{"size":20,"totalElements":4,"totalPages":1,"number":1,"offset":0}
}
```

## Identity Service Examples for Creating a Tenant

Syntax for each service example lists input parameters, output parameters, and curl commands.

### Displaying Your Current Tenants

GET /api/tenants lists all the vRealize Automation tenants in your system.

#### Input

Use the supported input parameters to control the command output.

| Parameter  | Description  |
| :--- | :--- |
| URL  | https://$vRA/identity/api/tenants  |
| $vRA | Specifies the appliance name and fully qualified domain name, or IP address of the vRealize Automation server.  |
| $token | Specifies a valid HTTP bearer token with necessary credentials.  |

#### Output

The command output contains property names and values based on the command input parameters.

<table>
  <thead>
    <tr>
      <th style="text-align:left">Parameter</th>
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
              <li>Self refers to the object that was returned or requested. This parameter
                does not appear when you query a single profile.</li>
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
      <td style="text-align:left">Content</td>
      <td style="text-align:left">
        <p>Specifies an array of data rows, each of which represents one of the tenant
          objects returned in a pageable list. Each tenant object can contain the
          following information:</p>
        <ul>
          <li>Id: Specifies the unique tenant identifier.</li>
          <li>urlName: Specifies the name of the tenant as it appears in URLs.</li>
          <li>Name: Specifies the name of the tenant for display purposes.</li>
          <li>description: Specifies the long description of the tenant.</li>
          <li>contactEmail: Specifies the primary contact email address.</li>
          <li>Password: Unused</li>
          <li>defaultTenant: Is set to True if the corresponding tenant is the default
            tenant (vsphere.local).</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Metadata</td>
      <td style="text-align:left">
        <p>Specifies the following paging-related data:</p>
        <ul>
          <li>Size: Specifies the maximum number of rows per page.</li>
          <li>totalElement: Specifies the number of rows returned. This parameter is
            not output when you query for a single profile.</li>
          <li>totalPages: Specifies the total number of pages of data available.</li>
          <li>Number: Specifies the current page number.</li>
          <li>Offset: Specifies the number of rows skipped.</li>
          <li>Size: Specifies the maximum number of rows per page.</li>
          <li>totalElement: Specifies the number of rows returned. This parameter is
            not output when you query for a single profile.</li>
          <li>totalPages: Specifies the total number of pages of data available.</li>
          <li>Number: Specifies the current page number.</li>
          <li>Offset: Specifies the number of rows skipped.</li>
        </ul>
      </td>
    </tr>
  </tbody>
</table>#### curl Command to Display Current Tenants

The following example command displays all available tenants.

```text
curl --insecure -H "Accept:application/json" -H "Authorization: Bearer $token" https://$vRA/identity/api/tenants
```

The response in JSON lists the current tenants. Format the output to improve its readability. For information about formatting output, see [Filtering and Formatting REST API Information](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-85CEA1CE-7A05-438F-8FB7-DE40B3948879.html#GUID-85CEA1CE-7A05-438F-8FB7-DE40B3948879).

```text
{
   "links":[],
   "content"[
      {
         "@type":"Tenant",
         "id":"vsphere.local",
         "urlName":"vsphere.local",
         "name":"vsphere.local",
         "description":null,
         "contactEmail":null,
         "password":"",
         "defaultTenant":true
      },
      {
         "@type":"Tenant",
         "id":"qe",
         "urlName":"qe",
         "name":"QETenant",
         "description":"Precreated test tenant",
         "contactEmail":null,
         "password":"",
         "defaultTenant":false
      }
      {
         "@type":"Tenant",
         "id":"management",
         "urlName":"management",
         "name":"Management-ITTenant",
         "description":"Precreated test tenant",
         "contactEmail":null,
         "password":"",
         "defaultTenant":false
      }
   ],
   "metadata":{
      "size":20,
      "totalElements":3,
      "totalPages":1,
      "number":1,
      "offset":0
   }
}
```



### Requesting a New Tenant

PUT /api/tenants/{tenantId} submits a request to create or update a tenant. You can specify request parameters using JSON command line input or by calling an existing JSON file from the command line.

#### Input

Use the supported input parameters to control the command output.

| Parameter  | Description  |
| :--- | :--- |
| URL  | https://$vRA/identity/api/tenants/$tenantId --data @$inputFileName.json  |
| $vRA | Specifies the appliance name and fully qualified domain name, or IP address of the vRealize Automation server.  |
| $token | Specifies a valid HTTP bearer token with necessary credentials.  |
| $tenantId | Specifies the ID of the tenant.  |
| $tenantURL | Specifies the URL of the tenant.  |
| $tenantName | Specifies the name of the tenant.  |
| $description | Specifies a description of the tenant.  |
| $emailAddress | Specifies the contact email address for the tenant.  |
| $password | Optional password for the new tenant. If blank, no password is required.  |

#### JSON Input File Template

To simplify command line input, you can call a JSON file with input parameters from the command line. You create the JSON file using a text editor, replacing italicized variables in the following template with your specific values.

```text
{
   "@type" : "Tenant",
   "id" : "$tenantId",
   "urlName" : "$tenantURL",
   "name" : "$tenantName",
   "description" : "$description",
   "contactEmail" : "$emailAddress",
   "password": "$password",
   "defaultTenant" : false
}
```

#### Output

The command output contains property names and values based on the command input parameters.

<table>
  <thead>
    <tr>
      <th style="text-align:left">Parameter</th>
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
              <li>Self refers to the object that was returned or requested. This parameter
                does not appear when you query a single profile.</li>
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
      <td style="text-align:left">Content</td>
      <td style="text-align:left">
        <p>Specifies an array of data rows, each of which represents one of the tenant
          objects returned in a pageable list. Each tenant object can contain the
          following information:</p>
        <ul>
          <li>Id: Specifies the unique tenant identifier.</li>
          <li>urlName: Specifies the name of the tenant as it appears in URLs.</li>
          <li>Name: Specifies the name of the tenant for display purposes.</li>
          <li>description: Specifies the long description of the tenant.</li>
          <li>contactEmail: Specifies the primary contact email address.</li>
          <li>Password: Unused</li>
          <li>defaultTenant: Is set to True if the corresponding tenant is the default
            tenant (vsphere.local).</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Metadata</td>
      <td style="text-align:left">
        <p>Specifies the following paging-related data:</p>
        <ul>
          <li>Size: Specifies the maximum number of rows per page.</li>
          <li>totalElement: Specifies the number of rows returned. This parameter is
            not output when you query for a single profile.</li>
          <li>totalPages: Specifies the total number of pages of data available.</li>
          <li>Number: Specifies the current page number.</li>
          <li>Offset: Specifies the number of rows skipped.</li>
          <li>Size: Specifies the maximum number of rows per page.</li>
          <li>totalElement: Specifies the number of rows returned. This parameter is
            not output when you query for a single profile.</li>
          <li>totalPages: Specifies the total number of pages of data available.</li>
          <li>Number: Specifies the current page number.</li>
          <li>Offset: Specifies the number of rows skipped.</li>
        </ul>
      </td>
    </tr>
  </tbody>
</table>#### curl Command to Request a New Tenant With JSON File

The following sample newTenant.json file contains parameters for the tenant request.

```text
{ "@type" : "Tenant", "id" : "rainpole", "urlName" : "rainpole", "name" : "rainpoleTenant", "description" : "New Custom Tenant", "contactEmail" : null, "password": "", "defaultTenant" : true }The following example command requests a new tenant by calling the newTenant.json file.
```

```text
curl --insecure -H "Content-Type: application/json" -H "Authorization: Bearer $token" https://$vRA/identity/api/tenants/rainpole --data @C:\Temp\newTenant.json
```

#### curl Command to Request a New Tenant With Parameters Inline

The following example command requests a new tenant with input parameters specified inline.

```text
curl --insecure -H "Accept: application/json" -H "Content-Type: application/json" -H "Authorization: Bearer $token" https://$vRA/identity/api/tenants/rainpole --data '{"@type":"Tenant","id":"rainpole","urlName":"rainpole","name":"rainpoleTenant",
"description":"New Custom Tenant","contactEmail":null,"defaultTenant":false}'
```

### Requesting New Identity Stores

GET /api/tenants/{tenantId}/directories lists all available identity stores for a named vRealize Automation tenant, such as the default tenant vsphere.local.

#### Input

Use the supported input parameters to control the command output.

| Parameter  | Description  |
| :--- | :--- |
| URL  | https://$vRA/identity/api/tenants/$tenantId/directories  |
| $vRA | Specifies the appliance name and fully qualified domain name, or IP address of the vRealize Automation server.  |
| $token | Specifies a valid HTTP bearer token with necessary credentials.  |
| $tenantId | Specifies the ID of the tenant.  |

#### Output

The command output contains property names and values based on the command input parameters.

<table>
  <thead>
    <tr>
      <th style="text-align:left">Parameter</th>
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
              <li>Self refers to the object that was returned or requested. This parameter
                does not appear when you query a single profile.</li>
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
      <td style="text-align:left">Content</td>
      <td style="text-align:left">
        <p>Specifies an array of data rows, each of which represents one of the tenant
          objects returned in a pageable list. Each tenant object can contain the
          following information:</p>
        <ul>
          <li>Id: Specifies the unique tenant identifier.</li>
          <li>urlName: Specifies the name of the tenant as it appears in URLs.</li>
          <li>Name: Specifies the name of the tenant for display purposes.</li>
          <li>description: Specifies the long description of the tenant.</li>
          <li>contactEmail: Specifies the primary contact email address.</li>
          <li>Password: Unused</li>
          <li>defaultTenant: Is set to True if the corresponding tenant is the default
            tenant (vsphere.local).</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Metadata</td>
      <td style="text-align:left">
        <p>Specifies the following paging-related data:</p>
        <ul>
          <li>Size: Specifies the maximum number of rows per page.</li>
          <li>totalElement: Specifies the number of rows returned. This parameter is
            not output when you query for a single profile.</li>
          <li>totalPages: Specifies the total number of pages of data available.</li>
          <li>Number: Specifies the current page number.</li>
          <li>Offset: Specifies the number of rows skipped.</li>
          <li>Size: Specifies the maximum number of rows per page.</li>
          <li>totalElement: Specifies the number of rows returned. This parameter is
            not output when you query for a single profile.</li>
          <li>totalPages: Specifies the total number of pages of data available.</li>
          <li>Number: Specifies the current page number.</li>
          <li>Offset: Specifies the number of rows skipped.</li>
        </ul>
      </td>
    </tr>
  </tbody>
</table>#### curl Command to List All Identity Stores for the Tenant

The following example command lists the identity stores.

```text
curl --insecure -H "Accept: application/json" -H 'Content-Type: application/json' -H "Authorization: Bearer $token” https://$vRA/identity/api/tenants/MYCOMPANY/directories
```

The following JSON output is returned based on the command input.

```text
{
   "links":[],
   "content":[
      {
         "@type":"IdentityStore",
         "domain":"vcac.mycompany.com",
         "name":"openLDAPPromocom",
         "description":null,
         "alias":"promocom.com",
         "type":"LDAP",
         "userNameDn":"cn=promocomadmin,ou=promocom,dc=vcac,dc=mycompany,dc=com",
         "password":null,
         "url":"ldap://10.000.00.000:389",
         "groupBaseSearchDn":"ou=promocom,dc=vcac,dc=mycompany,dc=com",
         "userBaseSearchDn":"ou=promocom,dc=vcac,dc=mycompany,dc=com"
      },
      {
         "@type":"IdentityStore",
         "domain":"example.mycompany.com",
         "name":"openLDAPDemo",
         "description":null,
         "alias":"example.com",
         "type":"LDAP",
         "userNameDn":"cn=demoadmin,ou=demo,dc=example,dc=mycompany,dc=com",
         "password":null,
         "url":"ldap://10.000.00.000:389",
         "groupBaseSearchDn":"ou=demo,dc=example,dc=mycompany,dc=com",
         "userBaseSearchDn":"ou=demo,dc=example,dc=mycompany,dc=com"
      }
   ],
   "metadata":{
      "size":20,
      "totalElements":2,
      "totalPages":1,
      "number":1,
      "offset":0
   }
}
```

### Linking an Identity Store to the Tenant

PUT /api/tenants/{tenantId}/directories/{id} links an LDAP, Active Directory, or Native Active Directory identity store to the vRealize Automationtenant.

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
      <td style="text-align:left">https://$vRA/identity/api/tenants/$tenantId/directories/$domainName --data
        @$inputFileName.json</td>
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
      <td style="text-align:left">$tenantId</td>
      <td style="text-align:left">Specifies the ID of the tenant.</td>
    </tr>
    <tr>
      <td style="text-align:left">userId</td>
      <td style="text-align:left">Specifies the ID of the user in the form name@domain.</td>
    </tr>
    <tr>
      <td style="text-align:left">$domainAlias</td>
      <td style="text-align:left">Specifies the domain alias.</td>
    </tr>
    <tr>
      <td style="text-align:left">$domainName</td>
      <td style="text-align:left">Specifies the domain of the identity store.</td>
    </tr>
    <tr>
      <td style="text-align:left">$grpBaseSearchDn</td>
      <td style="text-align:left">Specifies the group search base Distinguished Name.</td>
    </tr>
    <tr>
      <td style="text-align:left">$identityStoreName</td>
      <td style="text-align:left">Specifies a description of the new tenant.</td>
    </tr>
    <tr>
      <td style="text-align:left">$password</td>
      <td style="text-align:left">Specifies the password.</td>
    </tr>
    <tr>
      <td style="text-align:left">$identityStoreType</td>
      <td style="text-align:left">
        <p>Specifies the identity store type for the tenant. The following values
          are supported:</p>
        <ul>
          <li>LDAP</li>
          <li>AD</li>
          <li>NATIVE_AD</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">$identityServerUrl</td>
      <td style="text-align:left">Specifies the URL of the identity server.</td>
    </tr>
    <tr>
      <td style="text-align:left">$usrBaseSearchDn</td>
      <td style="text-align:left">Specifies the user search base Distinguished Name.</td>
    </tr>
    <tr>
      <td style="text-align:left">$usrNameDn</td>
      <td style="text-align:left">Specifies the Distinguished Name for the login user.</td>
    </tr>
  </tbody>
</table>#### JSON Input File Template

Use this template to create a JSON input file. Replace the variables in the template with actual values in the file.

```text
{
	"alias": "$domainAlias",
	"domain": "$domainName",
	"groupBaseSearchDn": "$grpBaseSearchDn",
	"name": "$identityStoreName",
	"password": "$password",
	"type": "$identityStoreType",
	"url": "$identityServerUrl",
	"userBaseSearchDn": "$usrBaseSearchDn",
	"userNameDn": "$usrNameDn"
}
```

#### Output

The command output contains property names and values based on the command input parameters.

<table>
  <thead>
    <tr>
      <th style="text-align:left">Parameter</th>
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
              <li>Self refers to the object that was returned or requested. This parameter
                does not appear when you query a single profile.</li>
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
      <td style="text-align:left">Content</td>
      <td style="text-align:left">
        <p>Specifies an array of data rows, each of which represents one of the tenant
          objects returned in a pageable list. Each tenant object can contain the
          following information:</p>
        <ul>
          <li>Id: Specifies the unique tenant identifier.</li>
          <li>urlName: Specifies the name of the tenant as it appears in URLs.</li>
          <li>Name: Specifies the name of the tenant for display purposes.</li>
          <li>description: Specifies the long description of the tenant.</li>
          <li>contactEmail: Specifies the primary contact email address.</li>
          <li>Password: Unused</li>
          <li>defaultTenant: Is set to True if the corresponding tenant is the default
            tenant (vsphere.local).</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Metadata</td>
      <td style="text-align:left">
        <p>Specifies the following paging-related data:</p>
        <ul>
          <li>Size: Specifies the maximum number of rows per page.</li>
          <li>totalElement: Specifies the number of rows returned. This parameter is
            not output when you query for a single profile.</li>
          <li>totalPages: Specifies the total number of pages of data available.</li>
          <li>Number: Specifies the current page number.</li>
          <li>Offset: Specifies the number of rows skipped.</li>
          <li>Size: Specifies the maximum number of rows per page.</li>
          <li>totalElement: Specifies the number of rows returned. This parameter is
            not output when you query for a single profile.</li>
          <li>totalPages: Specifies the total number of pages of data available.</li>
          <li>Number: Specifies the current page number.</li>
          <li>Offset: Specifies the number of rows skipped.</li>
        </ul>
      </td>
    </tr>
  </tbody>
</table>#### curl Command to Link an Identity Store to a Tenant

The following sample ldap.json.txt file contains parameters for the tenant request.

```text
{
	"alias": "example.com",
	"domain": "example.mycompany.com",
	"groupBaseSearchDn": "ou=demo,dc=example,dc=mycompany,dc=com",
	"name": "openLDAPDemo",
	"password": "password",
	"type": "LDAP",
	"url": "ldap://10.000.00.000:389",
	"userBaseSearchDn": "ou=demo,dc=example,dc=mycompany,dc=com",
	"userNameDn": "cn=demoadmin,ou=demo,dc=example,dc=mycompany,dc=com"
}
```

The following example command links an identity store to a tenant by calling the example JSON text file.

```text
curl --insecure -H "Content-Type: application/json" -H "Authorization: Bearer $token” https://$vRA/identity/api/tenants/development/directories/example.mycompany.com
--data @C:\Temp\ldap.json.txt
```

The command also tests that vRealize Automation can connect to the identity store successfully. If the command finishes successfully,vRealize Automation succeeded in connecting to the identity store.

This response in JSON indicates that an identity store is successfully linked to the specified tenant.

```text
Request Headers
{
	   Content-Type = application/json
	         Accept = application/json
	 Content-Length = 413
	 Accept-Charset = big5, big5-hkscs, euc-jp, euc-kr, gb18030, gb2312, gbk,
ibm-thai, ibm00858, ibm01140, ibm01141, ibm01142, ibm01143, ibm01144, ibm01145,
ibm01146, ibm01147, ibm01148, ibm01149, ibm037, ibm1026, ibm1047, ibm273, ibm277,
ibm278, ibm280, ibm284, ibm285, ibm290, ibm297, ibm420, ibm424, ibm437, ibm500,
ibm775, ibm850, ibm852, ibm855, ibm857, ibm860, ibm861, ibm862, ibm863, ibm864,
ibm865, ibm866, ibm868, ibm869, ibm870, ibm871, ibm918, iso-2022-cn, iso-2022-jp,
iso-2022-jp-2, iso-2022-kr, iso-8859-1, iso-8859-13, iso-8859-15, iso-8859-2,
iso-8859-3, iso-8859-4, iso-8859-5, iso-8859-6, iso-8859-7, iso-8859-8, iso-8859-9,
jis_x0201, jis_x0212-1990, koi8-r, koi8-u, shift_jis, tis-620, us-ascii, utf-16,
utf-16be, utf-16le, utf-32, utf-32be, utf-32le, utf-8, windows-1250, windows-1251,
windows-1252, windows-1253, windows-1254, windows-1255, windows-1256, windows-1257,
windows-1258, windows-31j, x-big5-hkscs-2001, x-big5-solaris, x-compound_text,
x-euc-jp-linux, x-euc-tw, x-eucjp-open, x-ibm1006, x-ibm1025, x-ibm1046, x-ibm1097,
x-ibm1098, x-ibm1112, x-ibm1122, x-ibm1123, x-ibm1124, x-ibm1364, x-ibm1381,
x-ibm1383, x-ibm300, x-ibm33722, x-ibm737, x-ibm833, x-ibm834, x-ibm856, x-ibm874,
x-ibm875, x-ibm921, x-ibm922, x-ibm930, x-ibm933, x-ibm935, x-ibm937, x-ibm939,
x-ibm942, x-ibm942c, x-ibm943, x-ibm943c, x-ibm948, x-ibm949, x-ibm949c, x-ibm950,
x-ibm964, x-ibm970, x-iscii91, x-iso-2022-cn-cns, x-iso-2022-cn-gb, x-iso-8859-11,
x-jis0208, x-jisautodetect, x-johab, x-macarabic, x-maccentraleurope, x-maccroatian,
x-maccyrillic, x-macdingbat, x-macgreek, x-machebrew, x-maciceland, x-macroman,
x-macromania, x-macsymbol, x-macthai, x-macturkish, x-macukraine, x-ms932_0213,
x-ms950-hkscs, x-ms950-hkscs-xp, x-mswin-936, x-pck, x-sjis_0213, x-utf-16le-bom,
x-utf-32be-bom, x-utf-32le-bom, x-windows-50220, x-windows-50221, x-windows-874,
x-windows-949, x-windows-950, x-windows-iso2022jp
}
Response Headers
{
	           Date = Wed, 29 Oct 2014 22:41:57 GMT
	   Content-Type = application/json;charset=UTF-8
	 Content-Length = 0
	           Vary = Accept-Encoding,User-Agent
	     Keep-Alive = timeout=15, max=100
	     Connection = Keep-Alive
}
Successful
```

#### Unlinked Identity Store Error

If an identity store is not linked to the specified tenant, the response includes status code 400 such as in the following output.

```text
Command failed [Rest Error]: {Status code: 400}, {Error code: 90027} , {Error
Source: null}, {Error Msg: Cannot connect to the directory service.}, {System
Msg: 90027-Connection to directory service can’t be established}
```

To resolve the problem, correct the identity store and connection details in the JSON input file and rerun the command.

### Searching LDAP or Active Directory for a User

GET /api/tenants/{tenantId}/principals/{userId} searches the configured LDAP directory, Active Directory, or Native Active Directory for a user.

#### Input

Use the supported input parameters to control the command output.

| Parameter  | Description  |
| :--- | :--- |
| URL  | https://$vRA/identity/api/tenants/$tenantId/principals/$userId |
| $vRA | Specifies the appliance name and fully qualified domain name, or IP address of the vRealize Automation server.  |
| $token | Specifies a valid HTTP bearer token with necessary credentials.  |
| $tenantId | Specifies the ID of the tenant.  |
| $userId | Specifies the ID of the user in the form name@domain.  |

#### Output

The command output contains property names and values based on the command input parameters.

<table>
  <thead>
    <tr>
      <th style="text-align:left">Parameter</th>
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
              <li>Self refers to the object that was returned or requested. This parameter
                does not appear when you query a single profile.</li>
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
      <td style="text-align:left">@type</td>
      <td style="text-align:left">Specifies the user name.</td>
    </tr>
    <tr>
      <td style="text-align:left">firstName</td>
      <td style="text-align:left">Specifies the first name of the user.</td>
    </tr>
    <tr>
      <td style="text-align:left">lastName</td>
      <td style="text-align:left">Specifies the last name of the user.</td>
    </tr>
    <tr>
      <td style="text-align:left">description</td>
      <td style="text-align:left">Specifies the description of the user.</td>
    </tr>
    <tr>
      <td style="text-align:left">emailAddress</td>
      <td style="text-align:left">Specifies the email address of the user.</td>
    </tr>
    <tr>
      <td style="text-align:left">locked</td>
      <td style="text-align:left">Specifies the Boolean flag indicating if the user is locked out.</td>
    </tr>
    <tr>
      <td style="text-align:left">disabled</td>
      <td style="text-align:left">Specifies the Boolean flag indicating if the user is disabled.</td>
    </tr>
    <tr>
      <td style="text-align:left">principalId</td>
      <td style="text-align:left">Specifies the principal ID of the user in username@domain format.</td>
    </tr>
    <tr>
      <td style="text-align:left">tenantName</td>
      <td style="text-align:left">Specifies the name of tenant to which user belongs.</td>
    </tr>
    <tr>
      <td style="text-align:left">name</td>
      <td style="text-align:left">Specifies the first and last name concatenated.</td>
    </tr>
  </tbody>
</table>#### curl Command to Search LDAP or Active Directory for a User

The following example command queries the configured LDAP directory for a specific user.

```text
curl --insecure -H "Accept:text/xml" -H "Authorization: Bearer $token" https://$vRA/identity/api/tenants/$tenantId/principals/$userId
```

The following JSON output is returned based on the command input.

```text
{
   "links" : [ ],
   "content" : [
      {
         "@type" : "User",
         "firstName" : "Tony",
         "lastName" : "Anteater",
         "emailAddress" : "tony@example.mycompany.com",
         "locked" : false,
         "disabled" : false,
         "principalId" :
            {
               "domain" : "example.mycompany.com",
               "name" : "susan"
            },
         "tenantName" : "MYCOMPANY1",
         "name" : "Tony Anteater"
      }
   ]
}
```

### Assigning a User to a Role

PUT /api/authorization/tenants/{tenantId}/principals/{principalId}/scopes/{scopeId}/roles/{scopeRoleId} assigns a user to a role.

#### Input

Use the supported input parameters to control the command output.

| Parameter  | Description  |
| :--- | :--- |
| URL  | https://$vRA/identity/api/authorization/tenants/$tenantId/principals/$principalId/roles/roleId |
| $vRA | Specifies the appliance name and fully qualified domain name, or IP address of the vRealize Automation server.  |
| $token | Specifies a valid HTTP bearer token with necessary credentials.  |
| $tenantId | Specifies the ID of the tenant.  |
| $principalId | Specifies the ID of the user in name@domain format.  |
| $roleId | Specifies the ID of the user role.  |

#### curl Command to Assign a User to a Role

The following example command string submits a request to assign the user tony in the domain example.mycompany.com to the tenant administrator role. It provides empty braces for the required JSON payload. For more information about getting the user name and domain values, see [Syntax for Searching LDAP or Active Directory for a User](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-7EECB0E6-0C44-40D9-AF8D-1D0A42F02701.html#GUID-7EECB0E6-0C44-40D9-AF8D-1D0A42F02701) .

```text
curl --insecure -H "Content-Type: application/json" -H "Authorization: Bearer $token" "https://$vRA/identity/api/authorization/tenants/development/principals/susan@example.mycompany.com/roles/CSP_TENANT_ADMIN/" --data "{}"If the command is successful, the HTTP response body is empty except for a 204 No Content status statement.
```

### Displaying All Roles Assigned to a User

GET /api/authorization/tenants/{tenantId}/principals/{principalId}/roles displays all of the roles assigned to a user.

#### Input

Use the supported input parameters to control the command output.

| Parameter  | Description  |
| :--- | :--- |
| URL  | https://$vRA/identity/api/authorization/tenants/$tenantId/principals/$principalId/roles  |
| $vRA | Specifies the appliance name and fully qualified domain name, or IP address of the vRealize Automation server.  |
| $token | Specifies a valid HTTP bearer token with necessary credentials.  |
| $tenantId | Specifies the ID of the tenant.  |
| principalId | Specifies the ID of the user in the form name@domain.  |

#### Output

The command output contains property names and values based on the command input parameters.

| Parameter  | Description  |
| :--- | :--- |
| id  | Specifies the role ID.  |
| name  | Specifies the role name.  |
| description  | Specifies the role description.  |
| status  | Specifies the status of this role.  |
| assignedPermissions  | Specifies the set of permissions that are implied by this role assignment.  |

#### curl Command to Display all Roles Assigned to a User

The following example command lists all the roles that are assigned to tony@example.mycompany.com.

```text
curl --insecure -H "Content-Type: application/json" -H "Authorization: Bearer $token" https://$vRA/identity/api/authorization/tenants/development/principals/tony@example.mycompany.com/roles
```

The following JSON output is returned based on the command input.

```text
{
   "links" : [ ],
   "content" : [
      {
         "@type" : "SystemRole",
         "id" : "ABX_TENANT_ADMIN",
         "name" : "Tenant Administrator",
         "description" : "ABX Tenant Administrator",
         "assignedPermissions" : [ {
         "id" : "CATALOG_CONSUME_TENANT_MGMT",
         "name" : "Catalog Consume Tenant Management",
         "description" : "Consume services, resources and manage requests ... within a Tenant",
         "prereqAdminPermissions" : null
      },
      {
         "id" : "MY_TENANT_MANAGEMENT",
         "name" : "My Tenant Management",
         "description" : "Manage my tenant.",
         "prereqAdminPermissions" : null
      },
      {
         "id" : "CATALOG_AUTHOR_TENANT",
         "name" : "Catalog Tenant-level Author",
         "description" : "Create, update and publish services, catalog ... across a Tenant.",
         "prereqAdminPermissions" : null
      },
      {
         "id" : "GUI_MY_TENANT_MANAGEMENT",
         "name" : "My Tenant Administration User Interface",
         "description" : "Access my tenant administration GUI.",
         "prereqAdminPermissions" : null
      },
      {
         "id" : "CATALOG_ENTITLE_TENANT",
         "name" : "Catalog Tenant-level Entitlement Management",
         "description" : "Entitle services, catalog items and actions ... users within a tenant.",
         "prereqAdminPermissions" : null
      },
      {
         "id" : "FILE_EDIT_TENANT",
         "name" : "Manage Tenant Files",
         "description" : "Upload and delete files belonging to this tenant.",
         "prereqAdminPermissions" : null
      },
      {
         "id" : "TENANT_USER_DATA_MANAGEMENT",
         "name" : "Manage user data (requests, items, tasks etc) within a tenant.",
         "description" : "Manage user created objects belonging to the tenant.",
         "prereqAdminPermissions" : null
      },
      {
         "id" : "TENANT_ADMIN_ROLE_ASSIGNMENT",
         "name" : "Tenant Administrator Role Assignment",
         "description" : "Assign the tenant administrator role to other users.",
         "prereqAdminPermissions" : null
      },
      {
         "id" : "GUI_MY_TENANT_TUG_MANAGEMENT",
         "name" : "My Tenant Identity Stores, Groups and Users Administration User Interfaces",
         "description" : "Access my tenant identity stores, groups ... users administration GUIs.",
         "prereqAdminPermissions" : null
      }
   ],
   "metadata" : {
   "size" : 20,
   "totalElements" : 1,
   "totalPages" : 1,
   "number" : 1,
   "offset" : 0
   }
}
```
