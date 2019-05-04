# Introduction to the CIM SMASH/Server Management API

VMware ESXi includes a CIM Object Manager \(CIMOM\) that implements a set of server discovery and monitoring features that are compatible with the SMASH standard. With the VMware CIM SMASH/Server Management API, clients that use industry-standard protocols can do the following: 

| ■  | Enumerate system resources  |
| :--- | :--- |
| ■  | Monitor system health data  |
| ■  | Upgrade installed software |

The VMware implementation of the SMASH standard uses the open-source implementation of the Open Management with CIM \(OMC\) project. OMC provides tools and software infrastructure for hardware vendors and others who require a reliable implementation of the Distributed Management Task Force \(DMTF\) management profiles.

## Platform Product Support for the CIM API

The VMware CIM SMASH/Server Management API is supported by ESXi. Hardware compatibility for ESXi is documented in the hardware compatibility guides, available on the VMware Web site. See [http://www.vmware.com/support/pubs](http://www.vmware.com/support/pubs/).

## Supported Protocols and Versions for the CIM API

The VMware CIM SMASH/Server Management API supports the following protocols: 

| ■  | CIM-XML over HTTP or HTTPS  |
| :--- | :--- |
| ■  | WS-Management over HTTP or HTTPS  |
| ■  | SLP |

### CIM Version

The CIM standard is an object model maintained by the DMTF, a consortium of leading hardware and software vendors. ESXi 6.7 is compatible with version 2.26.0 Final of the CIM schema.

### Supported Profiles for the CIM API

The VMware CIM SMASH/Server Management API supports a subset of the profiles defined by the SMWG. These profiles have overlapping structures and can be used in combinations to manage a server. 

This VMware CIM implementation also includes a profile from the SMI specification developed by the Storage Networking Industry Association \(SNIA\). The implementation uses SMI-S version 1.3. 

In some situations, the version of a profile supported by the CIMOM is important. The following table shows the version of each profile that is implemented by the VMware CIM SMASH/Server Management API for this release of ESXi. 

Some profiles are only partially implemented by VMware. The implementation does not include all mandatory elements specified in the profile. These profiles are listed with “N/A” in the Version column. For information about which elements are implemented, see the VMware CIM SMASH/Server Management API and Profile Reference at [http://pubs.vmware.com/vsphere-51/topic/com.vmware.cimsdk.smashref.doc/title\_page.html](http://pubs.vmware.com/vsphere-51/topic/com.vmware.cimsdk.smashref.doc/title_page.html).

### SMASH Version for the CIM API

The SMASH standard is maintained by the Server Management Working Group \(SMWG\) of the DMTF. ESXi 6.7 is compatible with version 1.0.0 of the SMASH standard.

### Installing CIM Profiles VIBs

The 3.5 and 4.1 versions of vSphere included the LSI and HP RAID CIM providers in the default VIB for the ESXi server.

In vSphere 5.0 and later, the LSI and HP provider VIBs are not included in the default VIBs. Therefore, if you are using an LSI or HP RAID controller card on your host with vSphere 5.0 or later, you will need to install an LSI or HP VIB before the associated RAID storage device will show up in your vCenter Server Inventory.

### Download CIM Provider VIBs

The following procedure gives you the general steps for downloading a VIB from a third-party website. The instructions may be slightly different for each third-party site.

To download a CIM Provider VIB: 

| 1 | Go to the website of the CIM Provider, and look for the ‘Support’ or ‘Downloads’ section. For example, on the HP website, the section is called, ‘HP Drivers and Support’. On the LSI Corporation website, the section is called, ‘Support Downloads By Product‘ under the ‘Support’ tab. |
| :--- | :--- |
| 2 | Enter the hardware type for the VIB you want to download, or select the type from a list. |
| 3 | Choose the VIB bundle that contains the words ‘VMware’ and the VMware product, such as ‘ESXi’. |

### Add a CIM Provider VIB to Your ESXi Image

You can add a CIM Provider VIB to your ESXi image using the vSphere ESXi Image Builder CLI. Install VIBs from only one OEM vendor at a time. 

Before you begin, install the VMware PowerCLI software. 

Use the following steps to add a new VIB to your image: Procedure 

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">
        <p>Run Add-EsxSoftwareDepot for each depot you want to work with.</p>
        <p>Run Add-EsxSoftwareDepot -DepotUrl depot_url</p>
        <p>or</p>
        <p>Run Add-EsxSoftwareDepot -DepotUrl C:\file_path\offline-bundle.zip</p>
        <p>The cmdlet returns one or more SoftwareDepot objects.</p>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">
        <p>Run Get-EsxImageProfile to list all image profiles in all currently visible
          depots.</p>
        <p>Get-EsxImageProfile</p>
        <p>The cmdlet returns all available profiles. You can narrow your search
          by using the optional arguments to filter the output.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left">
        <p>Clone the profile and make changes to the clone if the image profile is
          read only.</p>
        <p>New-EsxImageProfile -CloneProfile My_Profile -Name &quot; Test Profile
          Name &quot;</p>
        <p>Image profiles published by VMware and its partners are read only.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">4</td>
      <td style="text-align:left">
        <p>Run Add-EsxSoftwarePackage to add a new package to one of the image profile.</p>
        <p>Add-EsxSoftwarePackage -ImageProfile My_Profile -SoftwarePackage partner-package</p>
        <p>The cmdlet runs the standard validation tests on the image profile. If
          validation succeeds, the cmdlet returns a modified, validated image profile.
          If the VIB that you want to add depends on a different VIB, the cmdlet
          displays that information and includes the VIB that would resolve the dependency.
          If the acceptance level of the VIB that you want to add is lower than the
          image profile acceptance level, an error results. Change the acceptance
          level of the image profile to add the VIB.</p>
        <p>Your image profile now includes the new VIB.</p>
        <p>See the vSphere Installation and Setup guide for more information about
          how to add a VIB to an image profile.</p>
      </td>
    </tr>
  </tbody>
</table>



