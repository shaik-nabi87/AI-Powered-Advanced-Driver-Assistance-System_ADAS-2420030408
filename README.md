# SAAHAS-ADAS

### AI-Based Post-Crash Incident Intelligence and Automated Emergency Response for Advanced Driver Assistance Systems

SAAHAS-ADAS (**System for Automated Accident Harvesting, Analysis, and
Support in ADAS**) is a proposed end-to-end architecture that extends
Advanced Driver Assistance Systems (ADAS) beyond pre-crash collision
prevention into **post-crash evidence preservation, AI-based incident
analysis, automated documentation, and emergency response**.

The project is designed around a simple idea: a vehicle should not stop
being intelligent at the moment a collision occurs. Instead, existing
vehicle sensors, cameras, telemetry, and edge/cloud AI can be used to
preserve what happened, estimate crash severity, create a structured
incident record, and communicate actionable information to emergency
responders.

> **Project status:** The supplied project document presents SAAHAS-ADAS
> as an architectural framework and experimental evaluation plan. This
> README therefore describes the proposed system and its target behavior
> rather than claiming that all components are already implemented.

## Key Features

-   **Multi-signal crash confirmation**
    -   Airbag deployment status
    -   CAN-bus telemetry
    -   Sudden change in velocity
    -   High-G accelerometer measurements
    -   Severe-impact and optical-disruption signals
-   **Pre- and post-crash video preservation**
    -   Maintains a rolling 30-second synchronized video/telemetry
        buffer.
    -   After a confirmed crash, preserves the interval from
        approximately **T − 20 s to T + 10 s**.
-   **Vehicle telemetry harvesting**
    -   Longitudinal and lateral acceleration
    -   Yaw rate
    -   Vehicle speed
    -   Brake actuator state
    -   Steering trajectory
    -   GNSS position, heading, altitude, and timestamp
-   **Tamper-resistant evidence packaging**
    -   SHA-256 hashing
    -   Hardware Secure Element/HSM signing
    -   Timestamped evidence metadata
    -   Chain-of-custody support
-   **AI incident intelligence**
    -   Visual scene and damage analysis
    -   Vehicle deformation and rollover detection
    -   Pedestrian/barrier involvement analysis
    -   Fire/smoke signature detection
    -   Kinematic crash-severity estimation
    -   Chronological event-timeline reconstruction
-   **Automated incident documentation**
    -   AI-assisted FIR / accident dossier generation
    -   Structured incident information
    -   Confidence scoring
    -   Human validation by authorized investigators
-   **Context-rich emergency dispatch**
    -   Vehicle occupancy
    -   Estimated severity
    -   Rollover status
    -   Trapped-occupant probability
    -   Exact location and reverse-geocoded landmarks
-   **Offline-resilient operation**
    -   In-vehicle edge processing when connectivity is unavailable
    -   Secure local storage
    -   Queueing and synchronization after network recovery
    -   Optional satellite/V2X fallback paths

## Why SAAHAS-ADAS?

Conventional ADAS systems primarily focus on preventing or mitigating
collisions before impact. Existing automatic crash notification systems
can communicate basic information such as vehicle identity, time, and
location, but they generally do not provide the full visual, kinematic,
and semantic context needed for rapid emergency response and post-crash
investigation.

SAAHAS-ADAS bridges this gap by connecting:

**Pre-Crash Perception → Crash Detection → Evidence Preservation → AI
Reconstruction → Documentation → Emergency Dispatch**

This creates a unified **prevention--detection--forensics--response
ecosystem**.

## System Architecture

The architecture operates in two main regimes:

1.  **Active ADAS Operational Mode**
2.  **Post-Crash Forensic & Dispatch Mode**

The system is organized into four functional layers.

### Layer 1 --- Perception, Telemetry & Crash Confirmation

During normal driving, cameras, IMU sensors, and CAN-bus telemetry are
continuously processed. A synchronized ring buffer stores the recent
sensor history.

The proposed crash-confirmation logic combines independent signals to
reduce false alarms:

``` text
CrashConfirmed =
    AirbagDeploymentSignal
    OR (Delta_V >= Threshold_V AND G_Force_Peak >= Threshold_G)
    OR (SevereImpactSensor AND OpticalDisruptionScore > 0.85)
```

