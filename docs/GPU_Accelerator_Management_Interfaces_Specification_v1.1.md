# OCP GPU & Accelerator Management Interfaces
## Normative Specification v1.1 (Full, lossless transformation)
**Derived from:** *OCP GPU & Accelerator Management Interfaces, Version 1.1* (source PDF)

> **Note (non-normative):** This document is a **normative transformation** of the v1.1 PDF source into testable requirements. Narrative material from the source is retained to avoid loss of intent; where the source uses prose/tables/figures to imply requirements, those requirements are stated explicitly using RFC 2119 keywords.

---

## Authors (from source)
David Blocker (NVIDIA), James Bodner (NVIDIA), Weifu Chen (Microsoft), Deepak Kodihalli (NVIDIA), Shrikant Giridhar (NVIDIA), Johan van de Groenendaal (NVIDIA), Vishal Jain (NVIDIA), Justin King (AMD), Choudary Maddukuri (Microsoft), Rajat Madhusudan (Microsoft), Hari Ramchandran (Microsoft), Sivakumar Sathappan (AMD), Krishna Sugumaran (AMD), Balaji Vembu (Meta), Linda Wu (NVIDIA), Justin York (Google)

---

## Executive Summary (retained)
Management of GPUs is not standardized, resulting in significant effort and time to onboard each new HW design. The lack of standardization is also a burden on suppliers who must accommodate varying requirements from their customers. This document describes industry standard formats and protocols that make it easier for CSPs (Cloud Service Providers, hyperscalers) to onboard new GPU and accelerator designs with less toil and faster time to market while reducing manageability permutations for suppliers.

---

## Table of Contents (informative)
1. Introduction
2. Overview
   - 2.1 Discrete Accelerator Devices Manageability
   - 2.2 UBB Accelerator Devices Manageability
3. Discrete Accelerator Device Interfaces
   - 3.1 Access Frequency
   - 3.2 FRU Access and Data Model
   - 3.3 MCTP
   - 3.4 SPDM
   - 3.5 PLDM
   - 3.6 MCTP OEM VDM
4. UBB Accelerator Device Interfaces
   - 4.1 Redfish
   - 4.2 Out-of-Band Interfaces (MCTP, PLDM, SPDM)
   - 4.3 SPDM Over Redfish
5. Conclusion
6. Glossary
7. References
8. License - Open Web Foundation (OWF) CLA
9. OCP Tenets
10. About Open Compute Foundation

---

## 1. Introduction
Advances in accelerator-based workloads are driving the need for ever-faster bring-up and deployment of new accelerator-based system designs. Different accelerator components from different vendors implement a wide variety of management interfaces with little commonality. This lack of commonality causes extra toil and increases time-to-market for deploying new, innovative designs.

This document describes a common and generic framework for managing GPU and accelerators by way of management interfaces designed using standards-based management protocols.

This document covers both discrete accelerator devices (e.g., PCI-e CEM cards) and Universal Baseboard (UBB) designs (e.g., OCP OAI HGX).

*PCI-SIG®, PCI Express®, and PCIe® are registered trademarks of PCI-SIG*

### 1.1 Normative intent
Implementations claiming conformance to this specification **SHALL** satisfy all requirements marked **SHALL** and **SHALL NOT**.
Recommendations marked **SHOULD** are strongly advised for hyperscale interoperability; if not implemented, the implementation **SHALL** document and justify the deviation.
Optional features marked **MAY** are permitted and do not affect conformance.

---

## 2. Overview
The section specifies the interfaces for managing both Discrete and UUB design GPU/Accelerators.

### 2.1 Discrete Accelerator Devices Manageability
Discrete accelerator devices are GPUs packaged as standard CEM form-factor PCI-e adapters. Discrete accelerator device management can be broken down into several categories, each of which tie-in to various industry standards.

#### Table 1 – DMTF Standard Protocols (normative mapping)
Implementations **SHALL** support the protocols indicated for each manageability objective.

| Manageability Objective | Technology | Standard |
|---|---|---|
| Static discovery | IPMI FRU | Platform Management FRU Information Storage Definition (rev. 1.3) |
| Transport protocol | MCTP | DMTF DSP0236 revision 1.3.1 or later |
| Attestation | SPDM | DMTF DSP0274 revision 1.2.1 or later |
| Management capability discovery | PLDM Type 0 | DMTF DSP0240 revision 1.1.0 or later |
| Base monitoring and control | PLDM Type 2 | DMTF DSP0248 revision 1.2.2 or later |
| Accelerator monitoring and control | PLDM Type 2 | DMTF DSP2061 revision 1.0 |
| Firmware management | PLDM Type 5 | DMTF DSP0267 revision 1.2.0 or later |
| File I/O | PLDM Type (Future/TBD) | DMTF DSP0242 |
| PLDM Set State | PLDM | DSP0249 revision 1.1.0 |

