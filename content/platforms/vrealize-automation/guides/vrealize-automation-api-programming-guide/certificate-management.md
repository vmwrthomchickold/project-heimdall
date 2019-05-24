# Certificate Management

You can use the vSphere Automation API to manage the life cycle of TLS certificates. The API provides operations for generating a certificate signing request \(CSR\), retrieving, renewing, or replacing TLS certificates, and creating, retrieving, or deleting trusted root certificate chains. 

## VMware Endpoint Certificate Store

The TLS certificates and trusted root chain certificates are maintained in the VMware Endpoint Certificate Store \(VECS\) and provide the means for services inside vCenter Server to communicate in a secure manner. 

| User Operations |  |
| :--- | :--- |
|   |  |
| Operation  | Description  |
| Generate a CSR  | You can generate a CSR by providing a valid specification. If the operation is successful, you receive a CSR in PEM format.  |
| Get TLS certificates  | You can retrieve existing TLS certificates and additional certificate information such as serial number, issuer, validity, thumbprint, and so on.  |
| Replace TLS certificates  | You can replace the existing TLS certificate with another certificate that you specify.  |
| Renew TLS certificates  | You can renew the validity of an existing TLS certificate for a specified period. The duration should be less than or equal to 730 days. If you do not specify the duration, the default value of 730 days is applied.  |
| Create a trusted root certificate chain  | You can create a trusted root certificate chain by providing a valid specification. If the operation is successful, you receive a unique identifier of the last certificate present in the root chain.  |
| List trusted root certificates  | You can retrieve the identifiers of all certificates present in the trusted root chain.  |
| Get trusted root certificate information  | You can retrieve the PEM certificate by providing the identifier of the certificate. The certificate identifier can be retrieved by using the List trusted root certificates operation.  |
| Delete a trusted root certificate  | You can delete a specific certificate by providing the identifier. The certificate identifier can be retrieved by using the List trusted root certificates operation. |

## HTTP Requests for Certificate Management 

You can use HTTP requests to generate a CSR, retrieve, renew, or replace TLS certificates, and retrieve, create, or delete trusted root certificate chains. HTTP Requests 

The following HTTP requests show the syntax that you can use to perform the available user operations. Note 

Before you send requests, you must authenticate with administrator credentials. 

| ■  | Generate a CSR  |
| :--- | :--- |
| ■  | Get TLS certificates  |
| ■  | Replace TLS certificates  |
| ■  | Renew TLS certificates  |
| ■  | Create a trusted root certificate chain  |
| ■  | List trusted root certificates  |
| ■  | Get trusted root certificate information  |
| ■  | Delete a trusted root certificate  |

For information about the content and syntax of the HTTP request body, see the API Reference documentation. HTTP Status Codes 