### Layer 2 --- Tamper-Resistant Evidence Harvesting

After a confirmed crash, the rolling buffer is frozen and the relevant
pre- and post-impact data is extracted.

The evidence package can contain:

-   Synchronized H.264/H.265 camera streams
-   Vehicle kinematics
-   Steering and braking data
-   GNSS and temporal metadata
-   Cryptographic hash
-   Hardware-backed digital signature

### Layer 3 --- AI Incident Intelligence & Forensic Reconstruction

The harvested evidence is analyzed by an AI intelligence engine running
on an in-vehicle edge accelerator or an edge/cloud environment.

The proposed analysis includes:

1.  **Visual event decomposition**
2.  **Kinematic severity estimation**
3.  **Chronological event timeline synthesis**

The resulting incident model can describe the sequence from initial
hazard appearance through driver reaction, ADAS warning, impact, and
final vehicle state.

### Layer 4 --- Automated Documentation & Emergency Dispatch

The structured incident intelligence is converted into two outputs:

**AI-Assisted FIR / Accident Dossier**

Contains information such as:

-   Time
-   GPS coordinates
-   Involved parties
-   Road conditions
-   Mechanical-failure checks
-   Visual summary
-   Confidence score

The document is explicitly intended as an **AI-generated forensic
draft** requiring formal validation by authorized investigators.

**Emergency Dispatch Packet**

A compact emergency payload is designed to provide responders with:

-   Vehicle occupancy
-   Estimated severity
-   Rollover status
-   Probability of trapped occupants
-   Exact location
-   Reverse-geocoded landmarks

## End-to-End Workflow

``` text
Vehicle Sensors & Cameras
          |
          v
+---------------------------+
| ADAS Perception Pipeline  |
| Camera + IMU + CAN + GNSS |
+---------------------------+
          |
          v
+---------------------------+
| 30s Rolling Buffer        |
+---------------------------+
          |
          v
+---------------------------+
| Multi-Signal Crash Engine |
+---------------------------+
          |
       Crash?
       /    \
     No      Yes
     |        |
     |        v
     |   Freeze T-20s to T+10s
     |        |
     |        v
     |   Evidence Sealing
     |   SHA-256 + HSM
     |        |
     |        v
     |   AI Incident Engine
     |        |
     |   +----+----+
     |   |         |
     |   v         v
     | FIR Draft  Severity/
     |            Timeline
     |   |         |
     |   +----+----+
     |        |
     |        v
     |  Emergency Packet
     |        |
     |   +----+----+
     |   |         |
     | Online    Offline
     |   |         |
     v   v         v
  Emergency     Secure Local
  Gateway       Storage/Queue
                    |
                    v
              Network Recovery
```

## Formal Processing Pipeline

The proposed processing sequence is:

``` text
Input:
  Video Feed
  CAN Telemetry
  IMU Vector
  Airbag Status
  GNSS

1. Initialize a 30-second circular buffer.
2. Continuously append synchronized sensor data.
3. Run normal ADAS perception and collision-risk calculations.
4. Issue warnings or autonomous mitigation when hazards are detected.
5. Evaluate the multi-signal crash trigger.
6. If a crash is confirmed:
   - Continue recording for 10 seconds.
   - Extract the preceding 20 seconds.
   - Hash and sign the evidence package.
7. Extract salient video frames.
8. Run vision-based scene and damage analysis.
9. Compute kinematic severity.
10. Construct the event timeline.
11. Generate the AI-assisted incident/FIR dossier.
12. Generate the emergency dispatch packet.
13. Transmit immediately when a network is available.
14. Otherwise store securely and synchronize after recovery.
```

## Online and Offline Operation

SAAHAS-ADAS is designed to remain functional even when network
connectivity is unavailable.

  -----------------------------------------------------------------------
  Mode              Processing        Dispatch          Storage
  ----------------- ----------------- ----------------- -----------------
  **Online          In-vehicle edge + HTTPS/REST,       Encrypted cloud
  (4G/5G)**         cloud LLM         WebSockets,       storage
                                      emergency gateway 

  **Offline**       In-vehicle edge,  Local             Encrypted local
                    quantized TinyLLM beacon/siren,     NVRAM/eMMC
                    / rule-based      optional          
                    engine            satellite or V2X  
  -----------------------------------------------------------------------

