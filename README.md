# Deep-Blue-Windows-Event-Log-Analysis
Windows Event Log Analysis using DeepBlueCLI to investigate RDP brute-force attack and suspicious activity from Security.evtx and System.evtx
# Windows Event Log Analysis – Deep Blue Lab

**Lab**: Blue Team Labs Online – Deep Blue  
**Analysis Type**: Windows Event Log Investigation & Threat Hunting

---

### Executive Summary
Performed a structured investigation of Windows Security and System event logs to identify suspicious activity from a simulated attack. The analysis focused on detecting brute-force attempts, malicious process execution, Meterpreter activity, and persistence mechanisms using Event Viewer and DeepBlueCLI.

### Tools Used
- Windows Event Viewer
- DeepBlueCLI
- Security.evtx and System.evtx log files

### Investigation Process & Findings

**Question 1: Which user account ran GoogleUpdate.exe?**  
I analyzed the Security.evtx log using **DeepBlueCLI** by running the following command:

 on powershell:
```.\DeepBlue.ps1 ..\Security.evtx```

![GoogleUpdate.exe Process Creation Event](q1-googleupdate-event.png) 

DeepBlueCLI output showing a long encoded command line involving GoogleUpdate.exe. This indicates suspicious activity where GoogleUpdate.exe was used (likely for execution or download).

**Question 2: At what time is there likely evidence of Meterpreter activity?**  
Identified suspicious Meterpreter-related activity at **10:48:14 AM** on 10th April 2021 through process creation events.

![Meterpreter Activity Timestamp](q2-meterpreter-time.png)

DeepBlueCLI analysis results highlighting the exact timestamp 10:48:14 AM on 10th April 2021, where suspicious Meterpreter-style activity (cmd.exe with named pipe) was detected in the Security log.

**Question 3: What is the name of the suspicious service created?**  
Using DeepBlueCLI on the System.evtx log, detected the suspicious service named **`UpdateOrchestrator`**.

![Suspicious Service Creation](q3-suspicious-service.png)

DeepBlueCLI output showing suspicious command-line activity from the Security.evtx log. It highlights multiple PSAttack-style commands, including the creation of a named pipe (rztbzn) commonly associated with Meterpreter.

**Question 4: Identify the malicious executable downloaded for Meterpreter reverse shell (between 10:30–10:50 AM on 10th April 2021).**  
The malicious executable identified was **`ServiceUpdate.exe`**.

![Malicious Executable - ServiceUpdate.exe](q4-serviceupdate-exe.png)

Event Viewer (Event ID 4688) showing the process creation of the malicious executable ServiceUpdate.exe downloaded by user "Mike Smith". This was the payload used for the Meterpreter reverse shell.

**Question 5: What was the command line used to create the persistence account (between 11:25–11:40 AM)?**  
The command line used was:  
`net user ServiceAct /add`

![Persistence Account Creation Command](q5-account-creation.png)

Event Viewer showing Event ID 4688 with the command net user ServiceAct /add, which was used to create the persistence user account "ServiceAct".

**Question 6: What two local groups was this new account added to?**  
The newly created account was added to:
- **Administrators**
- **Remote Desktop Users**

![Local Group Additions](q6-group-additions-1.png)

Event Viewer displaying another Event ID 4688 where the command net localgroup administrators ServiceAct /add was executed, adding the newly created account to the Administrators group.


![Local Group Additions](q6-group-additions-2.png)

Event Viewer showing Event ID 4688 (Process Creation). This event captures the command net localgroup "Remote Desktop Users" ServiceAct /add, adding the persistence account to the Remote Desktop Users group.

### Key Takeaways
- Event ID 4688 (Process Creation) is highly effective for detecting suspicious executables and attacker tools.
- Brute-force attacks are clearly visible through repeated Event ID 4625 (failed logon) entries.
- Attackers commonly use SYSTEM privileges and create persistence accounts with elevated rights.
- Combining manual filtering in Event Viewer with tools like DeepBlueCLI significantly improves investigation speed and accuracy.

This lab reinforced the importance of strong Windows event log analysis skills for effective SOC operations and incident response.

---

🛡️ Vihanga | Blue Team Journey

Connect with me: [LinkedIn](https://www.linkedin.com/in/yourprofile)