[HTTP Status Codes](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-F689BA7C-1D04-4256-A117-67D01E34A30A.html#GUID-F689BA7C-1D04-4256-A117-67D01E34A30A__table_50E1AD5F586C477F816E3E8E9DE6F203) lists the status codes that you can receive when you send HTTP requests. 

| HTTP Status Codes |  |  |
| :--- | :--- | :--- |
|   |  |  |
| HTTP Status Code  | Description  | Operations that Return the Status Code  |
| 200  | The operation is successful.  | All operations. You can check the returned data in the results data structure.  |
| 400  | The operation is unsuccessful.  |  |
| 403  | There is an authorization issue.  |  |
| 404  | The object you are trying to perform an operation on is missing.  |  |

## cURL Examples of Certificate Management Operations 

The following cURL command examples show the syntax for operations that you can use to manage TLS certificates and trusted root certificates. 

### Prerequisites 

| ■  | Verify that the certificate management service is running on your vCenter Server instance.  |
| :--- | :--- |
| ■  | Verify that you have the session ID that is required to invoke the API operations. You can obtain the session ID by running the following command.  |

### Renewing a Certificate 

This example renews an existing TLS certificate issued by the VMware Certificate Authority \(VMCA\). Note 

The duration of the renewed certificate is explicitly set to 730 days in the input spec, which is the default and maximum value. If you do not specify the duration in the input spec, the default value of 730 days is applied. 

```text
curl --insecure -H 'Content-Type:application/json' --request POST --data-ascii '{"duration":"730"}' --url https://<server>/rest/vcenter/certificate-management/vcenter/tls/?action=renew --header 'vmware-api-session-id:8ab92796a606801c233a2189a1e8f823'
```

### Generating a CSR 

This example generates a CSR and private key on the vCenter Server instance. The private key remains on the machine. 

You can perform this operation as part of a use case scenario in which you want to replace a VMCA-issued TLS certificate with a TLS certificate issued by a custom Certificate Authority \(CA\). You must use the CSR and obtain a certificate from the external CA to replace the existing certificate. For details on the replacement operation, see [Example: Replace a Certificate](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-A8FD6048-19DD-48D5-8962-CB6949C2B0C4.html#GUID-A8FD6048-19DD-48D5-8962-CB6949C2B0C4__example_1144104084A9489DB9C4038534A7893C). 

```text
curl --insecure -H 'Content-Type:application/json' --request POST --data-ascii '{"spec": {"key_size": "2048","common_name":"sc-rdops-vm05-dhcp-154-50.eng.vmware.com","country":"US","locality":"PA","state_or_province":"CA","organization":"VMware","organization_unit":"SSO","email_address":"abc@xyz.com"} }' --url https://<server>/rest/vcenter/certificate-management/vcenter/tls-csr --header 'vmware-api-session-id:f20b7b1bea84f48cd460ead3da8f7fdd'
```

### Replacing a Certificate 

This example replaces an existing TLS certificate with another certificate obtained from a CSR that you generated. You must provide the obtained certificate in PEM format in the input spec. Note 

You must generate a CSR before you can replace a certificate. 

You can perform this operation as part of a use case scenario in which you want to replace a VMCA-issued TLS certificate with a TLS certificate issued by a custom Certificate Authority \(CA\). You must use the CSR and obtain a certificate from the external CA to replace the existing certificate. For details on the CSR generation operation, see [Example: Generate a CSR](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-A8FD6048-19DD-48D5-8962-CB6949C2B0C4.html#GUID-A8FD6048-19DD-48D5-8962-CB6949C2B0C4__example_55AC124F8BE14326802C2DEDD690D3FE). 

```text
curl --insecure -H 'Content-Type:application/json' --request PUT --data-ascii '{"spec":{"cert": "-----BEGIN CERTIFICATE-----\nMIID9TCCAt2gAwIBAgIBDDANBgkqhkiG9w0BAQsFADBJMQswCQYDVQQGEwJJTjELMAkGA1UECAwCS0ExDzANBgNVBAoMBlZNd2FyZTEMMAoGA1UECwwDU1NPMQ4wDAYDVQQDDAV2bWNhMjAeFw0xODEyMTExMDEwMDJaFw0xOTEyMTExMDEwMDJaMGwxCzAJBgNVBAYTAklOMQswCQYDVQQIDAJLQTEPMA0GA1UECgwGVk13YXJlMQwwCgYDVQQLDANTU08xMTAvBgNVBAMMKHNjLXJkb3BzLXZtMDUtZGhjcC0xNTQtNTAuZW5nLnZtd2FyZS5jb20wggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCrrU33wRIu1IWUX9RK4N2JYgI/CUsTuy87eTSANtlxhFNBxjlS1Pb+QQUsNxjNoYgOr+/HVOUhBwXaQsQ5u6PJS84aPIk71wtUtair3drHqYA2sz/kyAtTWpfLaGfGAtDuYuDesFelf7B4XvUg6KfspJofILFnNV2Krllz8hYH94H/hhakVH+FwmKNNraIeOVHhy0uuybA4vH2Y6eUePeANyGrqiop8iszG+eyZ73tQZjBEEUI6rcfRyyqGivNPKzTA9JzpaPdcOxaxNbQRq3jkX34vAodoU0WdI1sc+hIgy6vD0jVzPm2yPvdhX/YpEvrIOXkUn/uHb3Qdr8kkOgzAgMBAAGjgcQwgcEwCQYDVR0TBAIwADALBgNVHQ8EBAMCBPAwLAYJYIZIAYb4QgENBB8WHU9wZW5TU0wgR2VuZXJhdGVkIENlcnRpZmljYXRlMB0GA1UdDgQWBBTXXwxKMqkw+UhY3KzQ+gvC+6jZhDAfBgNVHSMEGDAWgBTgw+aYfUn8t/XComKqPt65mCGATzA5BgNVHREEMjAwgihzYy1yZG9wcy12bTA1LWRoY3AtMTU0LTUwLmVuZy52bXdhcmUuY29thwQKoJoyMA0GCSqGSIb3DQEBCwUAA4IBAQALMMpkuWIQBT137z/Gnu53lJCy3h+h4AU7ieZtVWlVR07Gwl8vb1n696TBY306ausaFIlsrZIw166Oz5Mm5cGkOPfK4vUs+bMO0w3CtFJg3wwzWqp7R37V1/c9YGSEH5pj9FaPBplf6iNEDcNQAFiVoPn1GHuM36Bubg6/8LyeZeYVONVC7xKq6FAX6uAjAKFEgBRrSr9oPnFApxhJQn1GZvAOGi69pnCTawZRcc6ouh7asMkVJ+zrFdo0jLXnFh0pPyWQI7rsN0SgqlM85SZ0dn+KJ4Az1LWeeO+7YGm9+r3jPA3BBTLF0dvhcRQUJsjBwnmcunSkeQJ6kbyO+yTV\n-----END CERTIFICATE-----"}}' --url https://<server>/rest/vcenter/certificate-management/vcenter/tls --header 'vmware-api-session-id:f20b7b1bea84f48cd460ead3da8f7fdd'
```

### Creating and Adding Trusted Root Certificates 

This example creates two certificate chains and adds them to the trusted root chain. 

```text
curl --insecure -H 'Content-Type:application/json' --request POST --data-ascii '{ "spec" : { "cert_chain" : {"cert_chain": ["-----BEGIN CERTIFICATE-----\nMIIDwjCCAqqgAwIBAgIJAI1OflMjc0LfMA0GCSqGSIb3DQEBCwUAMHYxCzAJBgNV\nBAYTAklOMQswCQYDVQQIDAJLQTEMMAoGA1UEBwwDQkxSMQ8wDQYDVQQKDAZWTXdh\ncmUxDDAKBgNVBAsMA1NTTzEMMAoGA1UEAwwDQ0ExMR8wHQYJKoZIhvcNAQkBFhBz\naWduMUB2bXdhcmUuY29tMB4XDTE5MDEwMjA2MTIyMloXDTI4MTIzMDA2MTIyMlow\ndjELMAkGA1UEBhMCSU4xCzAJBgNVBAgMAktBMQwwCgYDVQQHDANCTFIxDzANBgNV\nBAoMBlZNd2FyZTEMMAoGA1UECwwDU1NPMQwwCgYDVQQDDANDQTExHzAdBgkqhkiG\n9w0BCQEWEHNpZ24xQHZtd2FyZS5jb20wggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAw\nggEKAoIBAQDHuDDoAyGj6FGLZOIxMEK7oO2LhbfGbIbBiXTR5WWSkTsmsxy0Vge5\nhbVEkGW2OjgIxvmqBC/nVeH1b4gTJAZFmJ6lrh6Ri8HC5cyIePVJkz/PR08SbKmy\nmagd02N6ZqBgMEr3eQ2NTtqUOutvphRT5f+fyGKL5uPjOrhNn6v8GDrIF4wUY6aV\nWYDG6Mcay/cv814PZoTIJa0juIEfJXzOO0gxzAY6Jwi6k3DmLkps7zFErRbWUwYR\niaa46LKRHRlX71h0gsWfx7TNdCvQ8emiPXsYsqUkOy9+MSfr3CsQcPzNy8qDbImt\ngK6z2T4vvV7r5Iir5srD7yyWm5rKmtFDAgMBAAGjUzBRMB0GA1UdDgQWBBSv6kwh\nVWkFQ/se4wRz3PayMJTjgzAfBgNVHSMEGDAWgBSv6kwhVWkFQ/se4wRz3PayMJTj\ngzAPBgNVHRMBAf8EBTADAQH/MA0GCSqGSIb3DQEBCwUAA4IBAQC2yEXM2fTCYRvh\noD40MrDLK/g+mKSixvsXtebTga47fHi8LxnT6KXGc44ZMT/HTSzwk2alYG8EXHK1\nFZeNNFnhYmS24DLgrCq+9p/yThotbfWe6vaUZ87jgbAP9HRAsq/9HYW3s0lUBD4i\ne/FZrBGRjgdtXVQ0tm5N6TVRQq2IwVPQ3niv36KLFu9MmAMhlIIZ3y8sX4Bha13q\nmhOCM74/qw4d88kGgq9lnebpwhmmXl5IOScZX39gJpsgpWQ4a1lhOTWWLT5NYu3z\nxiS9Jc1hr0PWtKE5eWSVu6mMmEx9Tqov/KKMRBCP/pp4aHyn0NlWFtHl7MtWrGC7\nohzPCShe\n-----END CERTIFICATE-----","-----BEGIN CERTIFICATE-----\nMIID5jCCAs6gAwIBAgIBCDANBgkqhkiG9w0BAQsFADB2MQswCQYDVQQGEwJJTjEL\nMAkGA1UECAwCS0ExDDAKBgNVBAcMA0JMUjEPMA0GA1UECgwGVk13YXJlMQwwCgYD\nVQQLDANTU08xDDAKBgNVBAMMA0NBMTEfMB0GCSqGSIb3DQEJARYQc2lnbjFAdm13\nYXJlLmNvbTAeFw0xOTAxMDIwNjE3MDZaFw0yNDA2MjQwNjE3MDZaMGgxCzAJBgNV\nBAYTAklOMQswCQYDVQQIDAJLQTEPMA0GA1UECgwGVk13YXJlMQwwCgYDVQQLDANT\nU08xDDAKBgNVBAMMA0NBMjEfMB0GCSqGSIb3DQEJARYQc2lnbjJAdm13YXJlLmNv\nbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAL3s5ycFPQmgffQmZKaE\nM/0ymZgh/Kz3txTmWpAiEPGpGdrulDfwubDEbOXfHtsWfcvj48iDa6Nn4g5bNrej\naMoBEIKd0WeV9fwnL/i2wYFiKKhLYiWaHDm5BT79YVaBLEMK6BL/9wc2FoUI2vEf\nQyVSuDuKWSrwx3gB2IFC2q7BpzT3kgq1HmWKVA52nFpMgbe1zlRy9sV08bBTybMO\nzm/Z0c4+a5Y0P1fO6ThiCF+92s0jMow0Bm96qN3nQm6lMgbcY+5um7RgOuBY4iSF\nKTblVDMS/rZAQkPwcP/E8AxcywRazx46awCfe3NAasiVBuI/iADc63SmYs+z+0cS\n8qECAwEAAaOBjDCBiTAMBgNVHRMEBTADAQH/MAsGA1UdDwQEAwIBBjAsBglghkgB\nhvhCAQ0EHxYdT3BlblNTTCBHZW5lcmF0ZWQgQ2VydGlmaWNhdGUwHQYDVR0OBBYE\nFDexYpQDPTkuYf9M47ILnGOg5Fh/MB8GA1UdIwQYMBaAFK/qTCFVaQVD+x7jBHPc\n9rIwlOODMA0GCSqGSIb3DQEBCwUAA4IBAQAMKy6fM7ldYf/IlMSR/0zH4gTauR8Z\nERXkRD65SXa9YgOkp/U59mhlGsfxeAze47jXjD7GNTNpLogYFQkXP9yrIpyYKjRP\n0I8zo8faY/9hEJn2pHZTaYKgZICw0rlfCwGF/so1cxnkocoIsmA56lMPT5xcmyFc\nkvwEBgTb8WgXUTnR0MA20puGI8aaXsAHOwQYM8nexvrfSbJADYJtcG73YqjswNYk\niloSd/uslyhmvb1HVyix794SxAIEybs177ijKOxdicq3XogaeGhOIymvDcCv/55J\n5FgJY341cCZmESPyC1GkuX52OSoZartB1jhSd5cKKlaLobFbTTajs9oa\n-----END CERTIFICATE-----"]}}}' --url https://<server>/rest/vcenter/certificate-management/vcenter/trusted-root-chains/ --header 'vmware-api-session-id:e594038d4c1023afe86b2c14b0b741f0'
```

### Listing the Trusted Root Certificates 

This example lists the IDs of all certificates present in the trusted root chain. 

```text
curl --insecure --request GET --url https://<server>/rest/vcenter/certificate-management/vcenter/trusted-root-chains/ --header 'vmware-api-session-id: e594038d4c1023afe86b2c14b0b741f0'
```

### Getting Trusted Root Certificate Information 

This example retrieves information about a trusted root certificate with ID AFEA4C2155690543FB1EE30473DCF6B23094E383. 

```text
curl --insecure --request GET --url https://<server>/rest/vcenter/certificate-management/vcenter/trusted-root-chains/AFEA4C2155690543FB1EE30473DCF6B23094E383 --header 'vmware-api-session-id: e594038d4c1023afe86b2c14b0b741f0'
```

### Deleting a Trusted Root Certificate 

This example deletes the trusted root certificate with ID AFEA4C2155690543FB1EE30473DCF6B23094E383. 

```text
curl --insecure --request DELETE --url https://<server>/rest/vcenter/certificate-management/vcenter/trusted-root-chains/AFEA4C2155690543FB1EE30473DCF6B23094E383 --header 'vmware-api-session-id: e594038d4c1023afe86b2c14b0b741f0'
```

