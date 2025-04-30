# PowerShell-obfuscation-testing-

⚙️ Phase 1: Create a Basic PowerShell Script
Step 1: Open Notepad inside the VM
Paste this harmless PowerShell line:

powershell
Write-Host "This is a harmless test script"

![image](https://github.com/user-attachments/assets/e1257548-a178-4e6e-8008-bab5f37161a2)

✅ Next Step: Save the Script Properly
In Notepad, go to File > Save As

Name it:

Copy
Edit
test.ps1
Set “Save as type” to: All Files (*.*)

Choose Desktop as the save location

Click Save

![image](https://github.com/user-attachments/assets/0799a07f-01b0-48b9-8446-2323f88c421f)

🧪 Step 1: Run the Clean Script Normally
1. Open PowerShell as Administrator
Right-click Start menu → Windows PowerShell (Admin)

2. Navigate to your Desktop:
powershell

cd $env:USERPROFILE\Desktop

3. Run the script:

powershell
.\test.ps1

You should see:

This is a harmless test script 

But I saw this 

This is a common block because my system's execution policy is set to block all scripts for security

![image](https://github.com/user-attachments/assets/a6c33842-36b1-4493-8312-bb1251408a80)

to get past it like a proper analyst — without weakening the whole system. I did the following.

I ran this command in the same PowerShell (Admin) window:

powershell

powershell -ExecutionPolicy Bypass -File .\test.ps1

This runs your script just once without changing the system-wide setting.

![image](https://github.com/user-attachments/assets/793c9b8c-f813-449a-a446-0ff79be53883)

I just ran a PowerShell script with execution policy bypassed — like an attacker would. That's real-world technique #1 in malware delivery.

🧪 Next: Simulate the Obfuscated Payload (Base64)

Step 1: Generate the Base64 Payload

paste this into PowerShell:

powershell

$command = 'Write-Host "This is a harmless test script"'
$bytes = [System.Text.Encoding]::Unicode.GetBytes($command)
$encoded = [Convert]::ToBase64String($bytes)
$encoded

Copy the Base64 string it returns — should look like:

VwByAGkAdABlAC0ASABvAHMAdAAgACIAVABoAGkAcwAgAGkAcwAgAGEAIABoAGEAcgBtAGwAZQBzAHMAIAB0AGUAcwB0ACAAcwBjAHIAaQBwAHQAIgA

✅ Step 2: Run the Encoded Payload
Now in PowerShell, execute:

![image](https://github.com/user-attachments/assets/b004de09-5874-4865-850a-fdc2334e906f)

 I successfully ran an obfuscated PowerShell payload encoded in Base64 — exactly like real-world attackers do in phishing emails, LOLBins, or scheduled tasks.

 🔎 What You Can Do Next (Blue Team Mode):
🧠 Check the Logs
Open Event Viewer

Navigate to:


Applications and Services Logs > Microsoft > Windows > PowerShell > Operational
You might catch:

Base64 command lines

Script block logging (if enabled)

Security alerts if Defender or auditing is active

![image](https://github.com/user-attachments/assets/021d0fb9-e3fb-45c9-8fa3-029afc383990)

From that screenshot, I can confirm:

PowerShell logging is enabled

You’re seeing Event ID 40962, which means:

“PowerShell console is ready for user input”
(Basically the console opened.)

Right now you’re mostly logging basic console behavior.

To catch encoded or obfuscated scripts (like your -EncodedCommand), you want:

🔍 Advanced Logging to be Enable:
1. Script Block Logging
This logs full code after it’s decoded — powerful for catching:

Obfuscated payloads

Malicious one-liners

Anything run in memory

2. Module Logging
Tracks the commands and imported modules — like Invoke-WebRequest, etc.

✅ How to Enable Script Block Logging:
Open Group Policy Editor (Run gpedit.msc)

Go to:

Computer Configuration > Administrative Templates > Windows Components > Windows PowerShell
Double-click “Turn on PowerShell Script Block Logging”

Set to Enabled, then click OK

![2025-04-30 19_04_00-VirtualBoxVM](https://github.com/user-attachments/assets/0ed7486e-36bf-4324-a1ca-aa257b8513ca)

🧪 After That’s Enabled:
Re-run your encoded script

Go back to Event Viewer → PowerShell → Operational

Look for Event ID 4104: this logs decoded script blocks — even from base64

![image](https://github.com/user-attachments/assets/bde88a02-4604-4bca-94d3-c04e2f1c786e)

![image](https://github.com/user-attachments/assets/63411dd7-7306-4c9c-846e-51bef00cc115)

💡 Wanna See the Full Decoded Payload?
Click on one of those 4104 events, then go to the Details tab → XML View, and look for:

xml

<Data Name='ScriptBlockText'>Write-Host "This is a harmless test script"</Data>
That’s your decoded command — exactly what attackers try to hide.

![image](https://github.com/user-attachments/assets/5de16685-fffc-45bb-840b-bdf6524f4a5e)

✅ I’ve Just Simulated:

Stage | Status
PowerShell script authoring | ✅
Execution policy bypass | ✅
Obfuscated payload generation | ✅
Obfuscated execution simulation | ✅
Defensive detection via 4104 logs | ✅

I've basically walked through a red team attack simulation and a SOC analyst’s detection trail, all in one VM 💻⚔️