#### 2.1.1 Static discovery
The initial, static discovery of devices seeks to obtain primarily immutable information about the discrete accelerator device. This is done by reading the values from a dedicated FRU component (e.g. EEPROM) contained within the device.

#### 2.1.2 Transport Protocol
MCTP is the media independent protocol for communication among management controllers within the system. The corresponding section below describes the Bus owner relationship, the endpoint discovery, EID assignment, the bridges, and the EID pool assignments and corresponding flows.

#### 2.1.3 Attestation
Once static and MCTP endpoint discovery is complete, an important step is attestation of the device. SPDM (DSP0274) operates over MCTP as a standardized mechanism to authenticate hardware identity and measure firmware identity.

Using SPDM, the management controller serves as an SPDM requestor and discovers and negotiates the security capabilities to be used by the responder (discrete accelerator device). The management controller then authenticates the responder. Lastly, the management controller requests firmware measurements from the responder.

**Note:** SPDM attestation may happen repeatedly at the discretion of the management controller and is not limited to when the discrete accelerator device is initially discovered.

#### 2.1.4 Management capability discovery
Once the hardware discrete accelerator device has been authenticated and measured, continuous management begins by discovering the management capabilities of the device. For standards-based management, subsequent discovery of the device capabilities happens by way of PLDM Type 0 (base) discovery.

#### 2.1.5 Base monitoring and control
Once basic manageability capabilities are discovered dynamically from the device, continuous monitoring and control is performed by way of PLDM Type 2 (DSP0248).

#### 2.1.6 Firmware management
Standards-based device firmware management requires support for PLDM Type 5 (DSP0267). DSP0267 provides a standardized way to query the version of the device's firmware (inventory) and to push firmware to the device (update).

### 2.2 UBB Accelerator Devices Manageability
Universal baseboard (UBB) designs are multi-GPU/accelerator devices with high-speed intra-GPU interconnects, PCI-e switches and retimers, and complex topologies. In those HW devices the expectation is the UBB must present a Redfish interface and may additionally support MCTP for high-frequency telemetry (e.g. thermal loop data) and attestation.

The Redfish interface may be exposed by accelerator management controller (AMC) on/or adjacent to the UBB or, in the future, via a software library (virtual AMC) provided by the supplier (details to defined in a future version of this document).

#### Table 2 – DMTF Standard Protocols (UBB; normative mapping)
UBB implementations **SHALL** support the protocols indicated for each manageability objective.

| Manageability Objective | Technology | Description |
|---|---|---|
| Static discovery | IPMI FRU | Platform Management FRU Information Storage Definition (rev. 1.3) |
| Transport protocol | Redfish/MCTP | Redfish via NW and MCTP I2C/I3C/USB, etc... |
| Attestation | SPDM | Redfish or MCTP |
| Accelerator monitoring and control | Redfish/PLDM Type 2 | Comprehensive monitoring via Redfish and High frequency telemetry via I2C/I3C/USB, etc.. |
| Firmware management | Redfish | Via Redfish |

#### 2.2.1 Static discovery
UBB accelerator designs are expected to present a FRU EEPROM or virtual FRU equivalent (identical to what is required for discrete accelerator devices) and an AMC with a Redfish and optionally an MCTP interface w/ PLDM.

#### 2.2.2 Attestation
SPDM attestation is managed by the hyperscaler BMC but the low-level attestation is through the AMC Redfish interface or through standard SPDM/MCTP.

#### 2.2.3 Base monitoring and control
UBB accelerator designs are managed predominantly through Redfish implemented by the UBB AMC. High frequency and critical telemetry like power and thermal are managed via MCTP or PLDM over a sideband connection such as I2C/I3C/USB.

#### 2.2.4 Firmware management
UBB accelerator designs **SHALL** support firmware update through Redfish implemented by the AMC.

---

## 3. Discrete Accelerator Device Interfaces
This section lists the minimum set of protocols and OEM extension/format that any GPU/accelerator vendor needs to support on their discrete accelerator devices for hyperscalers to seamlessly manage these devices without any additional vendor/device specific work.

### 3.1 Access Frequency
The following table provides a high-level mapping of Data/Telemetry categories to protocols including the required frequency of access that must be reliably supported by the accelerator device.

#### Table 3 – Discrete Accelerator Management Protocols (normative expectations)
Implementations **SHALL** reliably support the access frequencies listed below for the corresponding data categories, unless a category is explicitly marked as future direction.

