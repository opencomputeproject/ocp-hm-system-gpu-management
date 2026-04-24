# Redfish Scale Up Fabric Registry

## Overview

This document defines the Redfish Message Registry for Scale Up Fabric management in OCP GPU systems, based on the `AcceleratorFabric` registry proposal.

## Registry Definition

| Property         | Value               |
| :--------------- | :------------------ |
| Registry Name    | AcceleratorFabric   |
| Registry Prefix  | AcceleratorFabric   |
| Registry Version | 1.0.0               |
| Language         | en                  |
| OwningEntity     | OCP                 |

## Messages Summary

### Cable State

| MessageId | Severity | Message |
| :-------- | :------- | :------ |
| `AcceleratorFabric.1.0.0.CableInserted` | OK | A network cable was inserted into accelerator '\<Arg1\>' port '\<Arg2\>'. |
| `AcceleratorFabric.1.0.0.CableRemoved` | OK | A cable was removed from accelerator '\<Arg1\>' port '\<Arg2\>'. |

### Link Establishment

| MessageId | Severity | Message |
| :-------- | :------- | :------ |
| `AcceleratorFabric.1.0.0.ConnectionDropped` | OK | The connection is no longer active for accelerator '\<Arg1\>' port '\<Arg2\>' function '\<Arg3\>'. |
| `AcceleratorFabric.1.0.0.ConnectionEstablished` | OK | A connection was established for accelerator '\<Arg1\>' port '\<Arg2\>'. |
| `AcceleratorFabric.1.0.0.ConnectionSpeedLow` | Warning | The accelerator '\<Arg1\>' port '\<Arg2\>' is operating at a link speed of '\<Arg3\>' Gbps which is below the specified threshold of '\<Arg4\>' Gbps. |
| `AcceleratorFabric.1.0.0.DegradedConnectionEstablished` | Warning | A degraded connection was established for accelerator '\<Arg1\>' port '\<Arg2\>'. |
| `AcceleratorFabric.1.0.0.LinkTrainingStarted` | OK | Link training has started for accelerator '\<Arg1\>' port '\<Arg2\>'. |
| `AcceleratorFabric.1.0.0.LinkTrainingSucceeded` | OK | Link training succeeded for accelerator '\<Arg1\>' port '\<Arg2\>'. |
| `AcceleratorFabric.1.0.0.LinkTrainingFailed` | Critical | Link training failed for accelerator '\<Arg1\>' port '\<Arg2\>'. |
| `AcceleratorFabric.1.0.0.LinkRetrained` | OK | Link was retrained for accelerator '\<Arg1\>' port '\<Arg2\>'. |

### Link Health

