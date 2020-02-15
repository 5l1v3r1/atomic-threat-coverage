| Title                | T1055 CreateRemoteThread API and LoadLibrary                                                                                                                                                 |
|:---------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Description          | Detects potential use of CreateRemoteThread api and LoadLibrary function to inject DLL into a process                                                                                                                                           |
| ATT&amp;CK Tactic    |   This Detection Rule wasn't mapped to ATT&amp;CK Tactic yet  |
| ATT&amp;CK Technique |  This Detection Rule wasn't mapped to ATT&amp;CK Technique yet  |
| Data Needed          | <ul><li>[DN_0012_8_windows_sysmon_CreateRemoteThread](../Data_Needed/DN_0012_8_windows_sysmon_CreateRemoteThread.md)</li></ul>  |
| Enrichment           |  Data for this Detection Rule doesn't require any Enrichments.  |
| Trigger              |  There is no documented Trigger for this Detection Rule yet  |
| Severity Level       | critical |
| False Positives      | <ul><li>Unknown</li></ul>  |
| Development Status   | experimental |
| References           | <ul><li>[https://github.com/Cyb3rWard0g/ThreatHunter-Playbook/tree/master/playbooks/windows/05_defense_evasion/T1055_process_injection/dll_injection_createremotethread_loadlibrary.md](https://github.com/Cyb3rWard0g/ThreatHunter-Playbook/tree/master/playbooks/windows/05_defense_evasion/T1055_process_injection/dll_injection_createremotethread_loadlibrary.md)</li></ul>  |
| Author               | Roberto Rodriguez @Cyb3rWard0g |


## Detection Rules

### Sigma rule

```
title: T1055 CreateRemoteThread API and LoadLibrary
id: 052ec6f6-1adc-41e6-907a-f1c813478bee
description: Detects potential use of CreateRemoteThread api and LoadLibrary function to inject DLL into a process
status: experimental
date: 2019/08/11
modified: 2019/11/10
author: Roberto Rodriguez @Cyb3rWard0g
references:
    - https://github.com/Cyb3rWard0g/ThreatHunter-Playbook/tree/master/playbooks/windows/05_defense_evasion/T1055_process_injection/dll_injection_createremotethread_loadlibrary.md
logsource:
    product: windows
    service: sysmon
detection:
    selection: 
        EventID: 8
        StartModule|endswith: '\kernel32.dll'
        StartFunction: 'LoadLibraryA'
    condition: selection
falsepositives:
    - Unknown
level: critical
```





### es-qs
    
```
(EventID:"8" AND StartModule.keyword:*\\\\kernel32.dll AND StartFunction:"LoadLibraryA")
```


### xpack-watcher
    
```
curl -s -XPUT -H \'Content-Type: application/json\' --data-binary @- localhost:9200/_watcher/watch/T1055-CreateRemoteThread-API-and-LoadLibrary <<EOF\n{\n  "metadata": {\n    "title": "T1055 CreateRemoteThread API and LoadLibrary",\n    "description": "Detects potential use of CreateRemoteThread api and LoadLibrary function to inject DLL into a process",\n    "tags": "",\n    "query": "(EventID:\\"8\\" AND StartModule.keyword:*\\\\\\\\kernel32.dll AND StartFunction:\\"LoadLibraryA\\")"\n  },\n  "trigger": {\n    "schedule": {\n      "interval": "30m"\n    }\n  },\n  "input": {\n    "search": {\n      "request": {\n        "body": {\n          "size": 0,\n          "query": {\n            "bool": {\n              "must": [\n                {\n                  "query_string": {\n                    "query": "(EventID:\\"8\\" AND StartModule.keyword:*\\\\\\\\kernel32.dll AND StartFunction:\\"LoadLibraryA\\")",\n                    "analyze_wildcard": true\n                  }\n                }\n              ],\n              "filter": {\n                "range": {\n                  "timestamp": {\n                    "gte": "now-30m/m"\n                  }\n                }\n              }\n            }\n          }\n        },\n        "indices": []\n      }\n    }\n  },\n  "condition": {\n    "compare": {\n      "ctx.payload.hits.total": {\n        "not_eq": 0\n      }\n    }\n  },\n  "actions": {\n    "send_email": {\n      "email": {\n        "to": "root@localhost",\n        "subject": "Sigma Rule \'T1055 CreateRemoteThread API and LoadLibrary\'",\n        "body": "Hits:\\n{{#ctx.payload.hits.hits}}{{_source}}\\n================================================================================\\n{{/ctx.payload.hits.hits}}",\n        "attachments": {\n          "data.json": {\n            "data": {\n              "format": "json"\n            }\n          }\n        }\n      }\n    }\n  }\n}\nEOF\n
```


### graylog
    
```
(EventID:"8" AND StartModule.keyword:*\\\\kernel32.dll AND StartFunction:"LoadLibraryA")
```


### splunk
    
```
(EventID="8" StartModule="*\\\\kernel32.dll" StartFunction="LoadLibraryA")
```


### logpoint
    
```
(event_id="8" StartModule="*\\\\kernel32.dll" StartFunction="LoadLibraryA")
```


### grep
    
```
grep -P '^(?:.*(?=.*8)(?=.*.*\\kernel32\\.dll)(?=.*LoadLibraryA))'
```



