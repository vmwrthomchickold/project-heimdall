# Creating a vSphere Automation Session with a SAML Token

To establish a vSphere Automation session, you create a connection to the vSphere Automation API Endpoint and then you authenticate with a SAML token to create a session for the connection. Prerequisites 

| ■  | Retrieve the vSphere Automation Endpoint URL from the Lookup Service.  |
| :--- | :--- |
| ■  | Obtain a SAML token from the vCenter Single Sign-On service.  |

## Procedure 

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">
        <p>Create a connection by specifying the vSphere Automation API Endpoint
          URL and the message protocol to be used for the connection. Note</p>
        <p>To transmit your requests securely, use https for the vSphere Automation
          API Endpoint URL.</p>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">
        <p>Create the request options or stub configuration and set the security
          context to be used.</p>
        <p>The security context object contains the SAML token retrieved from the
          vCenter Single Sign-On service. Optionally, the security context might
          contain the private key of the client application.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left">
        <p>Create an interface stub or a REST path that uses the stub configuration
          instance.</p>
        <p>The interface stub corresponds to the interface containing the method
          to be invoked.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">4</td>
      <td style="text-align:left">
        <p>Invoke the session create method.</p>
        <p>The service creates an authenticated session and returns a session identification
          cookie to the client.</p>
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