---
author: VMware
draft: true
description:
guide: CIM SMASH/Server Management API Programming Guide
include_toc: true
layout: cim_smash_server_api_programming_guide
menu: 
  cim_smash_server_api_programming_guide:
    parent: index
title: About This Guide
type: topic
toc: true
weight: 1
---
The CIM SMASH/Server Management API Programming Guide provides information about developing applications using the CIM SMASH/Server Management API.

VMware® provides many different APIs and SDKs for various applications and goals. This book provides information about developing management clients that use industry-standard data models. The System Management Architecture for Server Hardware \(SMASH\) is an industry standard for managing server hardware. This book describes the SMASH profiles implemented by VMware and contains suggestions for using the Common Information Model \(CIM\) classes to accomplish common use cases.

# Revision History

This book is revised with each release of the product or when necessary. A revised version can contain minor or major changes. The Revision History table summarizes the significant changes in each version of this book.

<table>
  <thead>
    <tr>
      <th style="text-align:left">Revision</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">20180417</td>
      <td style="text-align:left">Update for vSphere 6.7.</td>
    </tr>
    <tr>
      <td style="text-align:left">20161115</td>
      <td style="text-align:left">Update for vSphere 6.5. Removes support for CIM_SoftwareInstallationService.</td>
    </tr>
    <tr>
      <td style="text-align:left">20150312</td>
      <td style="text-align:left">Re-released for vSphere 6.0. No revisions.</td>
    </tr>
    <tr>
      <td style="text-align:left">20130912</td>
      <td style="text-align:left">Minor corrections only.</td>
    </tr>
    <tr>
      <td style="text-align:left">20120910</td>
      <td style="text-align:left">
        <p>Updated to include information about adding CIM Provider VIBs.</p>
        <p>Updated to include information about adjusting resource pool allocation.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">20110824</td>
      <td style="text-align:left">
        <p>Added PCI Device use cases.</p>
        <p>Corrected Software Update use cases to match current design.</p>
        <p>Updated product version numbers.</p>
        <p>Corrected CIM profile version numbers.</p>
        <p>Removed Host Hardware RAID Controller profile support from default configuration.</p>
        <p>Revised Perl WS-Management section to bypass deprecated StubOps module.</p>
        <p>Removed section about Rebooting the Managed Server (deprecated feature).</p>
        <p>Revised sections about manufacturer, model, and serial number.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">20100430</td>
      <td style="text-align:left">
        <p>Added Active Directory Authentication.</p>
        <p>Added WS-Management code sample.</p>
        <p>Added Software Update use cases.</p>
        <p>Corrected Software Inventory use case.</p>
        <p>Updated version numbers for vSphere 4.1 release.</p>
        <p>Added Software Inventory use case.</p>
        <p>Corrected error in RAID controller illustration.</p>
        <p>Added information on crossing namespace boundaries.</p>
        <p>Corrected error in WS-Man Resource URI for VMware classes.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">20090521</td>
      <td style="text-align:left">
        <p>Updated product names for vSphere 4.0 release.</p>
        <p>Added use cases for SEL, and physical memory slots.</p>
        <p>Added namespace, ports, and XML schema information.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">20080703</td>
      <td style="text-align:left">
        <p>VMware ESX&#x2122; Server 3.5 Update 2 and ESX Server 3i version 3.5 Update
          2 release.</p>
        <p>Replaced instance diagrams with expanded versions.</p>
        <p>Added use case for CPU core &amp; threading model.</p>
        <p>Added use case for fan redundancy.</p>
        <p>Added use cases for Host Hardware RAID Controller profile.</p>
        <p>Added appendix about troubleshooting connections.</p>
        <p>Replaced Profile Reference appendix with a URL.</p>
        <p>Listed indications supported.</p>
        <p>Added ESX Server 3.5.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">20080409</td>
      <td style="text-align:left">
        <p>ESX Server 3i version 3.5 Update 1 release.</p>
        <p>Changed title (formerly CIM SMASH API Programming Guide)</p>
        <p>Updated URLs.</p>
        <p>Removed List of Tables.&#x2028;Added Physical Asset profile; listed properties
          for all profiles.</p>
        <p>Updated ElementName of Base Server registered profile.</p>
        <p>Added SMI-S RAID Controller profile.</p>
        <p>Divided chapter 2 into 2 parts, and expanded introductory material.</p>
        <p>Corrected typographical errors.</p>
        <p>Added some illustrations.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">20071210</td>
      <td style="text-align:left">ESX Server 3i version 3.5 release.</td>
    </tr>
  </tbody>
</table>

# Intended Audience

This book is intended for software developers who create applications that need to manage VMware vSphere® server hardware with interfaces based on CIM standards.

# VMware Technical Publications Glossary

VMware Technical Publications provides a glossary of terms that might be unfamiliar to you. For definitions of terms as they are used in VMware technical documentation, go to [http://www.vmware.com/support/pubs](http://www.vmware.com/support/pubs). Document Feedback

VMware welcomes your suggestions for improving our documentation. Send your feedback to [docfeedback@vmware.com](mailto:docfeedback@vmware.com).

# Technical Support and Education Resources

The following sections describe the technical support resources available to you. To access the current versions of other VMware books, go to [http://www.vmware.com/support/pubs](http://www.vmware.com/support/pubs).

## Online Support
To use online support to submit technical support requests, view your product and contract information, and register your products, go to [http://communities.vmware.com/community/developer](http://communities.vmware.com/community/developer).

## Support Offerings

To find out how VMware support offerings can help meet your business needs, go to [http://www.vmware.com/support/services](http://www.vmware.com/support/services). VMware Professional Services

VMware Education Services courses offer extensive hands-on labs, case study examples, and course materials designed to be used as on-the-job reference tools. Courses are available onsite, in the classroom, and live online. For onsite pilot programs and implementation best practices, VMware Consulting Services provides offerings to help you assess, plan, build, and manage your virtual environment. To access information about education classes, certification programs, and consulting services, go to [http://www.vmware.com/services](http://www.vmware.com/services/).
