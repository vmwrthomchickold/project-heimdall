---
description:
draft: true
guide: VMware Cloud on AWS Programming Guide
include_toc: true
layout: vmc_aws_programming_guide
title: Overview of VMware Cloud on AWS for Developers
type: topic
toc: true
weight: 1
---
You can run REST commands for VMware Cloud on AWS in different ways, including:

* **Curl Commands** -- The curl command is always available on Linux and Mac OS X machines. On Windows you can install it with the Visual C++ redistributable package, or download it from various web sites such as https://curl.haxx.se. The name cURL means command URL. It sends commands to a URL, in this case sending REST commands with header, body, and parameters. One downside for VMware Cloud programming is that each curl command must include a very long authorization token.
* **Python** -- Python programming is a good way to use the REST interfaces with VMware Cloud. The program can store the authorization token, minimizing copy and paste overhead. You'll need the requests package for REST transmission, and the simplejson package to form JSON headers. A sample Python program is available on the https://code.vmware.com web site, in the code samples section. You can test the various APIs and make use of them to create your own Python programs.
* **PowerCLI** -- It is also possible to control VMC on AWS using PowerCLI cmdlets in the Windows PowerShell, or various namespaces in the vSphere Datacenter CLI \(DCLI\). For more information, go to the VMC Console and clickDeveloper Center &gt; Downloads.
* **Postman REST Client** -- The popular Postman REST client is now available as a free stand-alone application for Windows, Mac OS X, and Linux. It might be the easiest way to send REST commands to the VMware Cloud console. Postman started as a Chrome browser extension, like Firebug for Firefox. The Advanced REST Client is still available as a Chrome browser extension. The figure below shows the authorize command, which is run against the cloud service provider \(csp\). Subsequent REST commands will use the resulting access\_token when running against a software-defined data center in the VMC console.

Figure 1‐1. Advanced REST Client

