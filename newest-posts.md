# Newest Posts

**Unraveling Quakbot's Demise: Insights from Operation Duck Hunt**

An international coalition, including the FBI, dismantled Quakbot's network, addressing over 700,000 infected hosts. Initial analysis of the Operation Duck Hunt sample revealed vague clues, but meticulous disassembly and debugger use unveiled the shellcode's memory allocation, exposing executable files stored within allocated memory blocks. Deeper investigation into the dumped DLLs and executables provided insights into the sample's timeline and actions, aiding in understanding the takedown's orchestration.

{% content-ref url="malware/analysis/operation-duck-hunt.md" %}
[operation-duck-hunt.md](malware/analysis/operation-duck-hunt.md)
{% endcontent-ref %}



**Solving ROP Emporium ret2win**

This post dives deep into mastering ROP exploits, particularly focusing on the 'ret2win' challenge. It guides readers through using Python and Radare2 to craft and execute a specialized code sequence, successfully retrieving hidden data from 'flag.txt'. A concise and practical guide to conquering ROP challenges.

{% content-ref url="binary-exploitation/rop-emporium/ret2win.md" %}
[ret2win.md](binary-exploitation/rop-emporium/ret2win.md)
{% endcontent-ref %}

**Exploring AsyncRAT: Dissecting a Multi-Stage Malware**

This detailed post offers an in-depth analysis of a sophisticated malware leveraging AsyncRAT, an open-source Remote Access Tool maintained by NYAN-x-CAT. It delves into the intricate infection chain, starting with a malicious WSF file and progressing through PowerShell scripts, shedding light on the malware's staging, persistence establishment, and its final payload injection into RegSvcs.exe. Through technical breakdowns of downloader, builder, injector, and RAT components, it unveils the obfuscation techniques, anti-analysis checks, and communication methods employed by the malware, providing a comprehensive understanding of its functionalities and threat implications.

{% content-ref url="malware/analysis/asyncrat.md" %}
[asyncrat.md](malware/analysis/asyncrat.md)
{% endcontent-ref %}