When connectivity returns, locally stored evidence and incident outputs
can be synchronized.

## Security, Privacy & Chain of Custody

Because the system processes potentially sensitive vehicle and occupant
information, the proposed architecture includes:

-   **Privacy masking** for uninvolved faces and license plates
-   **Role-Based Access Control (RBAC)**
-   Restricted access to unencrypted raw video
-   Actionable, minimized emergency-response metadata
-   **Immutable audit logging**
-   SHA-256 evidence hashing
-   Hardware-backed signatures

These mechanisms are intended to support evidence integrity while
limiting unnecessary exposure of personal information.

## Evaluation Framework

The project proposes evaluation across several functional dimensions.

  Evaluation Area                 Target
  ------------------------------- -----------------------------------------
  Pre-crash perception            ≥ 30 FPS, mAP \> 0.82
  Crash trigger validation        FAR \< 0.001%, trigger latency \< 50 ms
  Evidence harvesting & hashing   Sealing \< 400 ms
  AI forensic synthesis           Completeness \> 96%, latency \< 2.5 s
  Emergency dispatch              Delivery \< 1.2 s

The proposed evaluation references datasets and setups including
**nuScenes, KITTI, custom dashcam feeds, simulated CAN/IMU crash curves,
road-roughness data, high-speed storage, expert police FIR templates,
and 4G/5G cellular emulation**.

> These values are **target benchmarks from the project proposal**, not
> measured results.

## Comparison with Existing Paradigms

  -------------------------------------------------------------------------------
  Capability           Standard ADAS Automotive EDR EU eCall / ACN    SAAHAS-ADAS
  ------------------- -------------- -------------- -------------- --------------
  Pre-crash detection              ✓        Partial            ---              ✓

  Multi-sensor crash             ---              ✓        Partial              ✓
  trigger                                                          

  Video preservation             ---            ---            ---              ✓

  AI scene reasoning             ---            ---            ---              ✓

  Automated                      ---            ---            ---              ✓
  FIR/documentation                                                

  Rich emergency                 ---            ---        Partial              ✓
  dispatch                                                         
  -------------------------------------------------------------------------------

SAAHAS-ADAS is therefore positioned as an integration layer that
combines capabilities usually distributed across separate vehicle,
telematics, forensic, and emergency-response systems.

## Limitations

The project identifies several practical challenges:

1.  **OEM interoperability**\
    Proprietary CAN architectures and security gateways can make
    universal deployment difficult.

2.  **Severe optical occlusion**\
    Fire, submersion, or camera damage can prevent reliable visual
    analysis. The system should degrade to kinematic and telemetry-based
    analysis.

3.  **Legal recognition**\
    AI-generated FIR material remains advisory and requires formal
    verification and endorsement by authorized investigating officers.

## Future Work

The proposed future research directions include:

-   Vehicle-to-Everything (V2X) collective crash broadcasting
-   Multi-agent federated learning for privacy-preserving severity
    modeling
-   Multimodal radar-camera fusion for extreme-weather forensic
    reconstruction

## Project Goals

SAAHAS-ADAS aims to transform the vehicle from a system that primarily
**prevents crashes** into an intelligent platform that can also:

-   Detect and confirm serious collisions
-   Preserve critical evidence
-   Reconstruct what happened
-   Estimate incident severity
-   Generate structured forensic documentation
-   Deliver actionable emergency information
-   Continue operating during connectivity failures

## References

The project document references foundational work and standards
including:

-   YOLO / real-time object detection research
-   KITTI autonomous-driving benchmark
-   nuScenes multimodal autonomous-driving dataset
-   European Union eCall regulation
-   NHTSA Event Data Recorder requirements
-   Llama 2
-   Connected and automated vehicle data-management research
-   SAE J3016 driving-automation terminology

## Disclaimer

SAAHAS-ADAS is presented as an AI-driven architectural framework.
Automated FIR generation, severity estimation, emergency dispatch, and
forensic outputs should be treated as decision-support capabilities and
must remain subject to appropriate human, legal, safety, and regulatory
validation before real-world deployment.
