# Overview of the vRealize Automation REST API

The vRealize Automation REST API provides consumer, administrator, and provider-level access to the service catalog with the same services that support the vRealize Automation console user interface. You can perform vRealize Automation functions programmatically by using REST API service calls.

## REST API Services

The vRealize Automation REST API offers the following services and functions. 

<table>
  <thead>
    <tr>
      <th style="text-align:left">vRealize Automation</th>
      <th style="text-align:left"></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left">REST API Services</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left">Service</td>
      <td style="text-align:left">Description</td>
    </tr>
    <tr>
      <td style="text-align:left">Approval Service</td>
      <td style="text-align:left">Retrieve, create, update, and delete approval policies, policy types,
        policy instances, and policy requests.</td>
    </tr>
    <tr>
      <td style="text-align:left">Branding Service</td>
      <td style="text-align:left">Change the background and text colors, company logo, company name, product
        name, tenant name, and other resources in the console.</td>
    </tr>
    <tr>
      <td style="text-align:left">Catalog Service</td>
      <td style="text-align:left">Retrieve global and entitled catalog items, and entitlements for a catalog
        item and its service that the current user can review. A consumer can retrieve,
        edit, and submit a request form for a catalog item. A provider can retrieve,
        register, update, and delete catalog items. Provision and manage systems.</td>
    </tr>
    <tr>
      <td style="text-align:left">Component Registry Service</td>
      <td style="text-align:left">Access and manage all services and serves as the central view for all
        service lookups.</td>
    </tr>
    <tr>
      <td style="text-align:left">Composition Service</td>
      <td style="text-align:left">Allows vRealize Automation services to register application components,
        which the composition service manages so that they can be used in composite
        blueprints.</td>
    </tr>
    <tr>
      <td style="text-align:left">Content Management Service</td>
      <td style="text-align:left">Access and manage the content controller and package controller for export
        and import processes. This includes export and import for blueprints and
        software.</td>
    </tr>
    <tr>
      <td style="text-align:left">Endpoint Configuration Service</td>
      <td style="text-align:left">Create, read, update and delete endpoint types, endpoint categories, and
        endpoints.</td>
    </tr>
    <tr>
      <td style="text-align:left">Event Broker Service</td>
      <td style="text-align:left">Provide a central location and a consistent way of recording events and
        querying for events.</td>
    </tr>
    <tr>
      <td style="text-align:left">Forms Service</td>
      <td style="text-align:left">Used internally by the vRealize Automation system to create, read, update
        and delete (perform CRUD operations on) request forms for XaaS components.</td>
    </tr>
    <tr>
      <td style="text-align:left">IaaS Proxy Provider Service</td>
      <td style="text-align:left">Run a proxy service that acts as a bridge between the service catalog
        and the IaaS provider to call other services, such as the catalog service,
        composition service, reservation service, and event broker service.</td>
    </tr>
    <tr>
      <td style="text-align:left">Identity Service</td>
      <td style="text-align:left">Manage tenants, business groups, SSO and custom groups, users, and identity
        stores.</td>
    </tr>
    <tr>
      <td style="text-align:left">IP Address Management Service</td>
      <td style="text-align:left">Allocate and deallocate IP addresses from IP address management (IPAM)
        providers.</td>
    </tr>
    <tr>
      <td style="text-align:left">Licensing Service</td>
      <td style="text-align:left">Retrieve permissions and post serial keys.</td>
    </tr>
    <tr>
      <td style="text-align:left">Management Service (Reclamation Service)</td>
      <td style="text-align:left">Retrieve work item forms, callbacks, and tasks. Manage endpoint details
        including tenant, password, user name, and endpoint URL. Retrieve performance
        metrics. Retrieve and cancel reclamation requests.</td>
    </tr>
    <tr>
      <td style="text-align:left">Network Service</td>
      <td style="text-align:left">Access and manage application network and security settings for creating
        and configuring NAT and routed networks; creating load balancers; and adding
        and configuring security groups, security tags and security policies for
        application components.</td>
    </tr>
    <tr>
      <td style="text-align:left">Notification Service</td>
      <td style="text-align:left">Configure and send notifications for several types of events such as the
        successful completion of a catalog request or a required approval.</td>
    </tr>
    <tr>
      <td style="text-align:left">Orchestration Gateway Service</td>
      <td style="text-align:left">Provides a gateway to VMware Realize Orchestrator (vRO) for services running
        on vRealize Automation. By using the gateway, consumers of the API can
        access a vRO instance, and initiate workflows or script actions without
        having to deal directly with the vRO APIs.</td>
    </tr>
    <tr>
      <td style="text-align:left">Extensibility (Plug-in) Service</td>
      <td style="text-align:left">Retrieve, create, update, and delete a resource. Retrieve an extension.
        Retrieve license notifications.</td>
    </tr>
    <tr>
      <td style="text-align:left">Placement Service</td>
      <td style="text-align:left">Provides vRealize Automation with recommendations for the placement of
        deployments. With cluster health information from an external service such
        as vRealize Operations Manager, the service can recommend reservations
        to use for the provisioning of blueprint components.</td>
    </tr>
    <tr>
      <td style="text-align:left">Portal Service</td>
      <td style="text-align:left">Retrieve, create, update, and delete a portal resource.</td>
    </tr>
    <tr>
      <td style="text-align:left">Properties Service</td>
      <td style="text-align:left">Manage custom properties, property groups, and property definitions. Properties
        specify items that can be added to blueprints to trigger vRealize Orchestrator
        actions.</td>
    </tr>
    <tr>
      <td style="text-align:left">Reservation Service</td>
      <td style="text-align:left">Retrieve, create, update, and delete a reservation or reservation policy.</td>
    </tr>
    <tr>
      <td style="text-align:left">Software Services</td>
      <td style="text-align:left">Triggers the execution life cycle of software components using the software
        agent, registers software agents, and manages the creation, modification
        and deletion of software component, software component types, software
        resource requests, and nodes (machines).</td>
    </tr>
    <tr>
      <td style="text-align:left">vRA Orchestrator Service</td>
      <td style="text-align:left">Manage vRealize Orchestrator actions, tasks, packages, and workflows.
        Browse system and plug-in inventories.</td>
    </tr>
    <tr>
      <td style="text-align:left">Work Item Service</td>
      <td style="text-align:left">Retrieve, create, update, complete, cancel, and delete a work item. Also
        retrieve form data, metadata, detail forms, and submission forms from service
        providers.</td>
    </tr>
    <tr>
      <td style="text-align:left">XaaS Service</td>
      <td style="text-align:left">
        <p>Manages XaaS elements such as forms, endpoints, XaaS blueprints, tenants,
          vRealize Orchestrator imports, workflows, and work items.</p>
        <p>The advanced designer service selection on the vRealize Automation API
          Reference landing page selects the documentation for the XaaS service.</p>
      </td>
    </tr>
  </tbody>
