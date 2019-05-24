---
author: VMware
draft: true
description:
guide: vSphere Automation SDK Programming Guide
include_toc: true
layout: vsphere_automation_sdk_programming_guide
title: Accessing vSphere Automation Services
type: topic
toc: true
weight: 5
---
vSphere Automation SDK provides mechanisms for creating remote stubs to give clients access to vSphere Automation services.

The sequence of tasks you must follow to create a remote stub starts with creating a ProtocolFactory. You use the protocol factory object to create a ProtocolConnection. Connection objects provide the basis for creating stub interfaces to vSphere Automation services.

When you establish a connection to the vSphere Automation Endpoint, you can create a StubFactory object and a StubConfiguration object. With these objects, you can create the remote stub for the vSphere Automation service that you want to access.

The complete connection sequence also includes SSL truststore support and a temporary StubConfiguration that you use for SAML token authentication and session creation.

<table>
  <thead>
    <tr>
      <th style="text-align:left">SSL Handshake</th>
      <th style="text-align:left">
        <p>The vSphere Automation Endpoint ( https://host/api) is an SSL&#x2010;enabled
          service that requires client authentication during login. The SSL connection
          relies on certificate verification supported by the Java security architecture.
          The Java security architecture defines truststores for SSL connections.
          A truststore contains vCenter Single Sign-On credentials. You use a truststore
          to verify credentials from a vCenter Server instance.</p>
        <p>The vSphere Automation SDK for Java includes an SSL utility sample code
          that supports the creation of a truststore for the HTTP connection, com.vmware.vcloud.suite.samples.common.SslUtil.
          Note</p>
        <p>The vSphere Automation SDK for Java SSL utility creates an instance of
          the Java security certificate class X509TrustManager. This instance declares
          an override client-side method, checkServerTrusted, that accepts all HTTPS
          certificates. This method is suitable only for development environments.
          For a production environment, do not use the X509TrustManager override
          methods. Instead, set up a truststore for use by the default X509TrustManager
          implementation.</p>
      </th>
    </tr>
  </thead>
</table>

For greater security, use an external utility to create a certificate store:

```text
keytool -import -noprompt -trustcacerts \
-alias <alias name> \
-file <certificate file> \
-keystore <truststore filename> \
-storepass <truststore password>
```

# Accessing a vSphere Automation Service

To access a vSphere Automation service, you must have a valid session connection. The sequence for accessing a vSphere Automation service includes creating a protocol connection object and using it to create the service stub.

## Prerequisites

Establish a connection to the vSphere Automation Endpoint URL. For more information about the authentication mechanisms that you can use, see [Authentication Mechanisms](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-FBE84F97-5AAC-4D08-9727-299CCC22C300.html#GUID-FBE84F97-5AAC-4D08-9727-299CCC22C300).

## Procedure

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
