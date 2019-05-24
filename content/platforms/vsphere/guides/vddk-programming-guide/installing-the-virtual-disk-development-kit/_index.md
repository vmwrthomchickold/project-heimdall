---
author: VMware
draft: true
description:
guide: VDDK Programming Guide
include_toc: true
layout: vddk_programming_guide
title: Installing the Virtual Disk Development Kit
type: topic
toc: true
weight: 3
---
To develop virtual disk applications, install the VDDK as described in this chapter. For backup applications, VADP development also requires the vSphere Web Services SDK.

## Prerequisites

This section covers what you need to begin VDDK and VADP development.

### Development Systems

The VDDK has been tested and is supported on the following systems:

| ■ | Windows 64-bit \(x86-64\) systems |
| :--- | :--- |
| ■ | Linux 64-bit \(x86-64\) systems |

See the VDDK Release Notes for specific versions, which change over time. Mac OS X is not supported.

### Programming Environments

You can compile the sample program and develop vSphere applications in the following environments:

#### Visual Studio on Windows

On Windows, programmers can use the C++ compiler in Visual Studio 2005, Visual Studio 2008, and later. Along with Visual Studio, you also need to install the 64-bit debugging tools.

#### C++ and C on Linux

On Linux, programmers can use the GNU C compiler, version 4 and higher. The sample program compiles with the C++ compiler g++, but VDDK also works with the C compiler gcc.

#### Java Development for VADP

When developing backup and restore software to run on vSphere, VMware recommends Eclipse with Java, on both Windows and Linux. The vSphere Web Services SDK now includes both Axis and JAX-WS bindings. You can call C or C++ code with wrapper classes, as in Java Native Interface \(JNI\).

### VMware Platform Products

Software applications developed with the VDDK and VADP target the following platform products:

| ■ | vCenter Server managing ESXi hosts |
| :--- | :--- |
| ■ | ESXi hosts directly connected |

See the VDDK Release Notes for specific versions, which change over time.

Hosted products including VMware Workstation are neither tested nor supported.

### Storage Device Support

VMware Consolidated Backup \(VCB\) had knowledge base article http://kb.vmware.com/kb/1007479 showing the support matrix for storage devices and multipathing. VMware does not provide a similar support matrix for VDDK and VADP. Customers must get this information from you, their backup software vendor.

## Installing the VDDK Package

The VDDK is packaged as a compressed archive for Windows 64-bit and for Linux 64-bit. The VDDK packages include the following components:

| ■ | Header files vixDiskLib.h and vm\_basic\_types.h in the include directory. |
| :--- | :--- |
| ■ | Function library vixDiskLib.lib \(Windows\) or libvixDiskLib.so \(Linux\) in the lib directory. |
| ■ | HTML reference documentation in the doc directory and sample program in doc/samples. |

Note: In the VDDK 5.5 release, VMware discontinued the Windows installer and 32-bit executables for Windows and Linux. The VDDK is available for 64-bit systems only.

To install the package on Windows:

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">On the Download page, choose the .zip file for Windows and download it
        to your development system.</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">Place the .zip file in a folder under Program Files &#x2013; you can choose
        the name &#x2013; and unpack it:</td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left">
        <p>Go to the bin subfolder, locate the vstor2install.bat script, and double-click
          to run it. The batch script should be run in place so that the current
          directory for execution is the bin subfolder. By running it, you implicitly
          accept the VMware license terms.Note</p>
        <p>If vstor2 is already installed on a backup proxy, you should first uninstall
          it with vstor2uninstall.bat (from its corresponding VDDK version) then
          run the new install script.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">4</td>
      <td style="text-align:left">
        <p>Edit the Windows registry with regedit and check for the following key.
          If this key exists from a previous VDDK install, right-click to delete
          it. Add a registry entry with the following key:</p>
        <p>HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\VMware, Inc.\VMware Virtual Disk
          Development Kit</p>
        <p>For convenience you might want to edit the Windows Path environment to
          include the VDDK installation folder, C:\Program Files\VMware\VDDK550\bin
          in the example above.</p>
      </td>
    </tr>
  </tbody>
</table>To Install the package on Linux:

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">On the Download page, choose the binary tar.gz for 64-bit Linux.</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">Unpack the archive with tar to create the vmware-vix-disklib-distrib subdirectory.</td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left">
        <p>Change to the newly created directory to see its subdirectories:</p>
        <p>The bin64 subdirectory contains a reporting program and virtual disk manager.
          License terms are in the doc/EULA file. The sample program is under doc/samples.
          Header files in the include subdirectory are for compiling your VDDK applications.
          Normally the lib64 components are installed under /usr/lib, /usr/lib/vmware-vix-disklib/lib64
          for instance.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">4</td>
      <td style="text-align:left">Install components as needed. You might want to edit your LD_LIBRARY_PATH
        environment to include the library installation path. Alternatively, you
        can add the library location to the list in /etc/ld.so.conf and run ldconfig
        as the superuser.</td>
    </tr>
  </tbody>
</table>### Repackaging VDDK Libraries

After you develop an application based on VDDK, you might need the VDDK binaries to run your application.

As described in [Redistributing VDDK Components](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-282C600E-D986-4592-9206-70BF60DBF684.html), partners can sign a license agreement to redistribute VDDK binaries that support VADP applications.

To enable VDDK binaries on Windows virtual machines without VDDK installedProcedure

| 1 | Install the Microsoft Visual C++ \(MSVC\) redistributable, possibly as a merge module. The latest MSVC runtime works as side-by-side component, so manually copying it might not work on Vista. See details on the Microsoft Web site for the redistributable package, x86 processors or x64 processors. Side-by-side is also explained on the Microsoft Web site. |
| :--- | :--- |
| 2 | Install VMware executables and DLLs from the \bin and \lib folders of the installed VDDK, and the vstor2-mntapi10.sys driver into the Windows\system\drivers folder or equivalent. |
| 3 | Create and install your application, compiled in a manner similar to the vixDiskLibSample.exe code, discussed in [Virtual Disk API Sample Code](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-AD789C9E-8813-4F2A-A874-DA00B3447BC1.html). |

### How to Find VADP Components

ESXi hosts and vCenter Server similarly implement managed objects that support inventory traversal and task requests. Before you write VADP software in Java, you need to download the vSphere Web Services SDK. You can find documentation and ZIP file for download on the VMware Web site.
