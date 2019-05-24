---
author: VMware
draft: true
description:
guide: CIM SMASH/Server Management API Programming Guide
include_toc: true
layout: cim_smash_server_api_programming_guide
title: Creating Offline Bundles
toc: true
type: topic
weight: 5
---
Offline bundles contain a combination of VIBs and metadata used to update ESXi host software. Offline bundles are similar to depots, with the difference that offline bundles are available from a local file system rather than from a web server.

You can create an offline bundle from a depot using VMware vSphere PowerCLI.

## Create an Offline Bundle With VMware vSphere PowerCLI

Before you can create an offline bundle, you must install the PowerCLI software. vSphere PowerCLI 5.0 requires:

| ■  | .NET 2.0 Service Pack 1  |
| :--- | :--- |
| ■  | Windows PowerShell 1.0 or Windows PowerShell 2.0 RTM  |

You can download vSphere PowerCLI 5.0 from the VMware vSphere 5.0 web site.

To create an offline bundle using vSphere PowerCLI Procedure

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">
        <p>Run vSphere PowerCLI.</p>
        <p>Choose Start &gt; Programs &gt; VMware &gt; VMware vSphere PowerCLI &gt;
          VMware vSphere PowerCLI.</p>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">
        <p>Select a software depot from which to create an offline bundle.</p>
        <p>Add-ESXSoftwareDepot http://depot-server/build-123456/ESX Note</p>
        <p>If you previously added a different software depot during this session,
          first remove it from the array of default software depots. Repeat the following
          commands until the $DefaultSoftwareDepots array is empty. Then select a
          software depot using the Add-ESXSoftwareDepot command.</p>
      </td>
    </tr>
  </tbody>
</table>```text
Remove-ESXSoftwareDepot $DefaultSoftwareDepots[0]
$DefaultSoftwareDepots
```

| 3 | Display a list of the array image profiles in the depot. |
| :--- | :--- |


```text
$profiles=Get-ESXImageProfile
$profiles
```

| 4 | Find the array index of the Standard image profile and export it to an offline bundle. |
| :--- | :--- |


```text
Export-ESXImageProfile -ImageProfile $profiles[index] ‘
                       -ExportToBundle -FilePath “C:\ESX_bundle.zip”
```

For information about using the offline bundle to upgrade ESXi host software, see [Create an Offline Bundle With VMware vSphere PowerCLI](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/f98d554a-3f1d-452c-bf5e-74b6e48ab37a/720b45cc-9f94-488d-90ed-e924134308ab/doc/GUID-3426299D-06D0-41AF-953D-6F3412B7D853.html#GUID-3426299D-06D0-41AF-953D-6F3412B7D853).

For more information about using vSphere PowerCLI with image profiles, see vSphere Installation and Setup.