| Telemetry Data/Attribute (unordered) | Protocol | Use Case | Frequency |
|---|---|---|---|
| Monitoring (Sensors) | PLDM Type 2 (DSP0248) and MCTP OCP OEM | e.g. Thermal, power, counters… | 1s |
| Discovery | MCTP |  | On demand |
| Support Dump | Future direction: PLDM Type DSP0242 | Comprehensive triage | On demand |
| Debug Logs | Future direction: PLDM Type (DSP0242) |  | On demand |
| Updates | PLDM Type 5 (DSP0267) |  |  |
| Configuration/Settings | PLDM Type 2 (Effecters) (DSP0248) |  | On demand |

### 3.2 FRU Access and Data Model
Static discovery of the discrete accelerator device is accomplished by accessing an I2C/I3C FRU EEPROM or, if not present, through virtual FRU devices.

#### 3.2.1 FRU data source
A discrete accelerator device **SHALL** expose a FRU data source using one of the following mechanisms:
- An I2C or I3C FRU EEPROM, or
- A virtual FRU device.

#### 3.2.2 Virtual FRU requirements
If a virtual FRU device is implemented, it **SHALL**:
- Be available for access on AUX power, and
- Be accompanied by a recovery design in the event that the virtual FRU fails.

#### 3.2.3 FRU format, capacity, and areas
The FRU device **SHALL** return IPMI 1.3 FRU formatted data.
The FRU device **SHALL** support a minimum capacity of 512 bytes and **SHOULD** support 4 KB.

The FRU device **SHALL** support the following FRU areas:
- Internal Use Area: Optional
- Chassis Info Area: Optional
- Board Info Area: Mandatory
- Product Info Area: Mandatory (80 Bytes Minimum)
- Multi Record Area: Mandatory for any OEM extensions and the last area so that it extends.

#### 3.2.4 FRU access ownership
It is mandatory that a FRU data source is only accessed by a single agent (e.g. BMC on Host Platform). Implementations **SHALL NOT** allow multiple agents to access a FRU data source concurrently.

#### Table 4 – IPMI FRU Data Fields (normative population)
When the corresponding FRU areas are present, the implementation **SHALL** populate the following fields as indicated:

| FRU Area | Fields | Required w/ Valid Value |
|---|---|---|
| Common Header | Common Header | Y |
| Chassis Info Area | Chassis Type | N |
|  | Chassis Part Number | N |
|  | Chassis Serial Number | N |
| Board Info Area | Mfg Date/Time | N |
|  | Board Manufacturer | Y |
|  | Board Product Name | Y |
|  | Board Serial Number | Y |
|  | Board Part Number | Y |
| Product Info Area | Manufacturer Name | Y |
|  | Product Name | Y |
|  | Product Part/Model Number | Y |
|  | Product Version | N |
|  | Product Serial Number | Y |
| Multi-Record Area | Mandatory for any OEM extensions and the last area so that it extends. | Y, if applicable |

### 3.3 MCTP
This section describes the MCTP discovery and inventory flows and lists minimum required commands. This section also proposes and defines a high-level generic VDM command format for data that needs OEM extensions.

#### 3.3.1 Bus Owner and EIDs
Typically, the hyperscaler’s BMC is the topmost bus owner and all other devices are endpoint devices. Endpoint devices can themselves be a bus owner when they are connected to 2 or more MCTP buses (also referred to as a “bridged device”).

The “topmost bus owner” **SHALL** allocate all EIDs. When bridge devices are present, the topmost bus owner **SHALL** assign an EID pool to bridge devices, and bridge devices **SHALL** allocate EIDs to devices behind them from their assigned pool.

#### 3.3.2 MCTP Discovery
MCTP discovery/re-discovery **SHALL** occur on the following conditions:
- AC power ON
- DC power cycle
- BMC reset
- Device reset

Following an AC/DC power ON, discovery can occur any time during the system boot and typically complete before the host operating system has booted.
Endpoints **SHOULD** be ready in less than 60 seconds from power ON.
PCI-e enumeration **SHALL NOT** impact the I2C/I3C communication used for MCTP discovery.

**Figure 1 – MCTP Discovery Flow (non-normative):** The source includes a flow chart showing the sequence of MCTP control commands used during discovery.

#### 3.3.3 MCTP command details (normative)
Implementations **SHALL** implement MCTP control protocol command support consistent with Table 5.

**Key for Endpoint/Bridge columns:**
- Ma = mandatory to accept
- Mg = mandatory to generate
- Oa = optional to accept
- Og = optional to generate
- Ca = conditional to accept (see notes)
- Cg = conditional to generate (see notes)
- Na = not applicable to accept
- Ng = not applicable to generate

