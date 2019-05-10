# Creating a vSphere Automation Session with User Credentials

With the vSphere Automation SDKs , you can create authenticated sessions by using only user credentials. 

You connect to the vSphere Automation Endpoint by using a user name and password known to the vCenter Single Sign-On service. The vSphere Automation uses your credentials to authenticate with the vCenter Single Sign-On Service and obtain a SAML token. Prerequisites 

| ■  | Retrieve the vSphere Automation Endpoint URL from the Lookup Service.  |
| :--- | :--- |
| ■  | Verify that you have valid user credentials for the vCenter Single Sign-On identity store.  |

## Procedure 

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">Create a connection stub by specifying the vSphere Automation Endpoint
        URL and the message protocol to be used for the connection.</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">
        <p>Create a stub configuration instance and set the specific security context
          to be used.</p>
        <p>The security context object uses the user name and password that are used
          for authenticating to the vCenter Single Sign-On service.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left">Create a Session stub that uses the stub configuration instance.</td>
    </tr>
    <tr>
      <td style="text-align:left">4</td>
      <td style="text-align:left">
        <p>Call the create operation on the Session stub to create an authenticated
          session to the vSphere Automation Endpoint.</p>
        <p>The operation returns a session identifier.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">5</td>
      <td style="text-align:left">Create a security context instance and add the session ID to it.</td>
    </tr>
    <tr>
      <td style="text-align:left">6</td>
      <td style="text-align:left">Update the stub configuration instance with the session security context.</td>
    </tr>
  </tbody>
</table>