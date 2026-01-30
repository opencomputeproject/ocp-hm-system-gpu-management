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

This document specifies the requirements for managing GPUs within a system.

## Scoping a GPU System

The scope of a GPU system may span multiple-racks. This definition is taken from the system managment section of the [Open Systems of AI whitepaper](https://www.opencompute.org/documents/ocp-open-systems-for-ai-whitepaper-v1-0-0-final-pdf) [1].

The [OCP Open Cluster Designs for AI](hhttps://www.opencompute.org/documents/white-paper-open-cluster-designs-for-ai-final-14-january-2026-pdf) [2] refers to the functional and physical aspects of an AI system as 'cluster' and 'pod', respectively.  Representing the physical aspects, the pod contains the power and cooling systems.

Both documents describe the possible management hierarchy (from node, to rack, to cluster, to DC manager), and specify Redfish (tm) [6] as the out-of-band management interface for the system [6]

At the top level of the Redfish model [7], there exista the the 'System' resource and the 'Chassis' resource to represent the functional and physical aspects of a managed entity, respectively.    The model also contains  'Manager' resource to represent the management hierarchy of a managed entity. *TODO - add high level diagram*.

has as phA GPU System is describe phyiscally as a pod, the functional aspect is termed a cluster.Within the document, system shall be an entity:

* Bounded by a platform or chassis
* Whose out-of-band remote manageability interface is Redfish over Ethernet
* Contains one or more GPUs

* 
* Shall contain one or more GPUs - which may be on a subsystem (UBB) or discrete
* Use Redfish, PLDM and SPDM for local manageability
* 

The system:

* Shall contain a power subsystem
* Shall contain compute complexes
* Shall contain a cooling and thermal subsystem
* May contain a management controller hierarchy

The GPU system is the foundation of a datacenter manageament hierarchy, which might include the rack manager, row manager, and datacenter manager.

** TBD - Are disaggregated GPU Systems included? - with GPU shelves, JBODs, etc**

# Redfish Requirements

The Redfish interface shall conform to the [Redfish Specification](https://www.dmtf.org/dsp/DSP0266) [6]

## GPU node manageability

The Redfish Service shall conform to the following OCP Profiles

| **Profile**         | **Usage Guide**             
| :---                  | :-----------       |
| [UBB_BaselineManagement](https://github.com/opencomputeproject/HWMgmt-OCP-Profiles/blob/master/gpu/OCP_UBB_BaselineManagement.v1.0.0.json) | [OCP GPU & Accelerator Management Interfaces](https://www.opencompute.org/documents/ocp-gpu-accelerator-management-interfaces-v1-1-pdf)  [3] |
| [OCP Server](https://github.com/opencomputeproject/HWMgmt-OCP-Profiles/blob/master/Server/OCPServerHardwareManagement.v1_1_0.json) | [Usage Guide for OCP Server Management](https://www.opencompute.org/documents/usage-guide-server-hw-mgmt-profile-v1-0-0final-pdf-1) |
| [OCP GPU Management]() | Usage Guide

## GPU cluster/pod manageability

From the Usage Guide for Rack Management (OpenRMC) v1.2

| **Use Case**        | **Management Task**                                       | **Requirement** |
| :---                | :-----------                                              | :---	|
| Composability       | [Construct a system with GPUs](#construct-a-system-with-gpus)     | Recommended |
|                     | [Get Health of GPUs from composed compute system](#get-health-of-gpus-from-composed-compute-system) | Recommended |
|                     | [Set policy when a GPU in a composed system fails](#set-policy-when-a-gpu-in-a-composed-system-fails) | Recommended |

## Testing Conformance to Profiles

The [Redfish Interop Validator](https://github.com/DMTF/Redfish-Interop-Validator) reads a profile. The validator autogenerates tests, executes the tests against an implementation, and generated a report.  The validator is invoked the the following command.

  \$\> python3 RedfishInteropValidator.py profileName --ip host:port

## Redfish Aggregation Behavior

In order to improve interoperablity, the behavior of the Redfish data model for the system should be consistent . The guidance of the ]Redfish Aggregation Guidance for Compute Expansion Modules](https://www.dmtf.org/sites/default/files/standards/documents/DSP2090_1.0.0.pdf) [20] should be followed.

## Firmware Management

The Redfish Service should support the MultipartHttpPushUri. If MultipartHttpPushUri property is not supported, then the HttpPushUri property shall be supported.

*Note*: The HttpPushUri property is 'deprecated' in favor of the MultiPartHttpPushUri property.


## Redfish Message Registry

# PLDM Requirements

## Firmware Management

The GPU shall support the requirements specified in the ["GPU Firmware Update Requirements v1.1"](https://www.opencompute.org/documents/ocp-gpu-fw-update-specification-v1-1-pdf)

The CPU shall support the requirements specified in the "CPU Hyperscale Impactless Firmware Update v1.0". The current draft is [v0.7](https://www.opencompute.org/documents/hyperscale-cpu-impactless-firmware-updates-requirements-specification-v0-7-9-29-2025-pdf) (2025).

## Sensors and Effecters

# Security Requirements (SPDM)

## Redfish Component Integrity

# Physical Interfaces and connections

## USB Requirements

# Performance requirements (e.g. KPIs)


# References

\[1\] "[OCP Open Systems of AI Whitepaper](https://www.opencompute.org/documents/ocp-open-systems-for-ai-whitepaper-v1-0-0-final-pdf)"

\[2\] "[OCP Open Cluster Designs for AI](hhttps://www.opencompute.org/documents/white-paper-open-cluster-designs-for-ai-final-14-january-2026-pdf)"

\[3\] "[OCP Usage Guide for Managing a Server](https://www.opencompute.org/documents/usage-guide-server-hw-mgmt-profile-v1-0-0final-pdf-1)"

\[4\] "[OCP Usage Guide for Managing a Rack (OpenRMC)](https://www.opencompute.org/documents/openrmc-usageguide-r1-1-draft0-6-1-1-pdf)"

\[5\] "[OCP GPU & Accelerator Management Interfaces v1.1](https://www.opencompute.org/documents/ocp-gpu-accelerator-management-interfaces-v1-1-pdf)"

\[6\] "[Redfish Specification](https://www.dmtf.org/dsp/DSP0266)"

\[7\] "[Redfish Data Model Specification](https://www.dmtf.org/dsp/DSP0268)"

\[8\] "[Redfish Interop Validator](https://github.com/DMTF/Redfish-Interop-Validator)"

\[9\] "[Redfish Interoperability Profile Specification](https://www.dmtf.org/dsp/DSP0272)"

\[10\] "[UBB_BaselineManagement profile v1.x](https://github.com/opencomputeproject/HWMgmt-OCP-Profiles/blob/master/gpu/OCP_UBB_BaselineManagement.v1.0.0.json)

\[11\] "[OCP Server Profile v1.1](https://github.com/opencomputeproject/HWMgmt-OCP-Profiles/blob/master/Server/OCPServerHardwareManagement.v1_1_0.json)

\[12\] "[OCP GPU Management Profle vx.x]()

\[20\] "[Redfish Aggregation Guidance for Compute Expansion Modules](https://www.dmtf.org/sites/default/files/standards/documents/DSP2090_1.0.0.pdf)" 

# Revision 

| Revision/Version | Date | Description |
|----|----|----|
| 0.1 | 1/12/2026 |initial draft |