#### Table 5 – MCTP Commands (normative support)
| Cmd Code | Command Name | Endpoint | Bridge | Notes |
|---:|---|---|---|---|
| 0x01 | Set Endpoint ID | Ma | Ng | Required as indicated in source |
| 0x02 | Get Endpoint ID | Ma | Og |  |
| 0x03 | Get Endpoint UUID | Ca | Og | Mandatory if device connects to multiple buses |
| 0x04 | Get MCTP Version Support | Ma | Og | Bridges must use to verify compatible control protocol versions |
| 0x05 | Get Message Type Support | Ma | Og |  |
| 0x06 | Get Vendor Defined Message Support | Oa/Ca | Og | Mandatory to accept if device uses vendor defined messaging |
| 0x07 | Resolve Endpoint ID | Na | Og |  |
| 0x08 | Allocate Endpoint IDs | Na | Ng |  |
| 0x09 | Routing Information Update | Oa | Og |  |
| 0x0A | Get Routing Table Entries | Na | Og |  |
| 0x0B | Prepare for Endpoint Discovery | Ca | Ng | Mandatory on a per bus basis to support endpoint discovery |
| 0x0C | Endpoint Discovery | Ca | Ng | Mandatory on a per bus basis to support endpoint discovery |
| 0x0D | Discovery Notify | Na | Cg | Mandatory if binding supports hot joins (e.g. I3C and PCI VDM) |
| 0x0E | Get Network ID | Ca | Ca |  |
| 0x0F | Query Hop | Na | Og |  |
| 0x10 | Resolve UUID | Na | Og | Mandatory if device and/or connected devices connect to multiple buses |
| 0x11 | Query Rate Limit | Ca | Og | Mandatory if device can support more than 1 request at a time |
| 0x12 | Request TX rate limit | Oa | Og | Bridges must allow endpoints to change their rate limits |
| 0x13 | Update rate limit | Oa | Og |  |
| 0x14 | Query Supported Interfaces | Oa | Og |  |

### 3.4 SPDM
The OCP profile for accelerator attestation relies on the upcoming (post 1.0) version of the OCP Attestation of System Components v1.0 to define SPDM requirements for GPUs.

The support for sending ‘RawBitStream’ as part of Firmware Measurement has been added in the SPDM over Redfish feature under section 4.3.

Normative requirements:
- The platform management controller **SHALL** act as the SPDM *Requester*.
- The discrete accelerator device **SHALL** act as the SPDM *Responder*.
- The management controller **SHALL** authenticate the responder before relying on device-reported management data.
- SPDM attestation **MAY** be performed multiple times.

### 3.5 PLDM
This section describes how to model a GPU/accelerator PCI-e adapter using PLDM Monitoring and Control protocol. It provides general guidelines on how to model the adapters and their components so that the CSPs can implement a common and generic framework for monitoring, control, and component firmware updates across devices and vendors.

#### 3.5.1 Discrete GPU/Accelerator Modules
The source includes a generic representation of an accelerator adapter. Components include:
- MC: management controller implementing PLDM and communicating to Host BMC
- GPUx: one or more GPU/accelerator controllers
- InterLink: one or more controllers for high-speed connection between GPUs
- PCI-e switch/controller

**Figure 4 – PCI-e CEM Card (non-normative):** Illustrates component set and example sensors/effecters.

Normative modeling requirements:
- The discrete accelerator device **SHALL** be modeled as a PLDM Add-in Card entity.
- Components **SHALL** be represented with PLDM entity types consistent with DSP0248/DSP0249.

#### 3.5.2 PLDM Sensors & Effecters
The implementation **SHALL** support both numeric and state sensors.
At a minimum, sensors listed in Table 6 are required if present in hardware.

##### 3.5.2.1 Sensors
**Table 6 – PLDM Sensors and Types (minimum if present)**
| Sensors | Sensor Type | Add-in Card | MC | GPU | Link | PCI-e |
|---|---|---|---|---|---|---|
| Temp | Numeric | • |  | • | • |  |
| Power | Numeric | • |  | • |  |  |
| Total power | Numeric | • |  |  |  |  |
| VRs | Numeric | • |  | • |  |  |
| Composite Healthget | State | • |  |  |  |  |
| Health | State | • | • | • | • | • |

##### 3.5.2.2 Effecters
The implementation **SHALL** support both numeric and state effecters.
At a minimum, effecters listed in Table 7 are required if supported by hardware.

**Table 7 – PLDM Effecters (minimum if supported)**
| Effecters | Add-in Card | MC | GPU | Link | PCI-e |
|---|---|---|---|---|---|
| Power Cap | • |  | • |  |  |
| RTD |  | • |  |  |  |
| Reset |  | • |  |  |  |

Reset-to-Defaults requirement:
- The Reset to Defaults state effecter **SHALL** use OEM State Set 40000 and **SHALL** support enumerated value “1” indicating “Reset to Default”. No other values are required.

#### 3.5.3 Entity Association
Implementations **SHALL** model entity associations using Entity Association PDRs.

##### 3.5.3.1 Physical Entity Association
Physical association PDRs **SHALL** describe physical components and their associations. The Entity Association PDR uses:
- Container ID
- Contained ID
- Entity type (as defined in DSP0248)
- Entity instance

