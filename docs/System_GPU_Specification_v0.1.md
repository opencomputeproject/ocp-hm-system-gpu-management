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

This document specifies the requirements for managing a GPU in a system.

# Requirements

## Manageability Interface

The system shall expose a Redfish Service (v??).

## Redfish data model

The Redfish Service shall conform to the following OCP Profiles
* [UBB_BaselineManagement profile v1.x](https://github.com/opencomputeproject/HWMgmt-OCP-Profiles/blob/master/gpu/OCP_UBB_BaselineManagement.v1.0.0.json)
* [OCP Server Profile v1.1](https://github.com/opencomputeproject/HWMgmt-OCP-Profiles/blob/master/Server/OCPServerHardwareManagement.v1_1_0.json)

### Firmware Management
### Redfish Component Integrity

## Redfish Aggregation Behavior

The system should follow the guidance of the *(insert document name*) (GPU specific version)

## Redfish Message Registry

# PLDM Requirements

# Security Requirements (SPDM)

# Physical Interfaces and connections
## USB Requirements

# Performance requirements (e.g. KPIs)

# Testing Conformance to Profiles

The [Redfish Interop Validator](https://github.com/DMTF/Redfish-Interop-Validator) reads a profile. The validator autogenerates tests, executes the tests against an implementation, and generated a report.  The validator is invoked the the following command.

  \$\> python3 RedfishInteropValidator.py profileName --ip host:port



# Revision 

| Revision/Version | Date | Description |
|----|----|----|
| 0.1 | 9/12/2025 |initial draft |