</table>## Using the vRealize Automation REST API

To make vRealize Automation REST API service calls, you can use a browser application or an HTTP client program to send requests and review responses. 

### REST Client Programs

Any client application that can send HTTPS requests is an appropriate tool for developing REST applications with the vRealize Automation API. The following open-source programs are commonly used: 

* cURL. http://curl.haxx.se 
* Postman application. http://www.getpostman.com 

### Accessing the API Reference

The vRealize Automation API Reference lists all REST API service calls. It is provided as a Swagger document and is available in either of the following ways. 

* If vRealize Automation is installed, documentation is available with the product. 

  * A list of general services 

    ```markup
    https://$vRA/component-registry/services/docs
    ```

  * A list of installation and configuration services 

    ```markup
    https://$vRA:5480/config/
    ```

  $vRA denotes an instance of vRealize Automation. 

* If vRealize Automation is not installed, documentation is available from the APIs section for the vRealize Automation API at VMware{code} or, [https://code.vmware.com/apis/vrealize-automation](https://code.vmware.com/apis/vrealize-automation). 

For information about using the vRealize Automation API Reference, see [Viewing API Reference Information](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-B6EB272D-7D4D-44E0-9424-B5EBE9E784A8.html#GUID-B6EB272D-7D4D-44E0-9424-B5EBE9E784A8).

## Common Use Cases

While the vRealize Automation API Reference contains a menu that lists all REST API service calls, it does not document use cases. The Programming Guide provides frequently used use cases including sample requests and responses. 

The following REST API use cases provide the prerequisite, command line options and format, and sample results to help you perform a variety of vRealize Automation functions, such as requesting a machine or creating a reservation. Each includes service examples that provide syntax for the calls referenced in the use case. 

* [Creating a Tenant](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-6016B815-53E4-4C3C-89E8-DFEB37DB9288.html#GUID-6016B815-53E4-4C3C-89E8-DFEB37DB9288)
* [Requesting a Machine](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-867F8881-E381-453D-9AC6-19A91A2F5FB8.html#GUID-867F8881-E381-453D-9AC6-19A91A2F5FB8)
* [Approving a Machine Request](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-7DF6457B-6AB7-478A-815D-3E7F1F9F7B8A.html#GUID-7DF6457B-6AB7-478A-815D-3E7F1F9F7B8A)
* [Listing Provisioned Resources](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-3A50F6E1-25C9-4C90-815F-8B5D9C57FE54.html#GUID-3A50F6E1-25C9-4C90-815F-8B5D9C57FE54)
* [Managing Provisioned Deployments](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-DDC01756-B143-477A-98D4-1D2F75AEBFAB.html#GUID-DDC01756-B143-477A-98D4-1D2F75AEBFAB)
* [Working with Reservations](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-7697320D-F3BD-4A42-8721-FBC971B47195.html#GUID-7697320D-F3BD-4A42-8721-FBC971B47195)
* [Working with Reservation Policies](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-42E93709-516D-4858-B37F-4F970A51FCA7.html#GUID-42E93709-516D-4858-B37F-4F970A51FCA7)
* [Working with Key Pairs](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-BB8EADA5-A505-4A62-A308-4AC34770D99C.html#GUID-BB8EADA5-A505-4A62-A308-4AC34770D99C)
* [Working with Network Profiles](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-CB577BF4-871A-4985-AF47-91E796BED38C.html#GUID-CB577BF4-871A-4985-AF47-91E796BED38C)
* [Getting a List of Available IP Ranges](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-DE91D96C-A90A-419A-AA52-78305D40F9CA.html#GUID-DE91D96C-A90A-419A-AA52-78305D40F9CA)
* [Importing and Exporting Content](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-8DE745A4-D97D-4DB3-A20F-20E419442F40.html#GUID-8DE745A4-D97D-4DB3-A20F-20E419442F40)

curl is used for example requests. Request headers required by the API are included in example requests that are not fragments of a larger example. The variable $vRA represents the appliance name.domain name of the vRealize Automation server in all URLs. 

Most example responses show only those elements and attributes that are relevant to the operation being discussed. Ellipses \(...\) indicate omitted content within response bodies. 

Postman collections are not used in the API examples, but are available from the Code Samples section for the vRealize Automation API at VMware{code} or, [https://code.vmware.com/apis/vrealize-automation](https://code.vmware.com/apis/vrealize-automation).

