# Microsoft-SOC-Lab---Endpoint-Security-Incident-Investigation

## Overview

This project demonstrates the detection and investigation of suspicious endpoint activity using Microsoft Defender for Endpoint, alongside the implementation of identity-based access controls using Conditional Access.

The objective was to simulate attacker behaviour, analyse generated alerts, and apply preventative controls to reduce risk. 

## Part 1 - Endpoint Detection & Simulation

### Device Onboarding

- Endpoint successfully onboarded into Microsoft Defender for Endpoint
- Telemetry enabled for monitoring and detection

<img width="658" height="198" alt="Screenshot 2026-04-06 at 18 31 19" src="https://github.com/user-attachments/assets/f97514f4-347e-4898-9aa7-5d4367704ee1" />

**Figure 1:** Shows the device successfully onboarded

### Attack Surface Reduction (ASR)

ASR rules were configured to reduce attack surface:

- Block Office applications creating child processes
- Block credential theft from LSASS
- Enable ransomware protection
- Block PsExec and WMI execution

<img width="665" height="139" alt="Screenshot 2026-04-06 at 18 36 22" src="https://github.com/user-attachments/assets/d2fb0f21-8d54-4b6f-a4bb-9237d0268b28" />

**Figure 2:** Shows the ASR policy created

<img width="267" height="357" alt="Screenshot 2026-04-06 at 18 36 54" src="https://github.com/user-attachments/assets/c35da88a-a2fa-4602-99c8-443bafa0cdd6" />

**Figure 3:** ASR rules configured


### Adversary Simulation

Adversary behaviour was simulated using Atomic Red Team

The following technique was executed:

- T1547.001 - Persistence via Startup Folder

This simulation was used to validate detection and generate realistic endpoint telemetry.

<img width="748" height="400" alt="Screenshot 2026-04-06 at 18 38 32" src="https://github.com/user-attachments/assets/cf537a70-100d-470a-8655-633742ae3f80" />

**Figure 4:** Shows Atomic Red Team Running Technique T1547.001

### Alert Generation

Execution of the simulated attack generated multiple alerts within Microsoft Defender for Endpoint including: 

- Suspicious PowerShell Command Line

<img width="750" height="161" alt="Screenshot 2026-04-06 at 18 39 52" src="https://github.com/user-attachments/assets/63cb9ee1-5c7d-44e8-bb9b-c422e6e59446" />

**Figure 5:** Shows the alerts generated as a result of atomic red team

<img width="645" height="361" alt="Screenshot 2026-04-06 at 18 40 32" src="https://github.com/user-attachments/assets/396e5721-d1dd-4d49-8918-6d3188dc4a4b" />

**Figure 6:** Shows the alert that corresponds to the atomic technique

## Part 2 - Incident Investigation

### Alert - Suspicious PowerShell Command Line

### Findings

- Time: Apr 6, 2026 ~ 13:04 PM
- Device: DESKTOP-HF5R3GF
- User: johar
- Process: powershell.exe
- Parent Process: explorer.exe
- Technique Observed: Persistence via Startup Folder (MITRE T1547.001)
- Command Observed:
  - PowerShell used Copy-Item to place a .bat file in:
    - C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup
- Script Source: Atomic Red Team framework (inferred from script content and file paths)
- Detection: Suspicious PowerShell execution flagged by Microsoft Defender for Endpoint

### Investigation Summary

A PowerShell process initiated by explorer.exe executed multiple scripts associated with the Atomic Red Team framework. The activity involved PowerShell executing scripts that resulted in a batch file being copied into the Windows Startup folder, establishing persistence on the system. This behaviour aligns with known attacker techniques used to maintain access following initial compromise.

This technique allows the script to execute automatically at user logon, which is commonly used by attackers to maintain persistence on compromised systems.

No additional malicious indicators such as external network connections or secondary payload execution were observed during the investigation. However, continued monitoring would be required to confirm that no further malicious activity occurs.

#### Who

- User: johar
- Execution context: Interactive user session

#### What 

- PowerShell executed multiple scripts
- Persistence established via Startup folder
- A .bat file was copied to a location that triggers execution at login

#### When 

- Apr 6, 2026 ~ 13:03-13:04

#### Where 

- Endpoint: DESKTOP-HF5R3GF
- File path:
  - C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup

#### Why

- The behaviour is consistent with Atomic Red Team adversary simulation. However, the technique observed closely mirrors real-world attacker persistence methods. Without confirmation of authorised testing, this activity would be treated as suspicious.

#### How

- PowerShell executed a Copy-Item command
- File placed into Startup directory
- Persistence established across logins

#### Recommendations 

- Verify whether the activity was part of an authorised security test.
- If unauthorised:
  - Remove persistence from the Startup folder
  - Investigate for additional persistence mechanisms
  - Review user activity and authentication logs
- Continue monitoring for:
  - Re-execution of PowerShell scripts
  - Recurrence of persistence behaviour
 
## Part 3 - Access Control (Conditional Access)

Following the investigation of endpoint activity, identity-based controls were implemented to reduce the risk of unauthorised access. 

### Conditional Access Policy 

A Conditional Access Policy was configured to: 

- Block access from unauthorised geographic locations
  - Example: Netherlands added as a blocked location
 
#### Outcome

- Sign-in attempts from restricted locations were successfully blocked
- Access was denied based on location-based conditions

#### Security Value

This control reduces the risk by:

- Preventing unauthorised access attempts
- Enforcing location-based restrictions
- Complementing endpoint detection with identity protection

<img width="639" height="366" alt="Screenshot 2026-04-06 at 18 55 36" src="https://github.com/user-attachments/assets/0eb8f61b-f20f-4a52-892c-a07ec26253fb" />

**Figure 7:** Shows the blocked location ticked of the netherlands

<img width="379" height="357" alt="Screenshot 2026-04-06 at 18 56 20" src="https://github.com/user-attachments/assets/cb3eb23a-f6fb-43fa-bdcd-27dc2206f3d0" />

**Figure 8:** Shows the conditional access policy stating that if the location matches one in the named location group, access is blocked

<img width="639" height="394" alt="Screenshot 2026-04-06 at 18 57 07" src="https://github.com/user-attachments/assets/a8750f50-cf5a-4177-87ab-f649bedda375" />

**Figure 9:** Shows the sign-in events blocking the login via conditional access

## Final Reflection

This project demonstrates how endpoint detection and identity-based controls work together to detect and prevent attacker activity.

The investigation highlighted how legitimate tools such as PowerShell can be used to replicate attacker behaviour, and how persistence techniques can be identified through telemetry. 

By combining detection, investigation and access control, the environment is better protected against both endpoint and identity-based threats. 












