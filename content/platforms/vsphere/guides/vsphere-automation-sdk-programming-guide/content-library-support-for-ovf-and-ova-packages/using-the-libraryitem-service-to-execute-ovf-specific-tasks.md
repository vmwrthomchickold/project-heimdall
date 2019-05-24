# Using the LibraryItem Service to Execute OVF-Specific Tasks

You can deploy virtual machines and vApps on hosts, clusters, and resource pools in your environment. You use the VM templates and vApp templates from an OVF package that is stored as a content library item. 

With the vSphere Automation API, you can use the LibraryItem service to deploy virtual machines and virtual applications from library items that contain OVF packages. You can also use the LibraryItem vSphere Automation service to create library items from existing virtual machines and virtual appliances.

## Deploying a Virtual Machine or Virtual Appliance from an OVF Package in a Content Library 

You can use the LibraryItem service to deploy a virtual machine or virtual appliance on a host, cluster, or resource pool from a library item. Procedure 

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">Create a DeploymentTarget instance to specify the deployment location
        of the virtual machine or virtual appliance.</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">
        <p>Instantiate the ResourcePoolDeploymentSpec class to define all necessary
          parameters for the deployment operation.</p>
        <p>For example, you can assign a name for the deployed virtual machine or
          virtual appliance, and accept the End User License Agreements (EULAs) to
          complete the deployment successfully.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left"><b>(Optional) </b>Retrieve information from the descriptor file of the
        OVF package and use the information during the OVF package deployment.</td>
    </tr>
    <tr>
      <td style="text-align:left">4</td>
      <td style="text-align:left">Invoke the deploy method on the LibraryItem service.</td>
    </tr>
    <tr>
      <td style="text-align:left">5</td>
      <td style="text-align:left">Verify the outcome of the deployment operation.</td>
    </tr>
  </tbody>
</table>## Create an OVF Package in a Content Library from a Virtual Machine 

You can create library items from existing virtual machines or virtual appliances. Use those library items later to deploy virtual machines and virtual appliances on hosts and clusters in the vSphere Automation environment. Procedure 

| 1 | Create a DeployableIdentity instance to specify the source virtual machine or virtual appliance to be captured in an OVF package.  |
| :--- | :--- |
| 2 | Create a CreateTarget instance to identify the content library where the OVF package is stored.  |
| 3 | Create a CreateSpec instance to specify the properties of the OVF package.  |
| 4 | Initiate a synchronous create operation by invoking the create function of the LibraryItem service.  |
| 5 | Verify the results of the create operation. |

