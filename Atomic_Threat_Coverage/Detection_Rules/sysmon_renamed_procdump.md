| Title                | Renamed ProcDump                                                                                                                                                 |
|:---------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Description          | Detects the execution of a renamed ProcDump executable often used by attackers or malware                                                                                                                                           |
| ATT&amp;CK Tactic    |  <ul><li>[TA0005: Defense Evasion](https://attack.mitre.org/tactics/TA0005)</li></ul>  |
| ATT&amp;CK Technique | <ul><li>[T1036: Masquerading](https://attack.mitre.org/techniques/T1036)</li></ul>  |
| Data Needed          | <ul><li>[DN_0003_1_windows_sysmon_process_creation](../Data_Needed/DN_0003_1_windows_sysmon_process_creation.md)</li><li>[DN_0011_7_windows_sysmon_image_loaded](../Data_Needed/DN_0011_7_windows_sysmon_image_loaded.md)</li></ul>  |
| Enrichment           |  Data for this Detection Rule doesn't require any Enrichments.  |
| Trigger              | <ul><li>[T1036: Masquerading](../Triggers/T1036.md)</li></ul>  |
| Severity Level       | critical |
| False Positives      | <ul><li>Procdump illegaly bundled with legitimate software</li><li>Weird admins who renamed binaries</li></ul>  |
| Development Status   | experimental |
| References           | <ul><li>[https://docs.microsoft.com/en-us/sysinternals/downloads/procdump](https://docs.microsoft.com/en-us/sysinternals/downloads/procdump)</li></ul>  |
| Author               | Florian Roth |


## Detection Rules

### Sigma rule

```
title: Renamed ProcDump
id: 4a0b2c7e-7cb2-495d-8b63-5f268e7bfd67
status: experimental
description: Detects the execution of a renamed ProcDump executable often used by attackers or malware
references:
    - https://docs.microsoft.com/en-us/sysinternals/downloads/procdump
author: Florian Roth
date: 2019/11/18
tags:
    - attack.defense_evasion
    - attack.t1036
logsource:
    product: windows
    service: sysmon
detection:
    selection:
        OriginalFileName: 'procdump'
    filter:
        Image: 
            - '*\procdump.exe'
            - '*\procdump64.exe'
    condition: selection and not filter
falsepositives:
    - Procdump illegaly bundled with legitimate software
    - Weird admins who renamed binaries
level: critical

```





### es-qs
    
```
(OriginalFileName:"procdump" AND (NOT (Image.keyword:(*\\\\procdump.exe OR *\\\\procdump64.exe))))
```


### xpack-watcher
    
```
curl -s -XPUT -H \'Content-Type: application/json\' --data-binary @- localhost:9200/_watcher/watch/Renamed-ProcDump <<EOF\n{\n  "metadata": {\n    "title": "Renamed ProcDump",\n    "description": "Detects the execution of a renamed ProcDump executable often used by attackers or malware",\n    "tags": [\n      "attack.defense_evasion",\n      "attack.t1036"\n    ],\n    "query": "(OriginalFileName:\\"procdump\\" AND (NOT (Image.keyword:(*\\\\\\\\procdump.exe OR *\\\\\\\\procdump64.exe))))"\n  },\n  "trigger": {\n    "schedule": {\n      "interval": "30m"\n    }\n  },\n  "input": {\n    "search": {\n      "request": {\n        "body": {\n          "size": 0,\n          "query": {\n            "bool": {\n              "must": [\n                {\n                  "query_string": {\n                    "query": "(OriginalFileName:\\"procdump\\" AND (NOT (Image.keyword:(*\\\\\\\\procdump.exe OR *\\\\\\\\procdump64.exe))))",\n                    "analyze_wildcard": true\n                  }\n                }\n              ],\n              "filter": {\n                "range": {\n                  "timestamp": {\n                    "gte": "now-30m/m"\n                  }\n                }\n              }\n            }\n          }\n        },\n        "indices": []\n      }\n    }\n  },\n  "condition": {\n    "compare": {\n      "ctx.payload.hits.total": {\n        "not_eq": 0\n      }\n    }\n  },\n  "actions": {\n    "send_email": {\n      "email": {\n        "to": "root@localhost",\n        "subject": "Sigma Rule \'Renamed ProcDump\'",\n        "body": "Hits:\\n{{#ctx.payload.hits.hits}}{{_source}}\\n================================================================================\\n{{/ctx.payload.hits.hits}}",\n        "attachments": {\n          "data.json": {\n            "data": {\n              "format": "json"\n            }\n          }\n        }\n      }\n    }\n  }\n}\nEOF\n
```


### graylog
    
```
(OriginalFileName:"procdump" AND (NOT (Image.keyword:(*\\\\procdump.exe *\\\\procdump64.exe))))
```


### splunk
    
```
(OriginalFileName="procdump" NOT ((Image="*\\\\procdump.exe" OR Image="*\\\\procdump64.exe")))
```


### logpoint
    
```
(OriginalFileName="procdump"  -(Image IN ["*\\\\procdump.exe", "*\\\\procdump64.exe"]))
```


### grep
    
```
grep -P '^(?:.*(?=.*procdump)(?=.*(?!.*(?:.*(?=.*(?:.*.*\\procdump\\.exe|.*.*\\procdump64\\.exe))))))'
```



