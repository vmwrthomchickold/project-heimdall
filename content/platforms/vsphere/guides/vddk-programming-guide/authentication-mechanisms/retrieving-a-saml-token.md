# Retrieving a SAML Token

The vCenter Single Sign-On service provides authentication mechanisms for securing the operations that your client application performs in the virtual environment. Client applications use SAML security tokens for authentication. 

Client applications use the vCenter Single Sign-On service to retrieve SAML tokens. For more information about how to acquire a SAML security token, see the vCenter Single Sign-On Programming Guide documentation. Prerequisites 

Verify that you have the vCenter Single Sign-On URL. You can use the Lookup Service on the Platform Services Controller to obtain the endpoint URL. For information about retrieving service endpoints, see [Retrieving Service Endpoints](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-C0A7294E-E1C6-41EA-BB2F-E319D85F3576.html#GUID-C0A7294E-E1C6-41EA-BB2F-E319D85F3576). 

## Procedure 

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">
        <p>Create a connection object to communicate with the vCenter Single Sign-On
          service.</p>
        <p>Pass the vCenter Single Sign-On endpoint URL, which you can get from the
          Lookup Service.</p>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">Issue a security token request by sending valid user credentials to the
        vCenter Single Sign-On service on the Platform Services Controller.</td>
    </tr>
  </tbody>
</table>The vCenter Single Sign-On service returns a SAML token.

