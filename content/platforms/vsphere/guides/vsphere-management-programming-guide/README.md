# Accessing vSphere Automation Services

vSphere Automation SDK provides mechanisms for creating remote stubs to give clients access to vSphere Automationservices. 

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
  <tbody></tbody>
</table>For greater security, use an external utility to create a certificate store: 

```text
keytool -import -noprompt -trustcacerts \
-alias <alias name> \
-file <certificate file> \
-keystore <truststore filename> \
-storepass <truststore password>
```

