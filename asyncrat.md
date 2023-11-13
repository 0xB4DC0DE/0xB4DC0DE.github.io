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

### Downloader
