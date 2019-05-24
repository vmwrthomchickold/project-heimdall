---
author: VMware
draft: true
description:
guide: vRealize Automation API Programming Guide
include_toc: true
layout: vrealize_automation_api_programming_guide
title: REST API Authentication
type: topic
toc: true
weight: 3
---

In the REST API, vRealize Automation requires HTTP bearer tokens in request headers for authentication of consumer requests. A consumer request applies to tasks that you can perform in the vRealize Automation console, such as requesting a machine.

To acquire an HTTP bearer token, you authenticate with an identity service that manages the communication with the SSO server. The identity service returns an HTTP bearer token that you include in all request headers until the token expires, or you delete it. An HTTP bearer token expires in 24 hours by default, but you can configure the token with a different duration.

## HTTP Bearer Tokens

You use HTTP bearer tokens for tasks that you can also perform in the vRealize Automation console. You create a request header with the curl command or with some other utility.

You use POST, HEAD, and DELETE methods to manage HTTP bearer tokens.

| Method  | URL  | Description  |
| :--- | :--- | :--- |
| POST  | /tokens  | Authenticate the user with the identity service /tokens and generate a new token.  |
| HEAD  | /tokens/tokenID | Validate the token tokenID.  |
| DELETE  | /tokens/tokenID | Delete the token tokenID.  |

Use the following root URL for HTTP bearer token calls:

```text
https://$vRA/identity/api/tokens
```

The variable $vRA represents the appliance name.domain name of the vRealize Automation server such as, vra-appliance-name.company.com.

## Configuring the Duration of an HTTP Bearer Token

You set the duration of HTTP bearer tokens in the /etc/vcac/security.properties file on the vRealize Automation appliance.

### About this Task

The effective duration or lifetime of an HTTP bearer token depends on the duration of its corresponding SAML token, which the SSO server creates at request time. An HTTP bearer token expires when it reaches the end of its configured duration, or at the end of the configured duration of the SAML token, whichever comes first. For example, if the configured duration is three days for the HTTP bearer token and two days for the SAML token, the HTTP bearer token expires in two days. A configuration setting on the SSO server determines the duration of SAML tokens.

### Prerequisites

* Log in to the vRealize Automation appliance with SSH as root. The password is the one you specified when you deployed the appliance.
* The /etc/vcac/security.properties file on the appliance must be editable.

### Procedure

1. Open the /etc/vcac/security.properties file for editing.
2. Add the following lines to the file, where N is an integer specifying the duration of the token in hours.

   ```text
   identity.basic.token.lifetime.hours=N
   #The number is in hours.
   ```

3. Save and close the file.
4. Log out of the vRealize Automation appliance.

### Results

The new value applies the next time someone requests an HTTP bearer token.

## Requesting an HTTP Bearer Token



You use an HTTP bearer token to authenticate a vRealize Automation REST API consumer request.

### About this task

A consumer request must specify the correct component registry service and resource. For example, the URL to obtain an HTTP bearer token must specify the identity service and token resource.

