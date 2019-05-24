---
description:
draft: true
guide: VMware Cloud on AWS Programming Guide
include_toc: true
layout: vmc_aws_programming_guide
title: Using a REST Client with VMware Cloud on AWS
type: topic
toc: true
weight: 3
---
You can use the VMware Cloud on AWS interfaces with a REST client such as Postman to automate setup and maintenance tasks in your datacenter.

## Generating an Access Token from Refresh Token

Each user of VMware Cloud on AWS is assigned an OAuth refresh token, which can be used to authorize a short-term session and obtain an access token.

To obtain your OAuth refresh token, login to vmc.vmware.com where your SDDC is hosted. Click the pull- down menu on upper right of the VMC console screen, and click OAuth Refresh Token. Its GUID appears near top of the list, and is valid for many months unless regenerated or revoked.

To obtain an access token, called a csp-auth-token in subsequent commands, supply these fields in Postman or similar browser based REST client. Under parameters \(Params\) type refresh\_token for the key and your OAuth Refresh Token for its value. Under Headers, type Content-Type for the key andapplication/json for its value.

```text
POST
https://console.cloud.vmware.com/csp/gateway/am/api/auth/api-tokens/authorize
```

```text
refresh_token   YourOAuthRefreshToken
Content-Type    application/json
```

This pattern for Params and Headers will repeat in subsequent commands. POST and PUT commands also have a Body that will be supplied underneath Headers.

Click Send to run the REST command, and your access\_token appears below. You will need to copy its \(long\) value as the csp-auth-token for subsequent commands. The returned Body text includes theexpires\_in field, expressed in seconds. For example, 1799 indicates that your access\_token lasts a bit less than 30 minutes.

Note Keep the authorize tab open in Postman or other REST client, so you can refresh theaccess\_token after it expires, for use in subsequent tabs.

## Operations on Organizations

You can find the organizations you are associated with, get details about an organization, and determine the enabled cloud providers for an organization.

In Postman or another REST client, leave the tab open where you just ran the authorize command, and open a new tab. This makes it easier to copy and paste the access-token to provide as the value ofcsp-auth-token for subsequent commands.

To find the organization you are associated with, run the /orgs command. Click Headers, type csp- auth-token for the key, and as its value paste the access-token from the authorize command in the first tab.

The "id" field is the GUID of the organization you are associated with. You can also find this value in the VMC Console under the Support tab for your SDDC. If you are associated with multiple organizations, they will appear as a separate JSON block in the returned body.