**Figure 5/6 (non-normative):** Component hierarchy and example PDR encodings.

##### 3.5.3.2 Logical Entity Association
Logical PDRs **SHALL** be used when associating physical components to a logical entity (e.g., “cooling domain”).
Logical grouping **SHOULD** enable management controllers to associate sensors to control algorithms without requiring prior knowledge.

**Figure 7 (non-normative):** Logical entity association example.

#### 3.5.4 Sensor & Effecter Entity Mapping
Implementations **SHALL** provide mapping that enables the Host BMC to map sensors/effecters to physical modules.

##### 3.5.4.1 Numeric Sensors
Numeric sensor PDRs **SHALL** be associated to the correct physical entity (e.g., GPU, InterLink) via entity association.

##### 3.5.4.2 State Sensors
State sensors (e.g., Health state sensor) **SHALL** be associated to an entity representing the intended scope (e.g., Add-in Card for overall health).

##### 3.5.4.3 Effecters
Power cap numeric effecter **SHALL** exist at the Add-in card level; it is optional at GPU/module level.
Reset and Reset-to-defaults effecters **SHALL** be associated to the management controller entity.

##### 3.5.4.4 Logical Sensor Mapping
When logical entities are used (e.g., cooling domain), sensors **SHALL** be mappable to those logical entities.

#### 3.5.5 PLDM Type IDs
**Table 8 – PLDM IDs**
| Component | Entity | Entity ID |
|---|---|---:|
| Management Controller | Management controller | 137 |
| Add-in card | Add-in card | 68 |
| GPU | Accelerator | 149 |
| InterLink | Inter-processor bus | 174 |
| PCI-e | PCI Express Bus | 166 |

### 3.6 MCTP OEM VDM (protocol-level normative)
This section lists a reference design for how a device may augment telemetry data over MCTP when existing PLDM models are insufficient.

#### 3.6.1 Management Protocol Structures
Device vendors may select a binding appropriate for their use case.
Two forms of OCP VDM bindings are defined:
- PCI vendor defined messages using the device vendor’s PCI Vendor ID.
- IANA enterprise ID vendor defined messages using OCP’s IANA enterprise ID (42623 = 0x0000A67F).

The supported OEM VDM binding **SHALL** be discoverable via the MCTP `Get Vendor Defined Message Support` command.
A device **SHALL** return either PCI Vendor ID-based message support or IANA Enterprise ID-based message support, and **MAY** indicate support for both.

If an implementer has vendor defined messages where byte 5 of the PCI Vendor ID binding is in the range 0x80-0xFF and does not conform to the Header Format of byte 5 defined in this specification, then:
- All OCP Header compliant messages, whether OEM or OCP defined, **SHALL** use OCP’s IANA Enterprise ID binding.
- A device **SHALL** indicate support for OCP Header Compliant Messages by returning 0x8000 in the last 2 bytes of `Get Vendor Defined Message Support`.

##### 3.6.1.1 Conventions
- Byte order: Per DSP0236, the standardized MCTP header is encoded in Big Endian.
- This specification requires that non-header byte ordering of multibyte numeric fields or multibyte bit fields is Little Endian.

##### 3.6.1.2 Data Type Enumeration
**Table 9 – Data Type Enumeration**
| # | Type | Explanation |
|---:|---|---|
| 0 | bool8 | Boolean in uint8 where 0x00 False, any non-zero True |
| 1 | enum8 | Sequential enumeration encoded in 8 bits |
| 2 | UINT8 | Unsigned 8-bit integer |
| 3 | INT8 | Signed 8-bit integer |
| 4 | UINT16 | Unsigned 16-bit integer |
| 5 | INT16 | Signed 16-bit integer |
| 6 | UINT32 | Unsigned 32-bit integer |
| 7 | INT32 | Signed 32-bit integer |
| 8 | UINT64 | Unsigned 64-bit integer |
| 9 | INT64 | Signed 64-bit integer |
| 10 | bitfield8 | 8-bit bitfield |
| 11 | bitfield32 | 32-bit bitfield |
| 12 | S24.8 | Signed fixed point (24 integer, 8 fraction) |
| 13 | String | ASCII with NULL termination |
| 14 | Character array | ASCII without NULL termination |
| 15 | DateTime | ASCII ISO 8601 yyyy-mmddThh:mm:ssZ (00:00:00Z if unknown) |

##### 3.6.1.3 OCP VDM binding using PCI Vendor ID
The PCI Vendor ID binding defines an 8-byte header including MCTP VDM framing and OCP designator/type/version fields.

##### 3.6.1.3.1 Field Descriptions (PCI Vendor ID binding)
Fields include: IC, Message type (0x7E), PCI vendor ID, RQ, D, Rsvd, Instance ID, OCP designator bit, OCP type, OCP version, Vendor Message Type, and Message payload.

