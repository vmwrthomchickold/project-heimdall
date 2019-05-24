---
author: VMware
draft: true
description:
guide: vSphere Automation SDK Programming Guide
include_toc: true
layout: vsphere_automation_sdk_programming_guide
title: Content Library Support for OVF and OVA Packages
type: topic
toc: true
weight: 7
---
You can use the objects and methods provided by the Content Library API to manage OVF and OVA packages.

Open Virtualization Format \(OVF\) is an industry standard that describes metadata about a virtual machine image in an XML format. An OVF package includes an XML descriptor file and optionally disk images, resource files \(such as ISO files\), manifest files, and certificate files.

An OVA package is a single file that contains all OVF package files in archived form. After you upload an OVA package to a content library, the OVA file is converted to the standard OVF package.

When you try to upload signed content to a content library, you might receive preview warnings. Signed content can be either OVF packages or OVA files that contain manifest and certificate files. If you do not respond to the preview warnings, the upload fails. To successfully complete an upload operation, you must ignore any preview warnings by using the WarningBehavior class.

With the vSphere Automation API, you can use the virtual machine \(VM\) and vApp templates from an OVF package in a content library to deploy VMs and virtual appliances on hosts, resource pools, and clusters. You can also use the API to create OVF packages in content libraries from virtual appliances and VMs on hosts, resource pools, and clusters.

When you create library items to store OVF packages, you must set the item type to ovf. To comply with the specific standards of the OVF packages, the vSphere Automation API provides the LibraryItem class.
