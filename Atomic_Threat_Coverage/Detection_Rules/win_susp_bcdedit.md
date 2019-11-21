| Title                | Possible Ransomware or unauthorized MBR modifications                                                                                                                                                 |
|:---------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Description          | Detects, possibly, malicious unauthorized usage of bcdedit.exe                                                                                                                                           |
| ATT&amp;CK Tactic    |  <ul><li>[TA0005: Defense Evasion](https://attack.mitre.org/tactics/TA0005)</li><li>[TA0003: Persistence](https://attack.mitre.org/tactics/TA0003)</li></ul>  |
| ATT&amp;CK Technique | <ul><li>[T1070: Indicator Removal on Host](https://attack.mitre.org/techniques/T1070)</li><li>[T1067: Bootkit](https://attack.mitre.org/techniques/T1067)</li></ul>  |
| Data Needed          | <ul><li>[DN_0002_4688_windows_process_creation_with_commandline](../Data_Needed/DN_0002_4688_windows_process_creation_with_commandline.md)</li></ul>  |
| Enrichment           |  Data for this Detection Rule doesn't require any Enrichments.  |
| Trigger              | <ul><li>[T1070: Indicator Removal on Host](../Triggers/T1070.md)</li><li>[T1067: Bootkit](../Triggers/T1067.md)</li></ul>  |
| Severity Level       | medium |
| False Positives      |  There are no documented False Positives for this Detection Rule yet  |
| Development Status   | experimental |
| References           | <ul><li>[https://docs.microsoft.com/en-us/windows-hardware/drivers/devtest/bcdedit--set](https://docs.microsoft.com/en-us/windows-hardware/drivers/devtest/bcdedit--set)</li></ul>  |
| Author               | @neu5ron |


## Detection Rules

### Sigma rule

```
title: Possible Ransomware or unauthorized MBR modifications
id: c9fbe8e9-119d-40a6-9b59-dd58a5d84429
status: experimental
description: Detects, possibly, malicious unauthorized usage of bcdedit.exe
references:
    - https://docs.microsoft.com/en-us/windows-hardware/drivers/devtest/bcdedit--set
author: '@neu5ron'
date: 2019/02/07
tags:
    - attack.defense_evasion
    - attack.t1070
    - attack.persistence
    - attack.t1067
logsource:
    category: process_creation
    product: windows
detection:
    selection:
        NewProcessName: '*\bcdedit.exe'
        ProcessCommandLine:
            - '*delete*'
            - '*deletevalue*'
            - '*import*'
    condition: selection
level: medium

```





### splunk
    
```
(NewProcessName="*\\\\bcdedit.exe" (ProcessCommandLine="*delete*" OR ProcessCommandLine="*deletevalue*" OR ProcessCommandLine="*import*"))
```



