# Authentication Mechanisms

To perform operations on services in the vSphere environment, you must create an authenticated connection to the services that you want to use. With the vSphere Automation SDKs you can authenticate and access vSphere Automation services. 

Client applications can choose from two supported authentication patterns for accessing services in the virtual environment. 

For better security, client applications can request a security token to authenticate connections with the vSphere Automationservices. 

To invoke operations on services, client applications must create a security context. The security context represents the client authentication. You can achieve authentication by using one of the following mechanisms. 

<table>
  <thead>
    <tr>
      <th style="text-align:left">Password-Based Authentication</th>
      <th style="text-align:left">To authenticate with user name and password, you connect to the vSphere
        Automation Endpoint with vCenter Single Sign-On user credentials and obtain
        a session identifier (ID). The user account credentials are validated by
        the vSphere Automation Endpoint, and must be present in the vCenter Single
        Sign-On identity store. The session ID is valid only for the service endpoint
        that you want to access and that issues the session ID.</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">Token-Based Authentication</td>
      <td style="text-align:left">
        <p>Client applications can authenticate by using the vCenter Single Sign-On
          component on the Platform Services Controller. vCenter Single Sign-On includes
          the Security Token Service (STS) that issues security tokens. The token
          must comply with the Security Assertion Markup Language (SAML) specification,
          which defines an XML-based encoding for communicating authentication data.</p>
        <p>vCenter Single Sign-On supports two types of security tokens: bearer token
          and Holder-of-Key (HoK) token. To acquire a SAML token, client applications
          must issue a token request to vCenter Single Sign-On.</p>
        <p>Client applications can present a SAML token to the vSphere Automation
          Endpoint in exchange for a session identifier with which they can perform
          a series of authenticated operations.</p>
        <p>To retrieve a session ID for the vSphere Web Services endpoint, you provide
          the SAML token to the vSphere Web services endpoint. For more information
          about creating an authenticated session to access the vSphere Web Services,
          see the vSphere Web Services SDK Programming Guide documentation.</p>
      </td>
    </tr>
  </tbody>
</table>