# Newest Posts

#### Analyzing GuLoader: Extracting and Deobfuscating PowerShell Payloads

&#x20;This guide outlines how to dissect GuLoader malware by extracting and deobfuscating its PowerShell payloads. It details locating `wscript` calls to extract the initial script and determining substring values for deobfuscation with Python scripts

{% content-ref url="malware-writeups/analysis/guloader-script-deobfuscation.md" %}
[guloader-script-deobfuscation.md](malware-writeups/analysis/guloader-script-deobfuscation.md)
{% endcontent-ref %}

#### Creating a PowerShell Obfuscator

This method employs several techniques to obfuscate PowerShell payloads, by using XOR encryption, gzipped base64 encoding, and minimal variable usage. It ensures scripts evade detection in PowerShell ISE by encoding input characters with an XOR key, shuffling arrays, and constructing format strings referencing encrypted values.

{% content-ref url="malware-writeups/development/powershell-script-block-obfuscation.md" %}
[powershell-script-block-obfuscation.md](malware-writeups/development/powershell-script-block-obfuscation.md)
{% endcontent-ref %}



**Solving ROP Emporium split**

This challenge, akin to ret2win, demands passing "/bin/cat flag.txt" as an argument to a function rather than the function automatically reading the flag. Using pwndbg and cyclic, the buffer size is determined, followed by the location of specific gadgets using radare2 and ropper to manipulate the program flow and call the desired function. By combining located gadgets, the exploit is crafted to execute "/bin/cat flag.txt," revealing the flag upon script execution.

{% content-ref url="binary-exploitation/rop-emporium/split.md" %}
[split.md](binary-exploitation/rop-emporium/split.md)
{% endcontent-ref %}

**Unraveling Quakbot's Demise: Insights from Operation Duck Hunt**

An international coalition, including the FBI, dismantled Quakbot's network, addressing over 700,000 infected hosts. Initial analysis of the Operation Duck Hunt sample revealed vague clues, but meticulous disassembly and debugger use unveiled the shellcode's memory allocation, exposing executable files stored within allocated memory blocks. Deeper investigation into the dumped DLLs and executables provided insights into the sample's timeline and actions, aiding in understanding the takedown's orchestration.

_December 5th 2023_

{% content-ref url="malware-writeups/analysis/operation-duck-hunt.md" %}
[operation-duck-hunt.md](malware-writeups/analysis/operation-duck-hunt.md)
{% endcontent-ref %}



**Solving ROP Emporium ret2win**

This post dives deep into mastering ROP exploits, particularly focusing on the 'ret2win' challenge. It guides readers through using Python and Radare2 to craft and execute a specialized code sequence, successfully retrieving hidden data from 'flag.txt'. A concise and practical guide to conquering ROP challenges.

{% content-ref url="binary-exploitation/rop-emporium/ret2win.md" %}
[ret2win.md](binary-exploitation/rop-emporium/ret2win.md)
{% endcontent-ref %}

**Exploring AsyncRAT: Dissecting a Multi-Stage Malware**

This detailed post offers an in-depth analysis of a sophisticated malware leveraging AsyncRAT, an open-source Remote Access Tool maintained by NYAN-x-CAT. It delves into the intricate infection chain, starting with a malicious WSF file and progressing through PowerShell scripts, shedding light on the malware's staging, persistence establishment, and its final payload injection into RegSvcs.exe. Through technical breakdowns of downloader, builder, injector, and RAT components, it unveils the obfuscation techniques, anti-analysis checks, and communication methods employed by the malware, providing a comprehensive understanding of its functionalities and threat implications.

{% content-ref url="malware-writeups/analysis/asyncrat.md" %}
[asyncrat.md](malware-writeups/analysis/asyncrat.md)
{% endcontent-ref %}
