---
api:
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
description: "The Error error describes thefields common to all standard errors."
devcenter:
images:
keywords:
lastmod:
layout: "structure"
linkTitle:
platform:
product_name:
product_version:
programming_lang:
resources:
series:
slug:
tags:
title: "com.vmware.vapi.std.errors.error"
type: "structure"
url:
videos:
weight:
---
This error serves two purposes:

1. It is the error that clients in many programming languages can catch to handle all standard errors. Typically those clients will display one or more of the localizable messages from messages a human.
2. It is the error that operations can report when they need to report some error, but the error doesn't fit into any other standard error, and in fact the only reasonable way for a client to react to the error is to display the message(s) to a human.
