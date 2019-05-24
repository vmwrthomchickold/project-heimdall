---
description:
draft: true
guide: VMware Cloud on AWS Programming Guide
include_toc: true
layout: vmc_aws_programming_guide
title: Preview Mode Networking and Security
type: topic
toc: true
weight: 4
---
VMware Cloud customers can connect their on-premises data centers to AWS using the direct connect service with VMware Cloud networking APIs.

## REST for Preview Mode Networking and Security

VMware Cloud on AWS provides public REST commands for setting up a cloud based datacenter, and direct mode REST commands for continuing networking and security operations.

These REST functions offer simplified access to control the management gateway \(MGW\), compute gateway \(CGW\), and logical networks. The REST functions pass JSON data back and forth. Sections below show the user of Curl commands.

Prerequisites
n Provisioned VMC SDDC
n For the public endpoint, VMC credentials with access tokenn For the direct endpoint, NSX Manager credentials
n REST client such as curl or browser based tool

## Authenticate API Endpoint to VMC

First obtain your refresh-token from the VMC Console. Click the v on upper right next to your name and organization. In the drop-down menu, click OAuth Refresh Token. Copy and save the Refresh Token, a 24 character alphanumeric string with hyphen separators.

### Generate an Access Token

Use the OAuth Refresh Token \(oauth below\) to generate an access token. Here you must connect to console.cloud instead of vmc.vmware.com.

This command returns a long string with token, bearer, and expiration at the end. The access token is at the beginning, in double quotes, following access\_token and colon. You will need this access token in subsequent REST commands, so copy and save the access token.

### Get NSX Edges ID

For this REST command, you need your organization ID and the cloud SDDC ID. Both of these are available in the VMC Console, in your SDDC listing under the Support tab. They are also available with REST commands as shown in the previous chapter. To get the management gateway \(MGW\) and compute gateway \(CGW\), also called NSX Edges, run this command, where you replace org with the organization ID and sddc with the SDDC ID. Provide the access\_token above as the csp-auth-token.

Here is an example of part of what might be returned by the command above.

curl -X POST -H "Content-Type: application/json" \ https://console.cloud.vmware.com/csp/gateway/am/api/auth/api-tokens/authorize?refresh\_token=oauth

curl -X GET -H 'csp-auth-token:{access-token}' -H 'Accept: application/json' \ 'https://vmc.vmware.com/vmc/api/orgs/org/sddcs/sddc/networks/4.0/edges

{

}

```text
"objectId":"edge-2",
"objectTypeName":"Edge",
"vsmUuid":"421EF988-82A1-E9BF-60A3-ACDDC6018DBA",
"nodeId":"a7be5498-9262-47c2-9dd6-dbfbbe8200fe",
"revision":30,
"type":{
```

```text
   "name":"Edge"
},
```

```text
"name":"SDDC-CGW-1-esg",
...
```

### Configure vCenter Public Access

Create the management gateway \(MGW\) Firewall rule to enable vCenter access.

### Request Public IP Address

Request additional public IP addresses for your VMC SDDC.

### Configure VPN for Management Gateway

Configure IPsec VPN for secure connectivity to on-premises.

### Validate Private API Endpoint

Authenticate to NSX API and retrieve edges.

The Networking and Security APIs are consistent between public and private endpoints. The differences in Authentication \(Basic Auth versus OAuth\) and API paths. You can use the same cloudadmin SSO user used to sign into vCenter Server and to authenticate with the NSX Private API endpoint.

1. Retrieve NSX Manager private IP.
2. Verify authentication through the Private Endpoint.
3. Retrieve Edges.

```text
curl -X GET -H 'csp-auth-token:{auth-token}' -H 'Accept: application/json' \
   https://vmc.vmware.com/vmc/api/orgs/{org}/sddcs/{sddc}
```

```text
curl -k -u cloudadmin:{nsxpassword} https://{nsxmanager-privateip}/api/versions
```

```text
curl -k -u cloudadmin:{nsxpassword} -X GET -H 'content-type: application/xml' \
   https://{nsxmanager-privateip}/api/4.0/edges
```

## Learning More About Network and Security APIs

The examples of Curl commands to control preview mode Networking and Security can be extended with further information available about VMware NSX APIs.

When you call preview mode Networking and Security APIs from VMware Cloud on AWS, you use the public endpoint. When you call the Networking and Security APIs from a private Cloud or data center, you are using the private endpoint. These endpoints are similar, with the following differences.

n Authentication of the public endpoint uses OAuth, while the private endpoint uses Basic Auth.
n API path names differ at the beginning, but not after these path name elements, public and private:

For more information about the individual preview mode Networking and Security APIs, you can search and find each of them in the NSX for vSphere API Guide, at the following URL:

https://docs.vmware.com/en/VMware-NSX-for-vSphere/6.3/nsx\_63\_api.pdf