RQ/D combinations **SHALL** be interpreted as:
- RQ=0, D=0: Response message
- RQ=0, D=1: Event acknowledgment
- RQ=1, D=0: Request message
- RQ=1, D=1: Event message

##### 3.6.1.4 OCP VDM binding using OCP’s IANA Enterprise ID
The IANA binding uses message type 0x7F and includes the 4-byte IANA Enterprise ID for OCP (42623).

##### 3.6.1.5 Message Payload Format
The source defines structures for:
- Discrete Request Message Format (OCP Version 1)
- Discrete Request Message Format (OCP Version 2)
- Discrete Response Message Format (success with data; aggregate success; failure with reason code)
- Bulk Telemetry Request/Response formats and record encodings

Implementations that claim support for these payload formats **SHALL** follow the field layouts and meanings as described by the corresponding tables in the source.

#### 3.6.2 Completion Codes
**Table 32 – Completion Codes (normative values)**
| Value | Name | Description |
|---:|---|---|
| 0x00 | SUCCESS | The command completed successfully. |
| 0x01 | ERROR | Generic error. Shall not be returned when a specific error is available. |
| 0x02 | ERR_INVALID_DATA | Request payload contained invalid data/illegal value. |
| 0x03 | ERR_INVALID_DATA_LENGTH | Requested data length does not match command specification. |
| 0x04 | ERR_NOT_READY | Responder is in transient state not ready to process request. |
| 0x05 | ERR_UNSUPPORTED_COMMAND_CODE | Command code not supported for this Vendor Message Type. |
| 0x06 | ERR_UNSUPPORTED_MSG_TYPE | Vendor message type not supported by responder. |
| 0x07–0x7C | RESERVED | Reserved for future use. |
| 0x7D | ACCEPTED | Response accepted; used for delayed response commands exceeding base timing. |
| 0x7E | ERR_BUSY | Endpoint busy and cannot accept new requests. |
| 0x7F | ERR_BUS_ACCESS | Responder encountered an internal bus error. |
| 0x80–0xFF | Command specific | Command-specific completion codes. |

#### 3.6.3 Reason Codes
**Table 33 – Reason Codes**
| Value | Name | Description |
|---:|---|---|
| 0x0000 | NONE | No additional reason code is available. |

#### 3.6.4 OEM Events
Events are asynchronous notifications that are issued by the managed device.

##### 3.6.4.1 Field semantics
Event payload fields include: ACKR flag, Version, Event ID, Event Class, Event State, Data Size, Data.

##### 3.6.4.2 OEM Event Classes
**Table 36 – OEM Event Classes**
| Event Class | Value | Description |
|---|---:|---|
| General | 0 | General purpose events. Event State always 0 for general in this version. |
| Assertion/Deassertion | 1 | Transient conditions that may generate assertion and de-assertion events. |
| Vendor defined | 128–255 | Reserved for vendor use. |

##### 3.6.4.3 OEM Event Acknowledgement
By default, events are unacknowledged datagram messages.
If acknowledged events are supported:
- Implementations **SHALL** restrict acknowledged events to only one event receiver.
- There **SHALL** be only a single event waiting for an ACK at a time; unacknowledged events may continue.
- If an ACK is not received within the acknowledgement timeout, the device **SHALL** retry delivery up to NRETRY times.
- If the event cannot be delivered after retries, it is considered expired and may be dropped.

#### 3.6.5 Timing Specification
**Table 39 – Timing Specification (normative minimums)**
| Timing specification | Symbol | Value | Description |
|---|---|---:|---|
| Number of request retries | NRETRY | 2 | Minimum number of times requester retries; in addition to original request. |
| Request-to-response time | TR2R | 100 ms | Maximum duration for responder to begin transmission of response. |
| Timeout waiting for response | TTIMEOUT | 300 ms | Typical duration requester waits for response accounting for delay. |
| Retry delay for ERR_NOT_READY | TRDY_DELAY | 250 ms | Minimum wait before retrying after ERR_NOT_READY. |
| Timeout waiting for long running request | TTIMEOUT_LONG | 1 second | Typical duration requester waits for long running request response. |

---

## 4. UBB Accelerator Device Interfaces
This section lists the minimum set of protocols and OEM extension/formats/schemas that any GPU/accelerator vendor needs to support on their AMC so that hyperscalers can seamlessly manage these devices without any additional vendor-specific work.

### 4.1 Redfish
This section describes the high-level overview of Redfish organization. This section also proposes various Redfish resource behaviors and OEM schema extensions. The security aspects of Redfish will be elaborated in future revisions of this specification.

#### 4.1.1 Redfish Tree
The source includes a tree diagram that provides a comprehensive overview of the AMC management capabilities and resource organization.

