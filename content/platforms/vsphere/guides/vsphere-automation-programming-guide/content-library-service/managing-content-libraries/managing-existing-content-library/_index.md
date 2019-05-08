---
author: VMware
description:
draft: true
guide: vSphere Automation SDK Programming Guide
include_toc: true
layout: article
linktitle: Publishing an Existing Content Library
title: Publishing an Existing Content Library
toc: true
type: topic
weight: 2
---
To make the library content available for other vCenter Server instances across the vSphere Automation environment, you must publish the library. Depending on your workflow, select a method for publishing the local library. You can publish a local library that already exists in your vSphere Automation environment.

## Procedure

1. Retrieve a reference to the LocalLibrary service.

2. Retrieve an existing local library by using the library ID.

3. Create a PublishInfo instance to define how the library is published.

4. Specify the authentication method to be used by a subscribed library to authenticate to the local library. You can enable either basic authentication or no authentication. Basic authentication requires a user name and password.

5. (Optional) If you publish the library with basic authentication, you must specify a user name and password for the PublishInfo instance, which must be used for authentication.

6. Specify that the library is published.

7. Use the retrieved local library to configure it with the PublishInfo instance.

8. Update the properties of the LibraryModel object returned for the local library.

## Java Example

This example is based on the code in the LibraryPublishSubscribe.java sample file.

## .NET Example

This example shows how you can publish a local library, which already exists in your virtual environment.

## Python Example

## Perl Example
