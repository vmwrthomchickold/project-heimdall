---
author: VMware
draft: true
description:
guide: vSphere Automation SDK Programming Guide
include_toc: true
layout: vsphere_automation_sdk_programming_guide
title: Accessing a vSphere Automation Service
type: topic
toc: true
weight: 1
---

To access a vSphere Automation service, you must have a valid session connection. The sequence for accessing a vSphere Automation service includes creating a protocol connection object and using it to create the service stub. Prerequisites

Establish a connection to the vSphere Automation Endpoint URL. For more information about the authentication mechanisms that you can use, see [Authentication Mechanisms](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-FBE84F97-5AAC-4D08-9727-299CCC22C300.html#GUID-FBE84F97-5AAC-4D08-9727-299CCC22C300). Procedure

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">Create a protocol factory object.</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">
        <p>Create a protocol connection object to access an API provider.</p>
        <p>The vSphere Automation API clients use ApiProvider instances to invoke
          operations on services running in the virtual environment. To invoke an
          operation, you must specify the target service and operation, input parameters,
          and execution context.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left">Create a StubFactory object by using the ApiProvider instance.</td>
    </tr>
    <tr>
      <td style="text-align:left">4</td>
      <td style="text-align:left">Create a StubConfiguration instance and set the security context to be
        used for the service stub.</td>
    </tr>
    <tr>
      <td style="text-align:left">5</td>
      <td style="text-align:left">Create the stub for the vSphere Automation service interface by calling
        the create method of the StubFactory instance. Pass the service class and
        the StubConfiguration instance as arguments.</td>
    </tr>
  </tbody>
</table>
