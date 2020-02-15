| Title                | T1086 Remote PowerShell Session                                                                                                                                                 |
|:---------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Description          | Detects remote PowerShell sessions                                                                                                                                           |
| ATT&amp;CK Tactic    |   This Detection Rule wasn't mapped to ATT&amp;CK Tactic yet  |
| ATT&amp;CK Technique |  This Detection Rule wasn't mapped to ATT&amp;CK Technique yet  |
| Data Needed          | <ul><li>[DN_0037_4103_windows_powershell_executing_pipeline](../Data_Needed/DN_0037_4103_windows_powershell_executing_pipeline.md)</li></ul>  |
| Enrichment           |  Data for this Detection Rule doesn't require any Enrichments.  |
| Trigger              |  There is no documented Trigger for this Detection Rule yet  |
| Severity Level       | critical |
| False Positives      | <ul><li>Unknown</li></ul>  |
| Development Status   | experimental |
| References           | <ul><li>[https://github.com/Cyb3rWard0g/ThreatHunter-Playbook/tree/master/playbooks/windows/02_execution/T1086_powershell/powershell_remote_session.md](https://github.com/Cyb3rWard0g/ThreatHunter-Playbook/tree/master/playbooks/windows/02_execution/T1086_powershell/powershell_remote_session.md)</li></ul>  |
| Author               | Roberto Rodriguez @Cyb3rWard0g |


## Detection Rules

### Sigma rule

```
title: T1086 Remote PowerShell Session
id: 96b9f619-aa91-478f-bacb-c3e50f8df575
description: Detects remote PowerShell sessions
status: experimental
date: 2019/08/10
modified: 2019/11/10
author: Roberto Rodriguez @Cyb3rWard0g
references:
    - https://github.com/Cyb3rWard0g/ThreatHunter-Playbook/tree/master/playbooks/windows/02_execution/T1086_powershell/powershell_remote_session.md
logsource:
    product: windows
    service: powershell
detection:
    selection: 
        EventID:
            - 4103
            - 400
        HostName: 'ServerRemoteHost'
        HostApplication|contains: 'wsmprovhost.exe'
    condition: selection
falsepositives:
    - Unknown
level: critical
```





### es-qs
    
```
(EventID:("4103" OR "400") AND HostName:"ServerRemoteHost" AND HostApplication.keyword:*wsmprovhost.exe*)
```


### xpack-watcher
    
```
curl -s -XPUT -H \'Content-Type: application/json\' --data-binary @- localhost:9200/_watcher/watch/T1086-Remote-PowerShell-Session <<EOF\n{\n  "metadata": {\n    "title": "T1086 Remote PowerShell Session",\n    "description": "Detects remote PowerShell sessions",\n    "tags": "",\n    "query": "(EventID:(\\"4103\\" OR \\"400\\") AND HostName:\\"ServerRemoteHost\\" AND HostApplication.keyword:*wsmprovhost.exe*)"\n  },\n  "trigger": {\n    "schedule": {\n      "interval": "30m"\n    }\n  },\n  "input": {\n    "search": {\n      "request": {\n        "body": {\n          "size": 0,\n          "query": {\n            "bool": {\n              "must": [\n                {\n                  "query_string": {\n                    "query": "(EventID:(\\"4103\\" OR \\"400\\") AND HostName:\\"ServerRemoteHost\\" AND HostApplication.keyword:*wsmprovhost.exe*)",\n                    "analyze_wildcard": true\n                  }\n                }\n              ],\n              "filter": {\n                "range": {\n                  "timestamp": {\n                    "gte": "now-30m/m"\n                  }\n                }\n              }\n            }\n          }\n        },\n        "indices": []\n      }\n    }\n  },\n  "condition": {\n    "compare": {\n      "ctx.payload.hits.total": {\n        "not_eq": 0\n      }\n    }\n  },\n  "actions": {\n    "send_email": {\n      "email": {\n        "to": "root@localhost",\n        "subject": "Sigma Rule \'T1086 Remote PowerShell Session\'",\n        "body": "Hits:\\n{{#ctx.payload.hits.hits}}{{_source}}\\n================================================================================\\n{{/ctx.payload.hits.hits}}",\n        "attachments": {\n          "data.json": {\n            "data": {\n              "format": "json"\n            }\n          }\n        }\n      }\n    }\n  }\n}\nEOF\n
```


### graylog
    
```
(EventID:("4103" "400") AND HostName:"ServerRemoteHost" AND HostApplication.keyword:*wsmprovhost.exe*)
```


### splunk
    
```
((EventID="4103" OR EventID="400") HostName="ServerRemoteHost" HostApplication="*wsmprovhost.exe*")
```


### logpoint
    
```
(event_id IN ["4103", "400"] HostName="ServerRemoteHost" HostApplication="*wsmprovhost.exe*")
```


### grep
    
```
grep -P '^(?:.*(?=.*(?:.*4103|.*400))(?=.*ServerRemoteHost)(?=.*.*wsmprovhost\\.exe.*))'
```



