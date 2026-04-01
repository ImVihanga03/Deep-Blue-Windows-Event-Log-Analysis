# Deep-Blue-Windows-Event-Log-Analysis
Windows Event Log Analysis using DeepBlueCLI to investigate RDP brute-force attack and suspicious activity from Security.evtx and System.evtx
---
# Windows Event Log Analysis – Deep Blue Lab

**Lab**: Blue Team Labs Online – Deep Blue  
**Date Completed**: April 1, 2026  
**Focus**: Threat Hunting & Incident Response using Windows Event Logs

---

### Executive Summary
I performed a structured investigation of Windows Security.evtx and System.evtx logs to detect RDP brute-force attempts, malicious process execution, Meterpreter activity, and persistence mechanisms. I used a combination of **DeepBlueCLI** for automated detection and **Event Viewer** for manual deep dive.

### Tools Used
- DeepBlueCLI
- Windows Event Viewer
- Security.evtx & System.evtx logs

### Investigation Process & Findings

**Question 1: Which user account ran GoogleUpdate.exe?**  
Analyzed the Security log with DeepBlueCLI (`.\DeepBlue.ps1 ..\Security.evtx`). The tool flagged a long encoded command line involving `GoogleUpdate.exe`, which executed under the **NT AUTHORITY\SYSTEM** account.

![DeepBlueCLI Output - GoogleUpdate.exe Activity](q1-googleupdate-event.png)  
*DeepBlueCLI showing a long obfuscated command line related to GoogleUpdate.exe execution.*

**Question 2: At what time is there likely evidence of Meterpreter activity?**  
DeepBlueCLI identified suspicious Meterpreter-style activity (cmd.exe with named pipe) at **10:48:14 AM** on 10th April 2021.

![Meterpreter Activity Timestamp](q2-meterpreter-time.png)  
*DeepBlueCLI output highlighting the exact timestamp of suspicious Meterpreter-related command execution.*

**Question 3: What is the name of the suspicious service created?**  
DeepBlueCLI detected suspicious PSAttack-style commands and activity related to a named pipe (`rztbzn`) often used by Meterpreter.

![DeepBlueCLI Suspicious Commands](q3-suspicious-service.png)  
*DeepBlueCLI results displaying multiple suspicious command lines and Meterpreter pipe activity.*

**Question 4: Identify the malicious executable downloaded for Meterpreter reverse shell**  
Event ID 4688 showed the process creation of the malicious executable `ServiceUpdate.exe` by user "Mike Smith".

![ServiceUpdate.exe Process Creation](q4-serviceupdate-exe.png)  
*Event Viewer showing the creation of ServiceUpdate.exe – the payload used for the Meterpreter reverse shell.*

**Question 5: Command line for persistence account creation?**  
Captured the command `net user ServiceAct /add` via Event ID 4688.

![Account Creation Command](q5-account-creation.png)  
*Event Viewer (Event ID 4688) capturing the net user command used to create the persistence account "ServiceAct".*

**Question 6: Which two local groups was the account added to?**  
The account "ServiceAct" was added to **Administrators** and **Remote Desktop Users** groups.

![Group Addition - Administrators](q6-group-additions-1.png)  
*Event Viewer showing the command adding "ServiceAct" to the Administrators group.*

![Group Addition - Remote Desktop Users](q6-group-additions-2.png)  
*Event Viewer showing the command adding "ServiceAct" to the Remote Desktop Users group.*

### Key Takeaways
- Event ID 4688 is extremely valuable for detecting attacker tools and persistence mechanisms.
- DeepBlueCLI combined with Event Viewer provides fast and accurate Windows log analysis.
- This lab strengthened my ability to investigate real-world Windows-based attacks — a critical skill for SOC Analyst and Blue Team roles.

---

🛡️ Vihanga | Blue Team Journey

Connect with me: [![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/vihanga-senanayake-b7898126a/)
