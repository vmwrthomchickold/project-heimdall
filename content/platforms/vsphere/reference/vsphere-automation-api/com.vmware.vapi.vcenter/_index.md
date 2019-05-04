---
api: "vSphere Automation"
api_method:
api_parameters:
api_path:
api_response:
api_return_type:
api_structures:
author:
categories:
date: 2019-02-08T16:56:22-08:00
draft: true
description: "The vAPI REST Navigation package provides operations that allow a vAPI REST client to navigate between Resources, Components and their Operations."
devcenter:
images:
keywords:
lastmod:
layout: "component"
linkTitle:
platform:
product_name:
product_version:
programming_lang:
resources:
series:
slug:
tags:
title: "com.vmware.vapi.rest.navigation"
type: "component"
url:
videos:
weight:
---
## Overview
You can use this functionality by invoking methods of following services:

  - The Root service is the entry point to vAPI REST.
  - The Resource service contains functionality that allows a vAPI REST client to explore Resources and their Operations.
  - The Component service contains functionality that allows a vAPI REST client to explore Components, their Services and Operations.
  - The Options service contains functionality that allows a vAPI REST client to explore what functionality can be called on a URL.
  - vAPI REST Navigation service methods are producing URLs. They use base URL parameter to produce absolute URLs. Base URL parameter is optional and if it is not provided services will produce relative URLs. Additional information is served for all vAPI REST URLs. Following a vAPI REST URL is actually executing a vAPI operation, detailed information for which is referred to as meta data. It contains operation's parameters, result and possible error conditions.
