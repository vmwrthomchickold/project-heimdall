---
author: VMware
draft: true
description:
guide: VDDK Programming Guide
include_toc: true
layout: vddk_programming_guide
title: Errors Codes and Open Source
type: topic
toc: true
weight: 10
---
## Recent Changes

In vSphere 6.0, the VDDK libraries were changed to return VIX\_E\_OUT\_OF\_MEMORY when an ESXi host runs out of memory for network file copy \(NFC\). Previously, VDDK would hang during I/O operations.

Error 2 – VIX\_E\_OUT\_OF\_MEMORY

Memory allocation failed. Out of memory.

## Finding Error Code Documentation

For a list of Virtual Disk API error codes, see the online reference guide Introduction to the VixDiskLib API:

| ■ | Windows – C:\Program Files\VMware\VMware Virtual Disk Development Kit\doc\intro.html |
| :--- | :--- |
| ■ | Linux – /usr/share/doc/vmware-vix-disklib/intro.html |

In a Web browser, click the Error Codes link in the upper left frame, and click any link in the lower left frame. The right-hand frame displays an alphabetized list of error codes, with explanations.

### Association With VIX API Errors

The Virtual Disk API shares many errors with the VIX API, which explains the VIX prefix. The error codes for the VIX API are likely to be the same, or almost the same as, a comparable release of the VDDK.

For information about the VIX API, including its online reference guide to functions and error codes, see the developer support section of the VMware Web site:

{% embed url="https://www.vmware.com/support/developer/vix-api/index.html" %}

### Interpreting Errors Codes

A VIX error is a 64-bit value. A value of VIX\_OK indicates success, but otherwise \(if there is an error\), several bit regions in the 64-bit value might be set. The least significant 16 bits are set to the error code described for VIX errors. More significant bit fields might be set to other values.

As for the VIX API, use the macro VIX\_ERROR\_CODE\(err\) to mask off bit fields not used by the VDDK.

## Troubleshooting Dynamic Libraries

Problem

On Windows, the SSL library is placed in the same directory as other vixDiskLib dynamically loaded libraries. On Linux, functions that load the librarieslibssl.so.0.9.8 and libcrypto.so.0.9.8 do the following:

| 1 | Attempt to load them from the environment’s LD\_LIBRARY\_PATH location. |
| :--- | :--- |
| 2 | Next, attempt to load them from the directory where libvixDiskLib.so is located. |
| 3 | Next, attempt to load them from the directory where the executable is located. |
| 4 | Failing that, exit with an error. |

On install, VDDK creates the directory /usr/lib/vimware-vix-disklib, populated with 64-bit executables and libraries placed into subdirectories bin64 and lib64. On determining the OS type, VDDK copies the vixDiskLib and vixMntapi libraries into /usr/lib. It does not copylibssl.so.0.9.8 or libcrypto.so.0.9.8 into /usr/lib.

On execution, the root user normally has no LD\_LIBRARY\_PATH, and /usr/lib is ahead of /opt/vmware/lib in the path. Running the lddcommand can help diagnose where a program is getting libvixDiskLib.so and other libraries. The /opt/vmware/lib directory is neither created nor updated by the VDDK install script.

If you see the error “Failed to load library libcrypto.so.0.9.8” there are several solutions:Solution

| ■ | Set or reset the LD\_LIBRARY\_PATH environment so it contains one of the directories above, /lib64 and possibly /bin64, before it contains /usr/lib. |
| :--- | :--- |
| ■ | Change the symbolic link in /opt/vmware/lib \(or elsewhere\) so it points to the directory above, /lib64. |
| ■ | Copy the libssl and libcrypto libraries from /usr/lib/vmware-vix-disklib/lib64 into /usr/lib. |

## Open Source Components

VDDK contains the following open source components, with license types indicated:

| ■ | Boost \(BSD style license\) |
| :--- | :--- |
| ■ | Curl \(MIT/X derivative license\) |
| ■ | Expat \(BSD style license\) |
| ■ | FreeBSD \(BSD license\) |
| ■ | ICU, International Components for Unicode \(BSD style license\) |
| ■ | LibXML2 \(MIT style license\) |
| ■ | OpenLDAP \(OpenLDAP v 2.8 license\) |
| ■ | OpenSSL \(OpenSSL license\) |
| ■ | Zlib \(BSD license\) |

These open source components have the GNU library general public license:

| ■ | GetText \(LGPL2.0\) |
| :--- | :--- |
| ■ | Glib \(LGPL 2.0\) |
| ■ | LibFuse \(LGPL2.0\) |
| ■ | LibIconv \(LGPL2.0\) |
