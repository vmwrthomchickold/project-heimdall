---
author: VMware
draft: true
description:
guide: CIM SMASH/Server Management API Programming Guide
include_toc: true
layout: test
title: Developing Client Applications for the CIM API
toc: true
type: topic
weight: 3
---
A basic CIM client that allows you to connect to a CIM server can be outlined as several steps that build on prior steps. Each step is explained and illustrated with pseudocode. You can expand this outline to create clients that allow you to manage the server.

The CIM client outline presented in this chapter shows a recommended general approach to accessing the CIM objects from the Interop namespace. This approach assumes no advance knowledge of the specifics of the CIM implementation. If your client is aware of items such as the Service URL and the namespaces used in the VMware implementation, see [Using the CIM Object Space](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/f98d554a-3f1d-452c-bf5e-74b6e48ab37a/720b45cc-9f94-488d-90ed-e924134308ab/doc/GUID-5227FF5C-C2E3-495D-BA68-0504B6EAEA0C.html) for more information about accessing specific objects in the Implementation namespace.

# CIM Server Ports

CIM servers are available for both CIM-XML and WS-Management protocols, and for both secured and non-secured HTTP connections. Select one of the ports that corresponds to the type of connection you want to make. [Port Numbers for CIM Client Connections](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/f98d554a-3f1d-452c-bf5e-74b6e48ab37a/720b45cc-9f94-488d-90ed-e924134308ab/doc/GUID-632DC141-2912-4504-A254-0DBEE710921C.html#GUID-632DC141-2912-4504-A254-0DBEE710921C__ID-3894-000001dc) shows the default port numbers used by the CIM servers.

# CIM Object Namespaces

To access a CIM object directly, you must know the namespace in which the object is stored. A managed server can have several CIM namespaces. This guide uses the Interop namespace and the Implementation namespace.

Most CIM objects are stored in the Implementation namespace. If you know the URL and the Implementation namespace in advance, you can enumerate objects directly by connecting to that namespace.

The Interop namespace contains a few CIM objects, particularly instances of CIM\_RegisteredProfile. One of these instances exists for each CIM profile that is fully implemented on the managed server.

CIM\_RegisteredProfile acts as a repository of information that can be used to identify and access objects in the Implementation namespace. For each registered CIM profile, the CIM server has an association that you can follow to move from the Interop namespace to the Implementation namespace.

Some profiles in the VMware implementation are only partially implemented. The implementation does not include all the mandatory properties and methods for those profiles. The Interop namespace does not contain instances of CIM\_RegisteredProfile for profiles that are only partially implemented. To access unregistered profiles, you must know the Implementation namespace.

# WS-Management Resource URIs

For WS-Management connections, the client must also specify a resource URI when accessing CIM objects. The URI represents an XML namespace associated with the schema definition.

The choice of URI depends on the class name of the CIM object. The prefix of the class name determines which URI the client must use. Table 2-3 shows which URI to use for each supported class name prefix.

| Class Name Prefix  | Resource URI \(Namespace only - link will not work in a browser\)  |
| :--- | :--- |
| VMware\_  | http://schemas.vmware.com/wbem/wscim/1/cim-schema/2/  |
| OMC\_  | http://schema.omc-project.org/wbem/wscim/1/cim-schema/2/  |
| CIM\_  | http://schemas.dmtf.org/wbem/wscim/1/cim-schema/2/  |

Note that the URIs given above do not resolve to a web page location. Although they look like a web address, they just represent a section of the CIM XML schema that you need to specify.

Example:

xmlns="http://schemas.vmware.com/wbem/wscim/1/cim-schema/2/"

See [http://www.w3schools.com/schema/schema\_example.asp](http://www.w3schools.com/schema/schema_example.asp) for more information about XML namespaces.

# Locating a Server with SLP

If you do not know the URL to access the WBEM service of the CIMOM on the ESXi machine, or if you do not know the namespace, use SLP to discover the service and the namespace before your client makes a connection to the CIMOM.

SLP-compliant services attached to the same subnet respond to a client SLP query with a Service URL and a list of service attributes. The Service URL returned by the WBEM service begins with the service type service:wbem:https:// and follows with the domain name and port number to connect to the CIMOM.

Among the attributes returned to the client is InteropSchemaNamespace. The value of this attribute is the name of the Interop namespace.

For more information about SLP, see the following links:

| ■  | [http://tools.ietf.org/html/rfc2608](http://tools.ietf.org/html/rfc2608) |
| :--- | :--- |
| ■  | [http://tools.ietf.org/html/rfc3059](http://tools.ietf.org/html/rfc3059) |

# CIM Ticket Automation

A CIM client must authenticate before it can access data or perform operations on an ESXi host. The client can authenticate in one of the following ways.

| ■  | Directly with the CIMOM on the managed host by supplying a valid user name and password for an account that is defined on the managed host. |
| :--- | :--- |
| ■  | With a sessionId that the CIMOM accepts in place of the user name and password. The sessionId \(called a “ticket”\) can be obtained by invoking the AcquireCimServicesTicket\(\) method on VMware vCenter™ Server. |

As a best practice, use CIM ticket authentication for servers managed by vCenter. If the managed host is operating in lockdown mode, the CIMOM does not accept new authentication requests from CIM clients. However, the CIMOM does continue to accept a valid ticket obtained from vCenter Server.

The ticket must be obtained by using the credentials of any user that has administrative privileges on vCenter Server. For more information about CIM ticket authentication, see the VMware technical note [CIM Authentication for Lockdown Mode](https://www.vmware.com/support/developer/cim-sdk/6.5/cim_smash_650_ticket_authentication.pdf).

# Active Directory Authentication on ESXi

ESXi hosts implement the Pluggable Authentication Module \(PAM\) framework, which can be configured to support authentication of Active Directory users. This feature is transparent to the CIM client. The client uses Active Directory authentication by supplying a user name and password that were previously entered into the Active Directory database.

System administrators can use the vSphere Client or the Web Services SDK to add an ESXi host to the Active Directory domain and to grant access rights to specific users. Hosts configured to use Active Directory authentication can also be configured to accept local users that have been granted access rights.

Make a Connection to the CIMOM

Before you can enumerate classes, invoke methods, or examine properties of the managed server, you must create a connection object in your client. The connection object manages the connection with the CIM server, accepts CIM methods by proxy, and passes them to the CIM server. The following pseudocode illustrates how to create a connection by using command-line parameters passed to the client.

To make a connection to the CIMOM Procedure

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">Collect the connection parameters from the environment.</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">
        <p>Create the connection object in the client.</p>
        <p>With some client libraries, creating a connection object in the client
          does not send a request to the CIMOM. A request is not sent until a method
          is called. To verify that such a client can connect to and authenticate
          with the server, see another use case, such as <a href="https://vdc-repo.vmware.com/vmwb-repository/dcr-public/f98d554a-3f1d-452c-bf5e-74b6e48ab37a/720b45cc-9f94-488d-90ed-e924134308ab/doc/GUID-263D0F60-D134-4E23-89E9-53C39FA003F0.html">Listing Registered Profiles in the CIM Implementation</a>.</p>
      </td>
    </tr>
  </tbody>
</table>
