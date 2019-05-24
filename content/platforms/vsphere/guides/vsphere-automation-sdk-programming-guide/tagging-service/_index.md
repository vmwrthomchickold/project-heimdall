---
author: VMware
draft: true
description:
guide: vSphere Automation SDK Programming Guide
include_toc: true
layout: vsphere_automation_sdk_programming_guide
title: Tagging Service
type: topic
toc: true
weight: 8
---
The vSphere Automation Tagging Service supports the definition of tags that you can associate with vSphere objects or vSphere Automationresources. vSphere has a tagging feature but no public API to manage tags. With the vSphere Automation SDK, you can manage tags programmatically.

For example, to tag your VMs by guest operating system type, you might create a category called operating system, and specify that it applies to VMs only. You might also specify that only a single tag can be applied to a VM at any time. The tags in this category might be Windows, Linux, and Mac OS.
