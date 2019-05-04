# Troubleshooting CIM Connections

If you have trouble with connections between a CIM client and a CIM server, or between a CIM server and a process that consumes indications, you can try to diagnose and correct the trouble using this information.

This material is organized into two sections. One section applies to connections initiated by the client. The other section applies to connections initiated by the server when delivering indications.

| ■  | See [Connections from Client to CIM Server](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/f98d554a-3f1d-452c-bf5e-74b6e48ab37a/720b45cc-9f94-488d-90ed-e924134308ab/doc/GUID-62018DDD-CBE4-4DC7-91E9-2F2E0C43182B.html) if your CIM client is unable to connect to the CIM server. |
| :--- | :--- |
| ■  | See [Connections from CIM Server to Indication Consumer](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/f98d554a-3f1d-452c-bf5e-74b6e48ab37a/720b45cc-9f94-488d-90ed-e924134308ab/doc/GUID-5D37668F-E204-4900-904F-4434D81DA32D.html) if your CIM client is able to connect to the CIM server and subscribe to indications, but the indications are not delivered. |

## Connecting from CIM Server to Indication Consumer

If your client can connect to a CIM server and subscribe to indications, but cannot receive indications, use these suggestions to try to resolve the problem.

### Using SLP 

Check the connection parameters using an SLP client \(available on the Web\). Run the SLP client on the same subnetwork as the managed server. Verify that the managed server advertises the expected CIM service and the correct URL.

### Using a Web Browser 

To verify that you can reach the CIM service at the advertised location, connect to the managed server with a Web browser. Use a URL of the form https://&lt;cim-server.mydomain.com&gt;:5989/ \(substituting the name of the actual server\), and verify that the server is responding on the expected port. Port 5989 is the default port for CIM-XML connections, but your installation might be different.

### Using a Command-Line Interface 

Using a command-line interface allows you to bypass any issues related to the correct invocation of the interface functions in a programmatic client.

For convenient interactive access to a CIM server, install wbemcli, available from [http://sourceforge.net/project/showfiles.php?group\_id=128809](http://sourceforge.net/project/showfiles.php?group_id=128809). Using wbemcli, you can invoke CIM operations from a shell.

To access a CIM server using the WS-Management protocol, install the wsmancli package, available from [http://sourceforge.net/projects/openwsman/](http://sourceforge.net/projects/openwsman/). Using the wsman command-line interface, you can invoke CIM operations from a shell.

### Verifying User Authentication Credentials 

If you are certain that the connection parameters are correct, verify the authentication parameters. To complete a connection, you must authenticate as a user that is known to the managed server. 

Connect to the managed server through the console and check that your root password is correct. Then use that password to authenticate as the root user from your client. Note 

If the managed server is in lockdown mode, you must authenticate using a CIM ticket obtained from vCenter Server. See CIM Authentication for Lockdown Mode for more information about using a CIM ticket to authenticate.

### Rebooting the Server 

If all your connection parameters are correct and you are certain of your authentication credentials but you still cannot complete a connection, reboot the managed server or restart the management agents on the server.

### Using Correct Client Samples 

If you are using sample clients supplied by VMware, check the documentation to be sure that the samples are intended to work with the CIM server to which you are trying to connect. The samples might hard-code parameters, such as the port and namespace, that affect the connection.

For example, the C++ code in the CIM Storage Management API Programming Guide connects to the CIM server included with ESX Server 3.0, but does not connect to the CIM server included with ESX 4 or later.

#### Using Other CIM Client Libraries 

VMware does not test all available CIM client libraries with ESXi. If your CIM client cannot connect to the CIM server, try writing a test client for a different library. For example, [http://.sourceforge.net](http://sourceforge.net/) has a number of CIM client libraries available.

#### Using the WS-Management Library 

If you are unable to find a satisfactory client library to make a WBEM connection, use WS-Management. ESXi includes a WS-Man server to support CIM operations. 

VMware recommends using the Web Services for Management Perl Library for WS-Man clients. This library is included with the VMware vSphere SDK for Perl version 1.6 or higher. See [http://www.vmware.com/support/pubs/sdk\_pubs.html](http://www.vmware.com/support/pubs/sdk_pubs.html) for more information about the vSphere SDK for Perl.

## Connections from Client to CIM Server

If your client fails to complete a connection to a CIM server, use these suggestions to help verify the connection parameters and the health of the CIM server.

### Firewall Configuration 

ESXi ships with a software firewall that is configured by default to block outgoing connection requests. When an indication is triggered, the producer cannot open a connection to the consumer unless the target port is opened in the firewall.

When you create an indication subscription, the CIMOM opens the corresponding port in the firewall for you. To check the firewall configuration, use these commands:

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x25A0;</th>
      <th style="text-align:left">
        <p>esxcli network firewall get</p>
        <p>tells you whether the firewall is enabled.</p>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">&#x25A0;</td>
      <td style="text-align:left">
        <p>esxcli network firewall ruleset list</p>
        <p>tells you which specific services are enabled.</p>
      </td>
    </tr>
  </tbody>
</table>To disable or enable the firewall, use these commands:

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x25A0;</th>
      <th style="text-align:left">
        <p>esxcli network firewall set -e false</p>
        <p>disables the firewall.</p>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">&#x25A0;</td>
      <td style="text-align:left">
        <p>esxcli network firewall set -e true</p>
        <p>enables the firewall.</p>
      </td>
    </tr>
  </tbody>
</table>It is also possible to create rulesets to open or close firewall ports manually. For information about manual firewall configuration for ESXi, see the vSphere Security Guide.

For information about the esxcli command set, see the manual Getting Started with vSphere Command-Line Interfaces.

### System Event Log 

Alert indications for a managed server rely primarily on the contents of the System Event Log \(SEL\). If the SEL is disabled, or if it is full and cannot accept new log entries, you will not receive most alert indications for new events. 

If the SEL is full, system status is shown correctly in response to CIM queries, regardless of indication delivery. To receive indications when the SEL is not accepting new entries, you have the following options. 

| ■  | Consult your hardware vendor’s system documentation for instructions to clear the SEL.  |
| :--- | :--- |
| ■  | You can clear the SEL from a vSphere Client connected to vCenter Server. On the Hardware tab, choose System Event Log from the View menu and click Reset event log. |



