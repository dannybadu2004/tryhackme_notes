# Windows Forensics - The Registry

## Overview
In this room, we explored the **Windows Registry**, essentially the "brain" of the Windows Operating System. The registry stores all configuration settings for hardware, software, and users.

Our mission was to perform a forensic analysis on a compromised machine (`dispatch-srv01`) to identify a suspicious application related to drones that was installed and established persistence.

---

## Key Concepts

### What is the Registry?
* **The Database:** It acts as a hierarchical database storing low-level settings for the OS and applications.
* **Hives:** The registry isn't one big file; it's split into separate files called **Hives** located on the disk.

### Critical Hives & Locations
| Hive Name | Registry Path (Root Key) | Disk Location | What it stores |
| :--- | :--- | :--- | :--- |
| **SYSTEM** | `HKLM\SYSTEM` | `C:\Windows\System32\config\SYSTEM` | Boot config, Services, Hardware |
| **SOFTWARE** | `HKLM\SOFTWARE` | `C:\Windows\System32\config\SOFTWARE` | Installed programs, Settings |
| **NTUSER.DAT** | `HKCU` | `C:\Users\<user>\NTUSER.DAT` | User-specific settings, Recent files |

---

## Technical Investigation (Registry Explorer)

We used **Registry Explorer** to analyze the offline hives collected from the server.

### Step 1: Identifying the Installed Software (SOFTWARE Hive)
We loaded the **SOFTWARE** hive and navigated to the `Uninstall` key to see what programs were recently installed on the system.
* **Path Analyzed:** `uninstall`
* **Finding:** We identified a suspicious application named **DroneManager Updater** installed on the system.

### Step 2: Finding the Source Execution (NTUSER.DAT Hive)
We loaded the **NTUSER.DAT** hive (specifically for the user `dispatch.admin`) to find the exact location from where the installer was launched. We analyzed user execution artifacts (like UserAssist/RecentDocs).
* **Path Analyzed:** `\store` (and related execution keys)
* **Finding:** The installer was launched directly from the user's Downloads folder.
* **Full Path:** `C:\Users\dispatch.admin\Downloads\DroneManager_Setup.exe`

### Step 3: Persistence Mechanism (SOFTWARE Hive)
We returned to the **SOFTWARE** hive, this time checking the `Run` key to see if the malware configured itself to start automatically with Windows.
* **Path Analyzed:** `\run`
* **Finding:** A value was added to automatically launch a helper executable in the background upon reboot.
* **Value:** `"C:\Program Files\DroneManager\dronehelper.exe" --background`

---

## Answers Summary

| Question | Answer |
| :--- | :--- |
| What application was installed on the dispatch-srv01? | `DroneManager Updater` |
| What is the full path where the user launched the application? | `C:\Users\dispatch.admin\Downloads\DroneManager_Setup.exe` |
| Which value was added by the application to maintain persistence? | `"C:\Program Files\DroneManager\dronehelper.exe" --background` |

---

## Lessons Learned
* **Registry is King:** Almost every user action (opening a file, running a program) leaves a trace in the registry.
* **Persistence:** Attackers almost always modify the `Run` keys (in HKLM or HKCU) to ensure their malware stays active after a reboot.
* **UserAssist Analysis:** This key inside `NTUSER.DAT` is critical for proving *who* ran a file and *from where*, dismantling defenses like "I didn't do it".
* **Offline Analysis:** Always analyze registry hives offline (using tools like Registry Explorer) to avoid modifying the evidence on the live system.

---
> **Key Takeaway**: The Registry is the "DNA" of the Windows OS. It remembers installations, executions, and persistence mechanisms long after the attacker thinks they've hidden their tracks.
