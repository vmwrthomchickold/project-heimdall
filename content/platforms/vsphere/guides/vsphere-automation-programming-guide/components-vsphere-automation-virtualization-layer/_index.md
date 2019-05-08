---
author: VMware
description:
draft: true
guide: vSphere Automation SDK Programming Guide
include_toc: true
layout: article
linktitle: Components of the vSphere Automation Virtualization layer
title: Components of the vSphere Automation Virtualization layer
toc: true
type: topic
weight: 3
---
At the core of vSphere Automation is vSphere, which provides the virtualization layer of the software-defined data center. You can use vSphere deployment options for vCenter Server and ESXi hosts to build virtual environments of different scales.

## Components and Services of a Virtual Environment

Starting with vSphere 6.0, the deployment of the virtual environment consists of two major components that provide different sets of services, the VMware Platform Services Controller and vCenter Server. You can deploy vCenter Server with an embedded or external Platform Services Controller.

Services Installed with Platform Services Controller
The Platform Services Controller group of infrastructure services contains vCenter Single Sign-On, License Service, Lookup Service, and VMware Certificate Authority. The services installed with the Platform Services Controller are common to the entire virtual environment. A Platform Services Controller can be connected to one or more vCenter Server instances. In a deployment that consists of more than one Platform Services Controller, the data of each service is replicated across all Platform Services Controller instances.

In vSphere Automation API client applications, you use the vCenter Single Sign-On and the Lookup Service on the Platform Services Controller to provide a range of functionality.

## vSphere Deployment Configurations
vSphere Automation client applications communicate with services on the Platform Services Controller and vCenter Server components of the virtual environment. vCenter Server can be deployed with an embedded or external Platform Services Controller.

### vCenter Server with an Embedded Platform Services Controller

vCenter Server and Platform Services Controller reside on the same virtual machine or physical server. This deployment is most suitable for small environments such as development or test beds.

You can use the Platform Services Controller in two ways to establish secure, authenticated sessions for your client application, by making requests to the Lookup Service and the vCenter Single Sign-On Service.

One way to use the Platform Services Controller is to request an authentication token that can be used to authenticate requests across services. The client connects to the Lookup Service and retrieves the vCenter Single Sign-On Service endpoint and the vSphere Automation API endpoint. The client then uses the vCenter Single Sign-On endpoint to authenticate with user credentials and receive a token that securely verifies the client's credentials. This allows the client to authenticate with a number of service endpoints without sending user credentials over the network repeatedly.

Alternatively, if the client connects directly to the vSphere Automation API endpoint, there is no need for the client to interact with the vCenter Single Sign-On Service. The client sends user credentials to the vSphere Automation API endpoint, which creates a session identifier that persists across requests.

### vCenter Server with an External Platform Services Controller

In the case of an external Platform Services Controller, the vCenter Server and the Platform Services Controller are deployed on separate virtual machines or physical servers. The Platform Services Controller can be shared across several vCenter Server instances. For larger deployments or to provide better availability, more than one Platform Services Controller can be deployed. When configured as replication partners within a single vCenter Single Sign-On domain, Platform Services Controller instances replicate all user and system data within the cluster.

A client application functions in a similar way as in a Platform Services Controller with embedded vCenter Server deployment. The only difference is that the client application can access services on multiple vCenter Server instances, or services only on a particular vCenter Server instance.