![page6image3866724848](blob:https://app.gitbook.com/042e71e2-74d7-480a-b21d-3da4718a0c4e)

In addition to access\_token, the 200 OK response includes the passed-in refresh\_token, expiry time, and your permissions scope. A bearer token allows anyone in possession of the token to execute a task like anyone else possessing the token.

## VMware Cloud REST Interfaces

The table below lists the current REST interfaces, organized by command hierarchy.

Curly braces indicate substitutions, usually a GUID. For reference documentation about the REST interfaces, go to the VMC Console, sign in, and click Developer Center &gt; API Explorer. The APIs are organized by expandable categories.

<table>
  <thead>
    <tr>
      <th style="text-align:left">URL</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">
        <p>get /orgs
          <br />get /orgs/{org}
          <br />get /orgs/{org}/providers
          <br />delete /orgs/{org}/subscriptions/{subscription} get /orgs/{org}/subscriptions/{subscription}
          get /orgs/{org}/subscriptions
          <br />post /orgs/{org}/subscriptions
          <br />get /orgs/{org}/offer-instances
          <br />get /orgs/{org}/tasks
          <br />get /orgs/{org}/tasks/{taskId}</p>
        <p>post /orgs/{org}/tasks/{taskId} post /orgs/{org}/sddcs/{sddc}/clusters
          delete /orgs/{org}/sddcs/{sddc}/clusters/{cluster} post /orgs/{org}/sddcs/{sddc}/esxs
          get /orgs/{org}/sddcs post /orgs/{org}/sddcs delete /orgs/{org}/sddcs/{sddc}
          get /orgs/{org}/sddcs/{sddc} get /orgs/{org}/sddcs/{sddc}/publicips post
          /orgs/{org}/sddcs/{sddc}/publicips delete /orgs/{org}/sddcs/{sddc}/publicips/{id}
          get /orgs/{org}/sddcs/{sddc}/publicips/{id} patch /orgs/{org}/sddcs/{sddc}/publicips/{id}
          get /orgs/{org}/sddcs/{sddc}/mgw/publicips/{id} get /orgs/{org}/sddcs/{sddc}/mgw/publicips
          put /orgs/{org}/sddcs/{sddc}/dns/private put /orgs/{org}/sddcs/{sddc}/dns/public
          post /orgs/{org}/sddcs/{sddc}/convert get /orgs/{org}/account-link get
          /orgs/{org}/account-link/sddc-connections get /orgs/{org}/account-link/compatible-subnets
          post /orgs/{org}/account-link/compatible-subnets get /orgs/{org}/account-link/compatible-subnets-async
          post /orgs/{org}/account-link/compatible-subnets-async get /orgs/{org}/account-link/connected-accounts
          delete /orgs/{org}/account-link/connected-accounts/{pathId} post /orgs/{org}/account-link/map-customer-zones
          get /orgs/{org}/sddcs/{sddc}/networking/connectivity-tests post /orgs/{org}/sddcs/{sddc}/networking/connectivity-tests
          post /locale</p>
      </td>
      <td style="text-align:left">
        <p>Get organizations associated with calling user Get details of organization
          <br
          />Get enabled cloud providers for an organization Cancel a Subscription
          <br
          />Get a subscription
          <br />Get all subscriptions
          <br />Create a subscription
          <br />List all offers available for the specific product type in its region
          List all tasks for organization
          <br />Get task details</p>
        <p>Modify an existing task Add a cluster in the target SDDC Delete a cluster
          Add/Remove one or more ESX hosts in the target cloud List all the SDDCs
          of an organization Provision SDDC Delete SDDC Get SDDC List all public
          IPs for an SDDC Allocate public IPs for an SDDC Free one public IP for
          an SDDC Get one public IP for an SDDC Attach or detach a public IP to workload
          VM for an SDDC Get one public IP for the mgw of an SDDC List all public
          IPs for the mgw of an SDDC Update DNS of management VMs to use private
          IP addresses Update DNS of management VMs to use public IP addresses Convert
          one host SDDC to minimum hosts for a default SDDC Get a link for customer&apos;s
          account to start the linking process Get a list of SDDC connections for
          the customer&apos;s organization Get a customer&apos;s compatible subnets
          for account linking Set which subnet to link accounts and finish the linking
          process Task to get customer&apos;s compatible subnets for account linking
          Task to set customer&apos;s compatible subnets for account linking Get
          a list of connected accounts Delete a particular connected (linked) account.
          Create task to re-map customer&apos;s datacenters across zones Connection
          validation group result wrapper at task-params Retrieve metadata for connectivity
          tests Set locale for the session</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
    </tr>
  </tbody>
</table>### Finding API Reference Information

For more information about these REST interfaces, visit the VMware {Code} website athttps://code.vmware.com and click API Explorer. On the left side, under Language Bindings, click the button for REST. VMC for AWS should appear as one of the categories. The API reference is in a common VMware REST API format.

## VMware Cloud Networking APIs

The table below lists the REST interfaces for VMware Cloud Networking APIs \(preview mode\), used to set up a software defined network.

These VMware Cloud Networking APIs can be run from the public cloud. The public APIs are typically used for provisioning and initial connectivity \(day 0\).

Similar commands are available from your on-premises cloud as VMware NSX APIs. In this case, the URL starts as /api/4.0/ instead of /orgs/... and continues as below. The direct from premises VMware NSX APIs are used for recurring operations \(day 2\).

These are available as "preview" APIs and may change in the future. Curly braces indicate substitutions, usually a GUID.

## VMC Console API Explorer

The VMC Console offers a Developer Center tab to help DevOps and administrators automate functions in the VMware Cloud on AWS.

If enabled, the Developer Center tab appears to the right of the SDDCs, Subscriptions, and Activity Log tabs. After you click Developer Center, another set of tabs appears below.

* Overview gives an introduction to the VMC Console Developer Center.
* API Explorer provides reference information about the REST interfaces listed in Table 1 and Table 2.
* Code Samples shows many contributed programs in various languages to automate VMC operations.
* SDKs lists vSphere Automation SDKs with Github source and documentation on code.vmware.com.
* Downloads gives direct links to obtain the Datacenter CLI bundle and the PowerCLI for PowerShell.

In addition to providing reference information, API Explorer can also be used to execute REST commands within your SDDC. APIs are organized according to category. Click a category to show related APIs, as has been done for Subscriptions in the following figure.

At the top level, API Explorer is similar to the Swagger UI. When you click an item, it expands to show API description, possible responses, and a Try it out section with parameters and their value. Unlike some other interfaces, the API Explorer hides information in blue headings that expand when you click them. This makes it easy to browse long responses with unneeded data. Your organization ID is pre-filled so you don't need to provide it, and authentication is done automatically with auth\_token fetched for you.

It might be possible to perform all your SDDC and Cloud automation from the API Explorer, although in the long run, repetitive tasks are easier to perform with a program coded in Python, PowerCLI, or DCLI.

Underneath the VMware Cloud on AWS interfaces is another section, showing REST commands for VMware Cloud Services, in case you need one of them, such as OAuth Client operations.

## About Login and Authorization

After you login to your organization's SDDC, you can obtain an access key, an organization ID, and an OAuth refresh token.

If you are an organization member, you can login to your organization's SDDC as a user. Your user name is probably your login ID @ DNS domain name.

To obtain an access key, click the pull-down menu on upper right of the VMC console screen, and clickView My Profile. The access key appears at the bottom of your profile.

To obtain an organization ID, click VIEW DETAILS at bottom left of the SDDCs tab, then click Support. The Org ID is listed under Support Information.

## OAuth Refresh Token

To use REST commands against the SDDC, you'll need to obtain an OAuth refresh token. Click the pull- down menu on upper right of the VMC console screen, and click OAuth Refresh Token. Its GUID appears near top of the list, and is valid for six months unless revoked.

You use an OAuth refresh token to authorize actions per organization. The OAuth refresh token handles authorization more securely than an access key because it applies to only one organization, not across all organizations. The OAuth refresh token is required to access APIs of the cloud services platform \(csp\) and the VMware Cloud services \(vmc\).

The O in OAuth stands for open. OAuth is an open protocol for a secure authorization method with standard methods across platforms. For more information see https://oauth.net.

The OAuth refresh token is the same as refresh-token in the above screenshot showing the Chrome REST Client add-on.
