| Title                | Security Eventlog Cleared                                                                                                                                                 |
|:---------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Description          | Some threat groups tend to delete the local 'Security' Eventlog using certain utitlities                                                                                                                                           |
| ATT&amp;CK Tactic    |  <ul><li>[TA0005: Defense Evasion](https://attack.mitre.org/tactics/TA0005)</li></ul>  |
| ATT&amp;CK Technique | <ul><li>[T1070: Indicator Removal on Host](https://attack.mitre.org/techniques/T1070)</li></ul>  |
| Data Needed          | <ul><li>[DN_0038_1102_the_audit_log_was_cleared](../Data_Needed/DN_0038_1102_the_audit_log_was_cleared.md)</li><li>[DN_0050_1102_audit_log_was_cleared](../Data_Needed/DN_0050_1102_audit_log_was_cleared.md)</li></ul>  |
| Enrichment           |  Data for this Detection Rule doesn't require any Enrichments.  |
| Trigger              | <ul><li>[T1070: Indicator Removal on Host](../Triggers/T1070.md)</li></ul>  |
| Severity Level       | high |
| False Positives      | <ul><li>Rollout of log collection agents (the setup routine often includes a reset of the local Eventlog)</li><li>System provisioning (system reset before the golden image creation)</li></ul>  |
| Development Status   |  Development Status wasn't defined for this Detection Rule yet  |
| References           |  There are no documented References for this Detection Rule yet  |
| Author               | Florian Roth |
| Other Tags           | <ul><li>car.2016-04-002</li></ul> | 

## Detection Rules

### Sigma rule

```
title: Security Eventlog Cleared
id: f2f01843-e7b8-4f95-a35a-d23584476423
description: Some threat groups tend to delete the local 'Security' Eventlog using certain utitlities
tags:
    - attack.defense_evasion
    - attack.t1070
    - car.2016-04-002
author: Florian Roth
logsource:
    product: windows
    service: security
detection:
    selection:
        EventID:
            - 517
            - 1102
    condition: selection
falsepositives:
    - Rollout of log collection agents (the setup routine often includes a reset of the local Eventlog)
    - System provisioning (system reset before the golden image creation)
level: high

```





### splunk
    
```
(EventID="517" OR EventID="1102")
```