| MessageId | Severity | Message |
| :-------- | :------- | :------ |
| `AcceleratorFabric.1.0.0.LinkFlapDetected` | Warning | The connection for accelerator '\<Arg1\>' port '\<Arg2\>' was established and dropped '\<Arg3\>' times in the last '\<Arg4\>' minutes. |
| `AcceleratorFabric.1.0.0.PortCongestionDetected` | Warning | Port congestion detected on accelerator '\<Arg1\>' port '\<Arg2\>'. |
| `AcceleratorFabric.1.0.0.PortCongestionCleared` | OK | Port congestion for accelerator '\<Arg1\>' port '\<Arg2\>' is cleared. |
| `AcceleratorFabric.1.0.0.PacketDropRateAboveCautionThreshold` | Warning | Packet Drop rate '\<Arg1\>' is above the '\<Arg2\>' per '\<Arg3\>' caution threshold for accelerator '\<Arg4\>' port '\<Arg5\>'. |
| `AcceleratorFabric.1.0.0.PacketDropRateAboveCriticalThreshold` | Critical | Packet Drop rate '\<Arg1\>' is above the '\<Arg2\>' per '\<Arg3\>' critical threshold for accelerator '\<Arg4\>' port '\<Arg5\>'. |
| `AcceleratorFabric.1.0.0.PacketDropRateCautionThresholdCleared` | OK | Packet Drop rate '\<Arg1\>' is below the '\<Arg2\>' per '\<Arg3\>' caution threshold for accelerator '\<Arg4\>' port '\<Arg5\>'. |
| `AcceleratorFabric.1.0.0.RetryRateAboveCautionThreshold` | Warning | Retry rate '\<Arg1\>' is above the '\<Arg2\>' per '\<Arg3\>' caution threshold for accelerator '\<Arg4\>' port '\<Arg5\>'. |
| `AcceleratorFabric.1.0.0.RetryRateAboveCriticalThreshold` | Critical | Retry rate '\<Arg1\>' is above the '\<Arg2\>' per '\<Arg3\>' critical threshold for accelerator '\<Arg4\>' port '\<Arg5\>'. |
| `AcceleratorFabric.1.0.0.RetryRateCautionThresholdCleared` | OK | Retry rate '\<Arg1\>' is below the '\<Arg2\>' per '\<Arg3\>' caution threshold for accelerator '\<Arg4\>' port '\<Arg5\>'. |
| `AcceleratorFabric.1.0.0.ErrorCorrectionRateAboveCautionThreshold` | Warning | Error correction rate '\<Arg1\>' is above the '\<Arg2\>' per '\<Arg3\>' caution threshold for accelerator '\<Arg4\>' port '\<Arg5\>'. |
| `AcceleratorFabric.1.0.0.ErrorCorrectionRateAboveCriticalThreshold` | Critical | Error correction rate '\<Arg1\>' is above the '\<Arg2\>' per '\<Arg3\>' critical threshold for accelerator '\<Arg4\>' port '\<Arg5\>'. |
| `AcceleratorFabric.1.0.0.ErrorCorrectionRateCautionThresholdCleared` | OK | Error correction rate '\<Arg1\>' is below the '\<Arg2\>' per '\<Arg3\>' caution threshold for accelerator '\<Arg4\>' port '\<Arg5\>'. |

### Port

| MessageId | Severity | Message |
| :-------- | :------- | :------ |
| `AcceleratorFabric.1.0.0.PortConfigChanged` | OK | The port configuration has changed for accelerator '\<Arg1\>' port '\<Arg2\>'. |

## Open Issues

| MessageId | Notes |
| :-------- | :---- |
| `CableInserted` / `CableRemoved` | Based on NetworkDevice Message. Use this or `Fabric.CableInserted` / `Fabric.CableRemoved`? |
| `ConnectionDropped` | Based on NetworkDevice Message. Use this or `Fabric.ConnectionRemoved`? Move 'Connection' to 'Link'? "Connection" == Full E2E Data Path Established? |
| `ConnectionEstablished` | Based on NetworkDevice Message. Use this or `Fabric.ConnectionCreated`? |
| `ConnectionSpeedLow` | Based on NetworkDevice Message. Include function like NetworkDevice? Where is threshold set? Event triggered after initial link establishment? |
| `DegradedConnectionEstablished` | Include function like NetworkDevice? Event expected when connecting below max supported by both endpoints? |
| `LinkFlapDetected` | Include function like NetworkDevice? |
| `PortCongestionDetected` / `PortCongestionCleared` | Should we rely on packet drops instead? Include a threshold value? Should this be in the Fabric MessageRegistry? |
| `PacketDropRate*` | Where is the threshold set? Include a time scale? Should this be `UpperCautionThreshold` to align with other similar messages? |
| `PortConfigChanged` | Use `ResourceEvent.ResourceChanged` for Port instead? Should this be in the Fabric MessageRegistry? |

## References

- [Redfish Message Registry Specification](https://www.dmtf.org/dsp/DSP0266)
- [Redfish Data Model Specification](https://www.dmtf.org/dsp/DSP0268)
- [OCP GPU & Accelerator Management Interfaces v1.1](https://www.opencompute.org/documents/ocp-gpu-accelerator-management-interfaces-v1-1-pdf)
