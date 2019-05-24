# Creating a Web Services Session

To develop a complex workflow, you might need to send requests to vSphere Web Services running in your virtual environment. To achieve this, you access the vSphere Web Services API by using the Web Services endpoint. 

The vSphere Web Services API also supports session-based access. To establish an authenticated session, you can send the SAML token retrieved from the vCenter Single Sign-On service to a vSphere Web Service. In return, you receive a session identifier that you can use to access the service. For more information about accessing Web Services and additional examples, see the vSphere Web Services SDK Programming Guide documentation. 

The vSphere Automation SDK for Python supports a simplified way of creating connections to the Web Services API by using the pyVim library. Prerequisites 

| ■  | Retrieve the vSphere Web Services endpoint URL from the Lookup Service.  |
| :--- | :--- |
| ■  | Obtain a SAML token from the vCenter Single Sign-On service.  |

## Procedure 

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">Connect to the vSphere Web Services endpoint.</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">Send the SAML token to a specific Web service to create an authenticated
        session.</td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left">
        <p>Add the retrieved session ID to the service content object.</p>
        <p>The Service Content object gives you access to several server-side managed
          objects that represent vSphere services and components.</p>
      </td>
    </tr>
  </tbody>
</table>