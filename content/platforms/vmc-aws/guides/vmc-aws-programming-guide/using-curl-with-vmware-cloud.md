# Using Curl with VMware Cloud

You can use the VMware CloudTM on AWS REST interfaces to automate tasks in your data center, and to perform some tasks that are not yet available in the VMC Console. Topic below show use of the curl command to send REST calls to the VMC Consol

## Generating an Access Token

Before making a call to the VMware Cloud on AWS, you must request an OAuth refresh token, which authorizes your REST based programs to use the service.

Before you can generate the access token, you need to obtain an OAuth refresh token associated with your VMware Cloud on AWS account.

Procedure

1. Get your user OAuth Refresh Token.
   1. Log in to http://vmc.vmware.com using your My VMware credentials.
   2. Click next to your user name and organization at the top right of the screen to open the User/Organization Settings.
   3. Click OAuth Refresh Token. A Refresh Token is displayed in the list with date and time last used, created, and when it expires.
2. To generate the access token, issue a POST command to the following URL, replacing {oauth} with the OAuth refresh token from step 1.
3. When the POST command returns results in the form {"access-token": "token-string"}, copy and save the access token string for later use. This string is over 900 characters long, so be careful to avoid line breaks.

### Example: Generate an Access Token Using cURL

For example, to generate an authorization token, run the following command. The -X option sends a custom request \(not GET\). The -H option specifies that the HTTP header follows.

### What to do next

Pass the returned access token as part of the header in any subsequent REST calls you make. This is the only REST call run against the console.cloud cloud service provider . Subsequent calls are run against the vmc URL.

## Getting the Organization ID

Many calls to the VMware CloudTM on AWS API require an organization ID.  
Use the /vmc/api/orgs method to get the organization ID. For reference documentation about the REST

interfaces, go to the VMC Console, sign in, and click Developer Center &gt; API Explorer.Prerequisites

You must have an authentication token to get the organization ID.

### Procedure

1. 1  Issue a GET to https://vmc.vmware.com/vmc/api/orgs
2. 2  Paste in the token-string that you saved previously.

   The value returned for the id key is the organization ID.

### Example: Getting the Organization ID Using cURL

You can also get the organization ID by browsing to your SDDC and clicking View Details &gt; Support. To get the organization ID, use the following command.

`curl -X GET -H 'csp-auth-token: token-string' https://vmc.vmware.com/vmc/api/orgs`

## Getting SDDC IDs

API calls that operate on an single SDDC require the SDDC ID as a parameter.

Use the vmc/api/orgs/{org}/sddcs method to get the organization ID. For reference documentation about the REST interfaces, go to the VMC Console, sign in, and click Developer Center &gt; API Explorer.

### Prerequisites

In order to get the SDDC ID, you must have an authentication token and an organization ID.

### Procedure

Issue a GET to https://vmc.vmware.com/vmc/api/orgs/{org}/sddcs where {org\] is replaced by the organization ID for the organization containing your SDDCs.

The IDs for each SDDC are returned in the sddc\_id key.

### Example: Get SDDC IDs Using cURL

Use the following command to get SDDC IDs using cURL:

## Adding a Host Using the VMware Cloud on AWS API

If you have the capacity to add extra hosts to your SDDC, you can use the VMware CloudTM on AWS API to add a host.

Use the vmc/api/orgs/{org}/sddcs/{sddc}/esxs method to add a host. For reference documentation about the REST interfaces, go to the VMC Console, sign in, and click Developer Center &gt; API Explorer.

You can't add more hosts than the maximum number allowed in your SDDC.

### Prerequisites

You need an authorization token, an organization ID, and an ID for the SDDC to which you want to add a host.

### Procedure

Issue a POST to https://vmc.vmware.com/vmc/api/orgs/{org}/sddcs/{sddc}/esxs where {org\] is replaced by the organization ID for the organization and {sddc} is replaced by the ID for your SDDC. The body of the request should specify the number of hosts to create in the format {"num\_hosts":number}

A new host is provisioned and added to the SDDC cluster.

### Example: Add a Host to an SDDC Using cURL

To provision a single host in your SDDC, use the following command.

```text
curl -X POST -H 'csp-auth-token: token-string \
-H 'Accept: application/json' --header 'Content-Type: application/json' \
-d '{ "num_hosts": 1}' 'https://vmc.vmware.com/vmc/api/orgs/{org}/sddcs/{sddc}/esxs'
```

## Remove a Host Using the VMware Cloud on AWS API

You can remove a host as long as you are above the minimum number of hosts for your SDDC.

Use the vmc/api/orgs/{org}/sddcs/{sddc}/esxs/{esx} method to remove a host. For reference documentation about the REST interfaces, go to the VMC Console, sign in, and click Developer Center &gt; API Explorer.

### Prerequisites

You need an authorization token, an organization ID, and an ID for the SDDC to which you want to add a host.

### Procedure

u Issue a DELETE to https://vmc.vmware.com/vmc/api/orgs/{org}/sddcs/{sddc}/esxs/{esx} where {org\]is replaced by the organization ID for the organization, {sddc} is replaced by the ID for your SDDC, and {esx} is replaced by the ID for the host.

The specified host is removed from the SDDC.

### Example: Remove a Host Using cURL

To remove a host in your SDDC, use the following command.

```text
curl -X POST -H 'csp-auth-token: token-string \
-H 'Accept: application/json' --header 'Content-Type: application/json' \
-d '{ "num_hosts": 1}' 'https://vmc.vmware.com/vmc/api/orgs/{org}/sddcs/{sddc}/esxs'
curl -X DELETE -H 'csp-auth-token: token-string \
-H'Accept: application/json' 'https://vmc.vmware.com/vmc/api/orgs/\{org}/sddcs/{sddc}/esxs/{esx}'
```

