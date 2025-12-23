# ICS/Modbus - Claus for Concern

## Overview
In this room, we shifted gears from traditional IT security to **Operational Technology (OT)**. We investigated a compromised **SCADA** system controlling a fleet of delivery drones.

The system was hijacked by "King Malhare" via the **Modbus TCP** protocol, forcing drones to deliver chocolate eggs instead of Christmas presents. The challenge involved interacting with a **PLC (Programmable Logic Controller)** using Python to diagnose the hack and safely restore operations without triggering a logic trap.

---

## Key Concepts

### SCADA & PLCs
* **SCADA (Supervisory Control and Data Acquisition):** The "nervous system" of industrial operations. It monitors sensors and controls actuators.
* **PLC (Programmable Logic Controller):** Robust industrial computers that execute logic (e.g., "If sensor A is triggered, move Robot Arm B").

### Modbus TCP Protocol
* **Port:** `502` (Default).
* **Security:** Historically lacks authentication or encryption. Anyone who connects can read/write data.
* **Data Types:**
    * **Coils:** Boolean values (ON/OFF). Used for switches, alarms, flags.
    * **Holding Registers:** Numeric values (16-bit). Used for configuration, counters, setpoints.



---

## Investigation & Reconnaissance

### Step 1: Network Scanning
We started by scanning the target to identify running services.
**Command:**
```bash
nmap -p 22,80,502 10.82.155.127
```
**Result:** Port **502 (Modbus TCP)** was open, confirming this is an industrial control system.

### Step 2: Register Mapping (The Note)
We discovered a maintenance note defining the system's memory map. This was crucial for understanding the "Trap":
* **HR0 (Holding Register 0):** Package Type (`1 = Eggs`, `0 = Gifts`).
* **C11 (Coil 11):** Protection/Trap Flag.
* **Trap Logic:** *"Never change HR0 while C11=True! Will trigger countdown!"*

### Step 3: Modbus Interrogation (Python)
Using the `pymodbus` library, we connected to the PLC to read the current state.
**Findings:**
* **HR0 = 1:** Confirmed the drones are set to deliver Eggs.
* **HR4 = 666:** "Eggsploit" signature found (System Compromised).
* **C11 = True:** The Protection mechanism was active.
* **C10 & C13 = False:** Safety checks and logging were disabled.

---

## Remediation (Defusing the Trap)

To restore Christmas, we had to write values back to the PLC. However, simply changing the package type (HR0) would have triggered the self-destruct mechanism because C11 was active.

**The Safe Sequence:**
1.  **Disable Protection:** Write `False` to Coil 11. (Disarms the trap).
2.  **Restore Package Type:** Write `0` to Register 0. (Sets drones to deliver Gifts).
3.  **Enable Safety:** Write `True` to Coil 10 (Inventory Check) and Coil 13 (Logging).

## Answers Summary

| Question | Answer |
| :--- | :--- |
| What port is commonly used by Modbus TCP? | `502` |
| What is the flag retrieved after restoration? | `THM{eGgMas0V3r}` |

---

## Lessons Learned
* **Legacy Protocols are Risky**: Modbus was designed in 1979 for trusted networks. It has **no authentication** by default. If you can ping the PLC, you can control the factory.

* **Order of Operations Matters**: In ICS/SCADA, blindly patching or changing configurations can trigger physical failsafes (or traps). Reconnaissance is vital.
* **IT vs. OT**: Unlike IT systems where a crash means a reboot, in OT (Operational Technology), a crash or misconfiguration can cause physical damage (drones dumping cargo, machinery breaking).

---
> **Key Takeaway**: In Industrial Control Systems, security isn't just about data; it's about physical safety. Understanding the logic flow is more important than just running tools.