**Figure 12 – UBB Redfish Model (non-normative):** The resource label “UBB” represents the complete Accelerator subsystem/enclosure within the server and “OAM_X” represents each accelerator module assembly.

Normative requirements:
- UBB accelerator enclosure **SHALL** be modeled as a single logical subsystem.
- Each accelerator module **SHALL** be represented as a distinct Redfish resource.
- Relationships **SHALL** be modeled using Redfish links.

#### 4.1.2 Location Objects
The Location object referenced from many Redfish resources is important in enabling automation at scale.

**Table 40 – Location Object Example**
```json
"Location": {
  "PartLocationContext": "Backplane 3, Slot 4",
  "PartLocation": {
    "LocationType": "Port",
    "ServiceLabel": "Port 1"
  }
}
```

CSPs often rely on a tuple formed by `Location.PartLocationContext` and `Location.PartLocation` to bind entities to external logical management controls.

Normative requirements:
- Any Redfish resource that supports Location **SHALL** include `PartLocationContext`, `PartLocation.LocationType`, and `PartLocation.ServiceLabel`.

#### 4.1.3 Redfish Interoperability Profile (RIP)
The GPU management Redfish Interoperability Profile (RIP) follows the DMTF Redfish Interoperability Profile standard and provides specifics on the minimum set of Redfish resources and properties required by hyperscalers.

A supplier **SHALL** use the OCP UBB GPU RIP to confirm their AMC Redfish implementation supports the minimum set of Redfish required to ease integration with hyperscale environments.

RIP referenced by the source: *OCP GPU Management RIP 1.0.0* (github link in source).

#### 4.1.4 DMTF Redfish Message Registry
The DMTF Redfish Message Registry to support GPU management is incorporated into the Platforms message registry version 1.3.0 and beyond.

### 4.2 Out-of-Band Interfaces (MCTP, PLDM, and SPDM)
UBB designs are connected to the enclosing system via high-speed networking, where Redfish is the interface, and out-of-band connections including I2C/I3C, where MCTP, PLDM, and SPDM are the interface protocols.

The implementation of the out-of-band protocols is same as with discrete GPU/Accelerator devices described in above sections except that out-of-band communication is limited to specific purposes such as monitoring high-frequency telemetry for thermal and power related features.

### 4.3 SPDM Over Redfish
For UBB based GPU systems, leveraging the existing support of Redfish over high-speed medium such as USB for SPDM protocol provides better performance when compared to SPDM over MCTP.

To mitigate performance penalties, the AMC in UBB platform **SHALL** support transferring bitstreams (and Certs) via Redfish over vNIC over USB to the orchestrator/requestor.

Redfish ComponentIntegrity schema does not support RawBitStream, while SPDM GET_MEASUREMENT supports RawBitStream and Digest. To add RawBitStream:
- Add an OEM `OCPGPUMgmtWG` property inside `Measurements` and use `RawBitStream` as the value for `OCPMeasurementType`.
- Upon request for `RawBitStream` for any FW component within the PLDM bundle, responder **SHALL** send the Base64 encoded raw bit stream (binary image) in string object via `Measurement`.
- When `RawBitStream` is not requested, responder **SHALL** use standard attributes such as `MeasurementType` and `MeasurementHashAlgorithm` and provide digest data.

Action URIs (retained):
- `/redfish/v1/ComponentIntegrity/UBB/Actions/OCPGPUMgmtWG.ComponentIntegrity.v1_0_0.SPDMGetRawBitStream`
- `/redfish/v1/ComponentIntegrity/UBB/Actions/ComponentIntegrity.SPDMGetSignedMeasurements`

---

## 5. Conclusion
The management profiles for GPUs in this document provide guidance for how GPU devices can be designed and how CSPs can manage them. These profiles reduce the amount of work required for GPU suppliers to bring new products to market by converging CSP requirements around industry standards. Likewise, these profiles reduce the effort and time required for CSPs to bring new GPU designs to market by enabling a higher level of code and test re-use between different GPU parts and vendors.

---

## 6. Glossary
AMC – the Accelerator Management Controller presented by a UBB design. This is the Redfish controlled management controller present on an OCP OAI HGX UBB board.

BMC – Baseboard Management Controller. This is a management controller present within system designs that frequently interacts with the management of discrete accelerator devices and UBBs.

CSP – Cloud Service Provider

Hyperscaler – Cloud Service Provider

UBB – Universal baseboard; a type of accelerator delivered as a large board containing multiple GPUs and highspeed fabric interconnect.

---