To get details about the organization, open a new tab and run the /orgs command again, pasting the GUID of your organization in place of {org} in the following command. Click Headers, type csp-auth- token for the key, and as its value paste the access-token from the authorize command in the first tab.![page18image3886293600](blob:https://app.gitbook.com/46b58cd5-86ac-48cf-be0e-72f8d568a359)![page18image3886293936](blob:https://app.gitbook.com/68fa957b-179b-40f8-8a8d-89565aa8c12d)

```text
GET
https://vmc.vmware.com/vmc/api/orgs
```

csp-auth-token Paste-access-token-here

```text
...
"id": "91c13b70-c533-460c-9288-767200cecaf9"
```

```text
GET
https://vmc.vmware.com/vmc/api/orgs/{org}
```

csp-auth-token Paste-access-token-here

```text
...
"created": "2018-01-23T05:13:32.000277Z"
```

Among other information is the date when the organization was created, information about the cloud provider, host limit, and invitation code.

To determine the enabled cloud providers for the organization, open a new tab and run the /orgscommand again, pasting the GUID of your organization in place of {org} in the following command, and adding /providers at the end. Click Headers, type csp-auth-token for the key, and as its value paste the access-token from the authorize command in the first tab.

```text
GET
https://vmc.vmware.com/vmc/api/orgs/{org}/providers
```

csp-auth-token Paste-access-token-here\[

```text
    {
        "provider": "OURCLOUD",
        "regions": [
            "US_WEST_2",
            "US_EAST_1"
                ]
    } ]
```



## Operations on Subscriptions

Subscriptions can save you money by committing to buy a certain amount of capacity for a defined period. A subscription is not required. Any use of service not covered by subscription is charged according to on-demand pricing.

If you run GET subscriptions command before creating a subscription, the subscription shows as empty. You can discover your subscription offers with the following interface:

For that command and ones below, click Headers, type csp-auth-token for the key, and as its value paste the access-token from the authorize command in the initial tab.

Once you see the subscription offers, you can run a POST command to create a subscription according to offered terms. In the header, supply the access token and set content type to JSON. In the request body, choose the offer instance that you prefer, for example one shown below for 12 months.

```text
GET
https://https://vmc.vmware.com/vmc/api/orgs/{org}/offer-instances
```

```text
POST
https://vmc.vmware.com/vmc/api/orgs/{org}/subscriptions
```

```text
csp-auth-token  Paste-access-token-here
Content-Type    application/json
```

```text
{
  "offer_version": "1.0",
  "product_type": "host",
  "region": "US_WEST_2",
  "commitment_term": "12",
  "offer_name": "VMware Cloud on AWS",
  "quantity": 1
}
...
    "id": "56193746-9d94-4839-bc6c-907754bc3d1f",
```

In the returned body, the "id" value is the GUID of your subscription. To verify your subscription, run the following command to see its terms.

To delete your subscription and return to on-demand pricing, use the DELETE command with the same URL.

## Operations on Hosts and Clusters

For greater compute and storage capacity, you an add an ESXi host to a cluster in your SDDC, or for even greater capacity, you can add another cluster of four or more ESXi hosts. Each ESXi host has 2 CPUs, 512GB memory, and over 10TB storage. Each cluster contains four or more ESXi hosts.

Use the POST esxs command to add or remove and ESXi host. The choice is made by a query \(?\) string at the end of the URL, specifying an action to add or remove. In Headers, type csp-auth-token for the key, and as its value paste the access-token from the authorize command. In the Body, supply JSON text indicating the number of hosts to add, and the availability zone where it should be added.

```text
GET
https://vmc.vmware.com/vmc/api/orgs/{org}/subscriptions/{subscriptionID}
```

```text
DELETE
https://vmc.vmware.com/vmc/api/orgs/{org}/subscriptions/{subscriptionID}
```

```text
POST
https://vmc.vmware.com/vmc/api/orgs/{org}/sddcs/{sddc}/esxs?action=add
```

csp-auth-token Paste-access-token-hereContent-Type application/json

```text
{
  "num_hosts": 1,
  "availability_zone": "US-West"
}
...

"id": "1d2bc925-4e4d-455a-ae22-350dca953676"
```

After you run the add action, the GUID of the new ESXi host appears in the returned body.

To remove an ESXi host for a cluster in your SDDC, use a similar POST command with the remove action. You only need to specify num\_hosts, unless you have multiple clusters in your SDDC, then you need to specify the clusterId as well.

```text
POST
https://vmc.vmware.com/vmc/api/orgs/{org}/sddcs/{sddc}/esxs?action=add
```

csp-auth-token Paste-access-token-hereContent-Type application/json

```text
{
  "num_hosts": 1,
  "availability_zone": "US-West"
```

}

```text
,,,
   "id": "1d2bc925-4e4d-455a-ae22-350dca953676",
```

The GUID of the removed ESXi host appears in the returned body, with other information.

Use the POST cluster command to add a cluster. In Headers, type csp-auth-token for the key, and as its value paste the access-token from the authorize command. In the Body, supply JSON text indicating the number of hosts the cluster should contain \(4 or more\).

```text
POST
https://vmc.vmware.com/vmc/api/orgs/{org}/sddcs/{sddc}/clusters
```

csp-auth-token Paste-access-token-hereContent-Type application/json

```text
{
  "num_hosts": 4
}
...
"id": "9251d2bc-4e4d-455a-ae22-350dca953676"
```

The GUID of the added cluster appears in the returned body, with other information. You specify this GUID when you choose to delete the cluster.

Use the POST cluster command to add a cluster. In Headers, type csp-auth-token for the key, and as its value paste the access-token from the authorize command. In the URL, specify as {cluster} the GUID of the cluster to delete.

```text
DELETE
https://vmc.vmware.com/vmc/api/orgs/{org}/sddcs/{sddc}/clusters/{cluster}
```

csp-auth-token Paste-access-token-hereContent-Type application/json

## Operations on Tasks

You can get a list of recent tasks executed in your SDDC, with status started, finished, or failed. You can get the details of a specific task, and request to cancel it if possible.

To get a long list of recent tasks within your organization, run the tasks command. In Headers, type csp- auth-token for the key, and as its value paste the access-token from the authorize command. Also in headers, set Content-Type to application/json so the results body will be easy to read.

```text
GET
https://vmc.vmware.com/vmc/api/orgs/{org}/tasks
```

csp-auth-token Paste-access-token-hereContent-Type application/json

```text
...
   "id": "faa241ed-04cb-4685-b67e-d17f69f9bf67"
```

The list of recent tasks starts with general information about the SDDC. Following this is information about each separate task, enclosed in curly braces. The requesting user is listed, which may help you identify which task is which. Near the top of each task listing is an "id" line. This GUID the taskId, which can be used to get information about this task only, by pasting its value at the end of the URL. The taskId can also be used to request task cancellation, as in the following example. Cancel is the only action allowed currently.

```text
POST
https://vmc.vmware.com/vmc/api/orgs/{org}/tasks/{taskId}?action=cancel
```

csp-auth-token Paste-access-token-hereContent-Type application/json

```text
...
   "error_messages": [
```

```text
      "Cannot cancel task: faa241ed-04cb-4685-b67e-d17f69f9bf67 in FINISHED state."
   ],
```

## Operations on SDDCs

You can use the sddcs commands to provision a new SDDC, although this is something that might be easier to do from the VMC Console. You can get information about all SDDCs in your organization, or just one SDDC. You can delete an SDDC if you have permission to do so.

The org/sddcs command gets SDDC information for an entire organization, which might involve a lot of data. You can limit the information returned to an SDDC of interest. You can find your SDDC ID to substitute for {sddc} below by looking under the Support tab in VMC Console. In Headers, type csp- auth-token for the key, and as its value paste the access-token from the authorize command. Also in headers, set Content-Type to application/json so the results body will be easy to read.

In the returned body, the "name" of the SCCS corresponds with its name in the VMC Console. The four or more ESXi hosts that constitute the SDDC are listed as for the esxs command.

To provision a new SDDC, you must provide a certain amount of information in the request body, as in the example below. This is actually not enough information to create the SDDC without error, but it does create one with the message FAILED on the VMC Console.

```text
GET
https://vmc.vmware.com/vmc/api/orgs/{org}/sddcs/{sddc}
```

csp-auth-token Paste-access-token-hereContent-Type application/json

```text
POST
https://vmc.vmware.com/vmc/api/orgs/{org}/sddcs
```

csp-auth-token Paste-access-token-hereContent-Type application/json

```text
{
  "name": "SDK SDDC",
  "account_link_sddc_config": [
  {
  "customer_subnet_ids": ["string" ],
  "connected_account_id": "c0c61e9d-23f4-3c0d-9472-ba8c6b182766"
}],
"vxlan_subnet": "192.168.1.0/24",
"vpc_cidr": "string",
"provider": "ZEROCLOUD",
"sso_domain": "vmc.local",
"num_hosts": 4,
"deployment_type": "SingleAZ",
"region": "US_WEST_2"}
```

To delete the SDDC, find the "id" in the returned body, and copy it for use in the delete command.

Before you delete an SDDC, make sure that no tasks are running on it, and that it does not contain any data you will need.

## Operations on Public IP Addresses

Using the publicips commands, you can get the public IP addresses available to your SDDC, allocate a public IP address, attach it to a virtual machine, and free the IP address when no longer needed. Thepublicips commands are based on an AWS mechanism.

To list the public IP addresses available from AWS, run publicips command containing your organization's GUID and the GUID of your SDDC.

From the listed public IP addresses, allocate one for use by a virtual machine, as in this example.

To attach a public IP address to a virtual machine workload, use this unusual PATCH command.

```text
DELETE
https://vmc.vmware.com/vmc/api/orgs/{org}/sddcs/{sddc}
```

csp-auth-token Paste-access-token-hereContent-Type application/json

```text
GET
https://vmc.vmware.com/vmc/api/orgs/{org}/sddcs/{sddc}/publicips
```

```text
GET
https://vmc.vmware.com/vmc/api/orgs/{org}/sddcs/{sddc}/publicips/{id}
```

```text
PATCH
https://vmc.vmware.com/vmc/api/orgs/{org}/sddcs/{sddc}/publicips/{id}?action=attach
```

```text
{
  "public_ip": "41.8.9.10",
  "name": "vm-name",
  "allocation_id": "string",
  "dnat_rule_id": "string",
  "associated_private_ip": "10.0.0.10",
  "snat_rule_id": "string"
```

}

To free a public IP from its association with a virtual machine workload and your SDDC, use the DELETE command.

```text
DELETE
https://vmc.vmware.com/vmc/api/orgs/{org}/sddcs/{sddc}/publicips/{id}
```

The mgw/publicips commands operate in a similar manner on the management gateway instead of the compute load.

The dns/private and dns/public commands update the DNS record of management VMs to use private IP addresses or public IP addresses, respectively.