The token expires in 24 hours by default. See [Configure the Duration of an HTTP Bearer Token](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-FDB47B40-F651-4FBD-8D1E-AC6FC8FA7A96.html#GUID-FDB47B40-F651-4FBD-8D1E-AC6FC8FA7A96) for information on how to set the duration.

For details regarding input, output, and response codes, see [Syntax for Requesting an HTTP Bearer Token](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-6578E1DE-56C7-464D-9F5B-1497530CA7DA.html#GUID-6578E1DE-56C7-464D-9F5B-1497530CA7DA).

### Prerequisites

* Secure a channel between the web browser and the vRealize Automation server. Open a browser and enter the URL such as:

  ```text

  ```

  The system warns that your connection is not private. Click through to confirm the security exception and establish an SSL handshake.

* Log in to vRealize Automation using the applicable credentials. For example, to assign a user to a role, log in as a tenant administrator.
* Verify that the appliance name and fully qualified domain name of the vRealize Automation instance are available.

### Procedure

1. Enter the command to request the HTTP bearer token.

   ```text
   curl --insecure -H "Accept: application/json" -H 'Content-Type: application/json' --data '{"username":"vra-user@company.com","password":"vra-user-password","tenant":"company.com"}' https://$vRA/identity/api/tokens
   ```

   In this example, $vRA is an instance of vRealize Automation. The --insecure flag is included so that the request will return a response even if the traffic is not secured with a trusted certificate.

2. Examine the response.

   A successful request returns an HTTP bearer token that you include in subsequent API requests.

3. For convenience, store the token in a variable.

   ```text
   export token="EXAMPLE-TOKEN-TEXT"
   ```

### Token Request and Response

The following sample displays output based on the example request.

```text
curl --insecure -H "Accept: application/json" -H 'Content-Type: application/json' --data '{"username":"vra-user@company.com","password":"vra-user-password","tenant":"company.com"}' https://$vRA/identity/api/tokens
{"expires":"2017-04-14T04:46:43.000Z","id":"MTQ5Mj ... M2RmMA==","tenant":"company.com"}
```

The id is the bearer token to store for future use.

```text
export token="MTQ5Mj ... M2RmMA=="
```

If the credentials supplied in the Authorization header are invalid, the response includes status code 401 as in the following output.

```text
<!DOCTYPE html><html><head><title>Error report</title></head><body><h1>HTTP Status 401 - Authentication required</h1></body></html>
```

## Syntax for Requesting an HTTP Bearer Token

An HTTP bearer token is required by the REST client to use the vRealize Automation REST API. You obtain a bearer token by authenticating to the identity service.

### Input

Use the supported input parameters to control the command output.

| Parameter  | Description  |
| :--- | :--- |
| URL  | https://$vRA/identity/api/tokens  |
| $vRA | appliance name.domain name of the vRealize Automation server.  |
| usrname | Tenant administrator user name.  |
| passwd | Tenant administrator password.  |
| tenantURLtoken | Tenant URL token determined by the system administrator when creating the tenant such as, support.  |

### Output

The following information is displayed as a result of your HTTP bearer token request.

| Parameter  | Description  |
| :--- | :--- |
| expires | Contains the ISO 8601 timestamp indicating when the token expires.  |
| id | Contains the HTTP bearer token to use in Authorization header in subsequent requests.  |
| tenant | Displays the tenant ID associated with the token.  |

### Response Status Codes

One of the following codes are displayed as a result of your HTTP bearer token request.

| Status Code  | Description  |
| :--- | :--- |
| 200 OK  | Your request succeeded and the resource was updated. The response body contains the full representation of the resource.  |
| 400 BAD REQUEST  | The data you provided in the POST failed validation. Inspect the response body for details.  |
| 401 UNAUTHORIZED  | The request could not authenticate the user or authentication credentials required.  |

### curl Command to Request HTTP Bearer Token

The following example command requests an HTTP bearer token.

```text
curl --insecure -H "Accept: application/json" -H 'Content-Type: application/json' --data '{"username":"usrname","password":"passwd","tenant":"tenantURLtoken"}' https://$vRA/identity/api/tokens
```

When your request succeeds, the system returns the expiration date and time of the token, and the HTTP bearer token.

## Validating HTTP Bearer Tokens

You can validate an existing HTTP bearer token.

### Prerequisites

* [Request an HTTP Bearer Token](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-007C7087-2714-4A20-8055-D8520FCE26AD.html#GUID-007C7087-2714-4A20-8055-D8520FCE26AD).

### Procedure

1. Enter the command to validate the HTTP bearer token.

   ```text
   curl --insecure -I -H "Accept: application/json" -H "Authorization:  Bearer $token" -H "Cache-Control: no-cache" "https://$vRA/identity/api/tokens/$token"
   ```

2. Examine the response.

   A successful request returns status code 204.

### Validating the Token Request and Response

The following sample displays output based on the example request.

```text
curl --insecure -I -H "Accept: application/json" -H "Authorization:  Bearer $token" -H "Cache-Control: no-cache" "https://$vRA/identity/api/tokens/$token"
HTTP/1.1 204
Cache-Control: no-cache, no-store, max-age=0, must-revalidate
Pragma: no-cache
Expires: 0
Strict-Transport-Security: max-age=31536000 ; includeSubDomains
X-XSS-Protection: 1; mode=block
X-Frame-Options: DENY
X-Content-Type-Options: nosniff
Content-Type: application/json;charset=UTF-8
Date: Thu, 13 Apr 2017 21:56:02 GMT
X-Frame-Options: SAMEORIGIN
```

The server returns one of the following status codes.

| Status Codes for Validate a Bearer Token |  |
| :--- | :--- |
| Status Code  | Description  |
| 204 NO CONTENT  | The request succeeded.  |
| 401 UNAUTHORIZED  | You must have authentication credentials to access the resource. All requests must be authenticated.  |
| 403 FORBIDDEN  | Your authentication credentials do not provide sufficient access to the resource.  |
| 404 NOT FOUND  | Could not locate the resource based on the specified URI.  |
| 405 METHOD NOT ALLOWED  | The HEAD method is not supported for the resource.  |
| 500 SERVER ERROR  | Could not create or update the resource because of an internal server error. |

## Deleting an HTTP Bearer Token

You can delete an HTTP bearer token.

### Prerequisites

* [Request an HTTP Bearer Token](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-007C7087-2714-4A20-8055-D8520FCE26AD.html#GUID-007C7087-2714-4A20-8055-D8520FCE26AD).

### Procedure

1. Enter the command to delete the HTTP bearer token, as in the following example.

   ```text

   ```

2. Examine the response.

   A successful request returns status code 204.

### Deleting the Token Request and Response

The following sample displays output based on the example request.

```text

```

The server returns one of the following status codes.

| Status Codes for Delete a Bearer Token |  |
| :--- | :--- |
| Status Code  | Description  |
| 204 NO CONTENT  | The request succeeded. The resource has been deleted.  |
| 401 UNAUTHORIZED  | You must have authentication credentials to access the resource. All requests must be authenticated.  |
| 403 FORBIDDEN  | Your authentication credentials do not provide sufficient access to the resource.  |
| 404 NOT FOUND  | Could not locate the resource based on the specified URI.  |
| 405 METHOD NOT ALLOWED  | The DELETE method is not supported for the resource.  |
| 500 SERVER ERROR  | Could not create or update the resource because of an internal server error.  |
