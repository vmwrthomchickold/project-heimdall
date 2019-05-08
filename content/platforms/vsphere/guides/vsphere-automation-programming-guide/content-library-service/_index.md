---
author: VMware
description:
draft: true
guide: vSphere Automation SDK Programming Guide
include_toc: true
layout: article
linktitle: Content Library Service
title: Content Library Service
toc: true
type: topic
weight: 6
---
The Content Library Service provides means for managing content libraries in the context of a single or multiple vCenter Server instances deployed in your virtual environment. You can use the vSphere Automation APIs to access the Content Library Service through the vSphere Automation Endpoint.

Administrators can use content libraries to share VM templates, vApp templates, and other types of files across vCenter Server instances in the virtual environment. Sharing templates across your virtual environment promotes consistency, compliance, efficiency, and automation in deploying workloads at scale.

## Content Libraries at a Glance

A content library instance represents a container for a set of library items. A content library item instance represents the logical object stored in the content library, which might be one or more usable files.

## Content Library Types

You can create two types of libraries: Local, and Subscribed.

- You can create a local library as the source for content you want to save or share. Create the local library on a single vCenter Server instance. You can add items to a local library or remove them. You can publish a local library and as a result this content library service endpoint can be accessed by other vCenter Server instances in your virtual environment. When you publish a library, you can configure the authentication method, which a subscribed library must use to authenticate to it.

- You can create a subscribed library and populate its content by synchronizing to a local library. A subscribed library contains copies of the local library files or just the metadata of the library items. The local library can be located on the same vCenter Server instance as the subscribed library, or the subscribed library can reference a local library on a different vCenter Server instance. You cannot add library items to a subscribed library. You can only add items to the source library. After synchronization, both libraries will contain the same items.

## Content Library Items

Library items are virtual machine templates, vApp templates, or other VMware objects that can be contained in a content library. VMs and vApps have several files, such as log files, disk files, memory files, and snapshot files that are part of a single library item. You can create library items in a specific local library or remove items from a local library. You can also upload files to an item in a local library so that the libraries subscribed to it can download the files to their NFS or SMB server, or datastore.

For information about the tasks that you can perform by using the content library service, see Content Libraries.

## Content Library Storage

When you create a local library, you can store its contents on a datastore managed by the vCenter Server instance or on a remote file system.

Depending on the type of storage that you have, you can use Virtual Machine File System (VMFS) or Network File System (NFS) for storing content on a datastore.

For storing content on a remote file system, you can enter the path to the NFS storage that is mounted on the Linux file system of the vCenter Server Appliance. For example, you can use the following URI formats: nfs://<server>/<path>?version=4 and nfs://<server>/<path>. If you have a vCenter Server instance that runs on a Windows machine, you can specify the Server Massage Block (SMB) URI to the Windows shared folders that store the library content. For example, you can use the following URI format: smb://<unc-server>/<path>.

### Java Example

This example of storing library content is based on the code in the LibraryCrud.java sample file.

### .NET Example

### Python Example
