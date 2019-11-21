| Title                | Suspicious GUP Usage                                                                                                                                                 |
|:---------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Description          | Detects execution of the Notepad++ updater in a suspicious directory, which is often used in DLL side-loading attacks                                                                                                                                           |
| ATT&amp;CK Tactic    |  <ul><li>[TA0005: Defense Evasion](https://attack.mitre.org/tactics/TA0005)</li></ul>  |
| ATT&amp;CK Technique | <ul><li>[T1073: DLL Side-Loading](https://attack.mitre.org/techniques/T1073)</li></ul>  |
| Data Needed          | <ul><li>[DN_0002_4688_windows_process_creation_with_commandline](../Data_Needed/DN_0002_4688_windows_process_creation_with_commandline.md)</li><li>[DN_0001_4688_windows_process_creation](../Data_Needed/DN_0001_4688_windows_process_creation.md)</li><li>[DN_0003_1_windows_sysmon_process_creation](../Data_Needed/DN_0003_1_windows_sysmon_process_creation.md)</li></ul>  |
| Enrichment           |  Data for this Detection Rule doesn't require any Enrichments.  |
| Trigger              | <ul><li>[T1073: DLL Side-Loading](../Triggers/T1073.md)</li></ul>  |
| Severity Level       | high |
| False Positives      | <ul><li>Execution of tools named GUP.exe and located in folders different than Notepad++\updater</li></ul>  |
| Development Status   | experimental |
| References           | <ul><li>[https://www.fireeye.com/blog/threat-research/2018/09/apt10-targeting-japanese-corporations-using-updated-ttps.html](https://www.fireeye.com/blog/threat-research/2018/09/apt10-targeting-japanese-corporations-using-updated-ttps.html)</li></ul>  |
| Author               | Florian Roth |


## Detection Rules

### Sigma rule

```
title: Suspicious GUP Usage
id: 0a4f6091-223b-41f6-8743-f322ec84930b
description: Detects execution of the Notepad++ updater in a suspicious directory, which is often used in DLL side-loading attacks
status: experimental
references:
    - https://www.fireeye.com/blog/threat-research/2018/09/apt10-targeting-japanese-corporations-using-updated-ttps.html
tags:
    - attack.defense_evasion
    - attack.t1073
author: Florian Roth
date: 2019/02/06
logsource:
    category: process_creation
    product: windows
detection:
    selection:
        Image: '*\GUP.exe'
    filter:
        Image:
            - 'C:\Users\\*\AppData\Local\Notepad++\updater\gup.exe'
            - 'C:\Users\\*\AppData\Roaming\Notepad++\updater\gup.exe'
            - 'C:\Program Files\Notepad++\updater\gup.exe'
            - 'C:\Program Files (x86)\Notepad++\updater\gup.exe'
    condition: selection and not filter
falsepositives:
    - Execution of tools named GUP.exe and located in folders different than Notepad++\updater
level: high

```





### splunk
    
```
(Image="*\\\\GUP.exe" NOT ((Image="C:\\\\Users\\\\*\\\\AppData\\\\Local\\\\Notepad++\\\\updater\\\\gup.exe" OR Image="C:\\\\Users\\\\*\\\\AppData\\\\Roaming\\\\Notepad++\\\\updater\\\\gup.exe" OR Image="C:\\\\Program Files\\\\Notepad++\\\\updater\\\\gup.exe" OR Image="C:\\\\Program Files (x86)\\\\Notepad++\\\\updater\\\\gup.exe")))
```



