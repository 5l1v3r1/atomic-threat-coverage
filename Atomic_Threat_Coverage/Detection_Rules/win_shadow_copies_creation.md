| Title                | Shadow copies creation using operating systems utilities                                                                                                                                                 |
|:---------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Description          | Shadow Copies creation using operating systems utilities, possible credential access                                                                                                                                           |
| ATT&amp;CK Tactic    |  <ul><li>[TA0006: Credential Access](https://attack.mitre.org/tactics/TA0006)</li></ul>  |
| ATT&amp;CK Technique | <ul><li>[T1003: Credential Dumping](https://attack.mitre.org/techniques/T1003)</li></ul>  |
| Data Needed          | <ul><li>[DN_0002_4688_windows_process_creation_with_commandline](../Data_Needed/DN_0002_4688_windows_process_creation_with_commandline.md)</li></ul>  |
| Enrichment           |  Data for this Detection Rule doesn't require any Enrichments.  |
| Trigger              | <ul><li>[T1003: Credential Dumping](../Triggers/T1003.md)</li></ul>  |
| Severity Level       | medium |
| False Positives      | <ul><li>Legitimate administrator working with shadow copies, access for backup purposes</li></ul>  |
| Development Status   | experimental |
| References           | <ul><li>[https://www.slideshare.net/heirhabarov/hunting-for-credentials-dumping-in-windows-environment](https://www.slideshare.net/heirhabarov/hunting-for-credentials-dumping-in-windows-environment)</li><li>[https://www.trustwave.com/en-us/resources/blogs/spiderlabs-blog/tutorial-for-ntds-goodness-vssadmin-wmis-ntdsdit-system/](https://www.trustwave.com/en-us/resources/blogs/spiderlabs-blog/tutorial-for-ntds-goodness-vssadmin-wmis-ntdsdit-system/)</li></ul>  |
| Author               | Teymur Kheirkhabarov, Daniil Yugoslavskiy, oscd.community |


## Detection Rules

### Sigma rule

```
title: Shadow copies creation using operating systems utilities
id: b17ea6f7-6e90-447e-a799-e6c0a493d6ce
description: Shadow Copies creation using operating systems utilities, possible credential access
author: Teymur Kheirkhabarov, Daniil Yugoslavskiy, oscd.community
date: 2019/10/22
references:
    - https://www.slideshare.net/heirhabarov/hunting-for-credentials-dumping-in-windows-environment
    - https://www.trustwave.com/en-us/resources/blogs/spiderlabs-blog/tutorial-for-ntds-goodness-vssadmin-wmis-ntdsdit-system/
tags:
    - attack.credential_access
    - attack.t1003
logsource:
    category: process_creation
    product: windows
detection:
    selection:
        NewProcessName|endswith:
            - '\powershell.exe'
            - '\wmic.exe'
            - '\vssadmin.exe'
        CommandLine|contains|all:
            - shadow
            - create
    condition: selection
falsepositives:
    - Legitimate administrator working with shadow copies, access for backup purposes
status: experimental
level: medium

```





### es-qs
    
```
(NewProcessName.keyword:(*\\\\powershell.exe OR *\\\\wmic.exe OR *\\\\vssadmin.exe) AND CommandLine.keyword:*shadow* AND CommandLine.keyword:*create*)
```


### xpack-watcher
    
```
curl -s -XPUT -H \'Content-Type: application/json\' --data-binary @- localhost:9200/_watcher/watch/Shadow-copies-creation-using-operating-systems-utilities <<EOF\n{\n  "metadata": {\n    "title": "Shadow copies creation using operating systems utilities",\n    "description": "Shadow Copies creation using operating systems utilities, possible credential access",\n    "tags": [\n      "attack.credential_access",\n      "attack.t1003"\n    ],\n    "query": "(NewProcessName.keyword:(*\\\\\\\\powershell.exe OR *\\\\\\\\wmic.exe OR *\\\\\\\\vssadmin.exe) AND CommandLine.keyword:*shadow* AND CommandLine.keyword:*create*)"\n  },\n  "trigger": {\n    "schedule": {\n      "interval": "30m"\n    }\n  },\n  "input": {\n    "search": {\n      "request": {\n        "body": {\n          "size": 0,\n          "query": {\n            "bool": {\n              "must": [\n                {\n                  "query_string": {\n                    "query": "(NewProcessName.keyword:(*\\\\\\\\powershell.exe OR *\\\\\\\\wmic.exe OR *\\\\\\\\vssadmin.exe) AND CommandLine.keyword:*shadow* AND CommandLine.keyword:*create*)",\n                    "analyze_wildcard": true\n                  }\n                }\n              ],\n              "filter": {\n                "range": {\n                  "timestamp": {\n                    "gte": "now-30m/m"\n                  }\n                }\n              }\n            }\n          }\n        },\n        "indices": []\n      }\n    }\n  },\n  "condition": {\n    "compare": {\n      "ctx.payload.hits.total": {\n        "not_eq": 0\n      }\n    }\n  },\n  "actions": {\n    "send_email": {\n      "email": {\n        "to": "root@localhost",\n        "subject": "Sigma Rule \'Shadow copies creation using operating systems utilities\'",\n        "body": "Hits:\\n{{#ctx.payload.hits.hits}}{{_source}}\\n================================================================================\\n{{/ctx.payload.hits.hits}}",\n        "attachments": {\n          "data.json": {\n            "data": {\n              "format": "json"\n            }\n          }\n        }\n      }\n    }\n  }\n}\nEOF\n
```


### graylog
    
```
(NewProcessName.keyword:(*\\\\powershell.exe *\\\\wmic.exe *\\\\vssadmin.exe) AND CommandLine.keyword:*shadow* AND CommandLine.keyword:*create*)
```


### splunk
    
```
((NewProcessName="*\\\\powershell.exe" OR NewProcessName="*\\\\wmic.exe" OR NewProcessName="*\\\\vssadmin.exe") CommandLine="*shadow*" CommandLine="*create*")
```


### logpoint
    
```
(event_id="1" NewProcessName IN ["*\\\\powershell.exe", "*\\\\wmic.exe", "*\\\\vssadmin.exe"] CommandLine="*shadow*" CommandLine="*create*")
```


### grep
    
```
grep -P '^(?:.*(?=.*(?:.*.*\\powershell\\.exe|.*.*\\wmic\\.exe|.*.*\\vssadmin\\.exe))(?=.*.*shadow.*)(?=.*.*create.*))'
```



