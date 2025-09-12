---
project: Hardware Management
title: System GPU Management Specification
version: 0.1.0
supersedes: none
status: draft
released: true
class: info
date: 2025-09-12
copyright: 2025
paragraph_numbering: no
bibliography: bibliography.yaml
header-includes: |
  \newenvironment{smallcode}{\begin{footnotesize}}{\end{footnotesize}}
...

---

\tableofcontents

---

# License

This work is licensed under a [Creative Commons Attribution-ShareAlike
4.0 International
License](https://creativecommons.org/licenses/by-sa/4.0/).

<img src="media/image2.png" style="width:1.37869in;height:0.48237in" />

# Scope

This document specifies the requirement for managing a GPU in a system.

# Requirements

## Manageability Interface

The system shall expose a Redfish-based interface (v??).

## Redfish data model

The Redfish interface shall conform to the following OCP Profiles
* [UBB_BaselineManagement profile v1.x](https://github.com/opencomputeproject/HWMgmt-OCP-Profiles/blob/master/gpu/OCP_UBB_BaselineManagement.v1.0.0.json)
* [OCP Server Profile v1.1](https://github.com/opencomputeproject/HWMgmt-OCP-Profiles/blob/master/Server/OCPServerHardwareManagement.v1_1_0.json)

## Aggregation Behavior

The system should follow the guidance of the *(insert document name*)

# Testing Conformance to Profiles

A profile is read by the Redfish Interop Validator which autogenerates and executes the tests against an implementation.  The Interop Validator generates a test report – in text or HTML format.

  \$\> python3 RedfishInteropValidator.py profileName --ip host:port

The [Redfish Interop Validator](https://github.com/DMTF/Redfish-Interop-Validator) is open source)

# Revision 

| Revision/Version | Date | Description |
|----|----|----|
| 0.1 | 9/12/2025 |initial draft |