## 7. References
- OCP GPU & Accelerator RAS Requirements v1.0
- OCP Attestation of System Components
- Platform Management FRU Information Storage Definition rev. 1.3 (IPMI)
- DMTF DSP0236 revision 1.3.1 or later (MCTP)
- DMTF DSP0274 revision 1.2.1 or later (SPDM)
- DMTF DSP0240 revision 1.1.0 or later (PLDM Type 0)
- DMTF DSP0248 revision 1.2.2 or later (PLDM Type 2)
- DMTF DSP0267 revision 1.2.0 or later (PLDM Type 5)
- DMTF DSP0266 revision 1.18.0 or later (Redfish)

---

## 8. License - Open Web Foundation (OWF) CLA
Contributions to this Specification are made under the terms and conditions set forth in Open Web Foundation Modified Contributor License Agreement (“OWF CLA 1.0”) (“Contribution License”) by:
AMD, Google, Meta, Microsoft, NVIDIA

Usage of this Specification is governed by the terms and conditions set forth in Open Web Foundation Modified Final Specification Agreement (“OWFa 1.0.2”) (“Specification License”).

You can review the applicable OWFa1.0 Specification License(s) referenced above by the contributors to this Specification on the OCP website at http://www.opencompute.org/participate/legal-documents/.

**Notes:**
1. The above license does not apply to the Appendix or Appendices. The information in the Appendix or Appendices is for reference only and non-normative in nature.

NOTWITHSTANDING THE FOREGOING LICENSES, THIS SPECIFICATION IS PROVIDED BY OCP "AS IS" AND OCP EXPRESSLY DISCLAIMS ANY WARRANTIES (EXPRESS, IMPLIED, OR OTHERWISE), INCLUDING IMPLIED WARRANTIES OF MERCHANTABILITY, NON-INFRINGEMENT, FITNESS FOR A PARTICULAR PURPOSE, OR TITLE, RELATED TO THE SPECIFICATION.

NOTICE IS HEREBY GIVEN, THAT OTHER RIGHTS NOT GRANTED AS SET FORTH ABOVE, INCLUDING WITHOUT LIMITATION, RIGHTS OF THIRD PARTIES WHO DID NOT EXECUTE THE ABOVE LICENSES, MAY BE IMPLICATED BY THE IMPLEMENTATION OF OR COMPLIANCE WITH THIS SPECIFICATION.

OCP IS NOT RESPONSIBLE FOR IDENTIFYING RIGHTS FOR WHICH A LICENSE MAY BE REQUIRED IN ORDER TO IMPLEMENT THIS SPECIFICATION.

THE ENTIRE RISK AS TO IMPLEMENTING OR OTHERWISE USING THE SPECIFICATION IS ASSUMED BY YOU.

IN NO EVENT WILL OCP BE LIABLE TO YOU FOR ANY MONETARY DAMAGES WITH RESPECT TO ANY CLAIMS RELATED TO, OR ARISING OUT OF YOUR USE OF THIS SPECIFICATION, INCLUDING BUT NOT LIMITED TO ANY LIABILITY FOR LOST PROFITS OR ANY CONSEQUENTIAL, INCIDENTAL, INDIRECT, SPECIAL OR PUNITIVE DAMAGES OF ANY CHARACTER FROM ANY CAUSES OF ACTION OF ANY KIND WITH RESPECT TO THIS SPECIFICATION, WHETHER BASED ON BREACH OF CONTRACT, TORT (INCLUDING NEGLIGENCE), OR OTHERWISE, AND EVEN IF OCP HAS BEEN ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

---

## 9. OCP Tenets
**Openness**
- This specification was developed via close and open collaboration between industry partners and competitors.
- All specifications and interfaces produced through this effort will be available to all OCP members.

**Efficiency**
- The goal of this specification is to make integration of GPUs into Hypercaler solutions seamless, reducing toil for both the supplier and the hyperscaler consumers.
- A companion to this effort is an OCP compliance tool that will enable automated validation of these interfaces, ensuring reduced toil and high-quality products.

**Impact**
- This document represents the first industry initiative to standardize GPU requirements between suppliers and hyperscale consumers.
- The advances in this document are expected to have significant impact on quality and time-to-market for GPU systems deployed by hyperscalers.
- These advances will also be applicable and beneficial to enterprise deployments of GPU systems.

**Scale**
- This specification applies to very large-scale GPU system deployments in hyperscaler data centers.

**Sustainability**
- The profiles defined in this document enable cross-generational commonality for key functionality of GPU parts, enabling logistics to support longer lifespan of GPU parts and a healthy secondary market for these parts.

---

## 10. About Open Compute Foundation
At the core of the Open Compute Project (OCP) is its Community of hyperscale data center operators, joined by telecom and colocation providers and enterprise IT users, working with vendors to develop open innovations that, when embedded in product are deployed from the cloud to the edge.

The OCP Foundation is responsible for fostering and serving the OCP Community to meet the market and shape the future.

Learn more at http://www.opencompute.org/.
