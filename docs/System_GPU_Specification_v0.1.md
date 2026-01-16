---
project: Hardware Management
title: System GPU Management Specification
version: 0.1.0
supersedes: none
status: draft
released: true
class: info
date: 2026-01-12
copyright: 2026
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

## Scoping a GPU System

Within the document, system shall be an entity:

* Bounded by a platform or chassis
* Whose out-of-band remote manageability interface is Redfish over Ethernet
* Contains one or more GPUs

The system is the foundation of a datacenter manageament hierarchy, which might include the rack manager, row manager, and datacenter manager.

The system:

* Shall contain a power subsystem
* May contain CPUs in addition to one or more GPUs
* May contain a cooling subsystem

# Redfish Requirements

## Redfish Interface

The Redfish interface should be Redfish Specification v1.22, [DSP0266](https://www.dmtf.org/dsp/DSP0266)

## Redfish data model

The Redfish Service shall conform to the following OCP Profiles
* [UBB_BaselineManagement profile v1.x](https://github.com/opencomputeproject/HWMgmt-OCP-Profiles/blob/master/gpu/OCP_UBB_BaselineManagement.v1.0.0.json)
* [OCP Server Profile v1.1](https://github.com/opencomputeproject/HWMgmt-OCP-Profiles/blob/master/Server/OCPServerHardwareManagement.v1_1_0.json)

## Firmware Management

The Redfish Service should support the MultipartHttpPushUri. If MultipartHttpPushUri property is not supported, then the HttpPushUri property shall be supported.

*Note*: The HttpPushUri property is 'deprecated' in favor of the MultiPartHttpPushUri property.

## Redfish Component Integrity

## Redfish Aggregation Behavior

The system is an assemblage of subsystems. In order to improve interoperablity, the Redfish data model of the the assemblage should be consistent . Hence, the guidance of the "Redfish Aggregation Guidance for Compute Expansion Modules", [DSP2090](https://www.dmtf.org/sites/default/files/standards/documents/DSP2090_1.0.0.pdf) should be followed.

## Redfish Message Registry

# PLDM Requirements

## Firmware Management

The GPU shall support the requirements specified in the ["GPU Firmware Update Requirements v1.1"](https://www.opencompute.org/documents/ocp-gpu-fw-update-specification-v1-1-pdf)

The CPU shall support the requirements specified in the "CPU Hyperscale Impactless Firmware Update v1.0". The current draft is [v0.7](https://www.opencompute.org/documents/hyperscale-cpu-impactless-firmware-updates-requirements-specification-v0-7-9-29-2025-pdf) (2025).

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
| 0.1 | 1/12/2026 |initial draft |

