| Title                | Suspicious netsh Dll persistence                                                                                                                                                 |
|:---------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Description          | Detects pesitence via netsh helper                                                                                                                                           |
| ATT&amp;CK Tactic    |  <ul><li>[TA0003: Persistence](https://attack.mitre.org/tactics/TA0003)</li></ul>  |
| ATT&amp;CK Technique | <ul><li>[T1060: Registry Run Keys / Startup Folder](https://attack.mitre.org/techniques/T1060)</li></ul>  |
| Data Needed          | <ul><li>[DN_0003_1_windows_sysmon_process_creation](../Data_Needed/DN_0003_1_windows_sysmon_process_creation.md)</li><li>[DN_0002_4688_windows_process_creation_with_commandline](../Data_Needed/DN_0002_4688_windows_process_creation_with_commandline.md)</li></ul>  |
| Enrichment           |  Data for this Detection Rule doesn't require any Enrichments.  |
| Trigger              | <ul><li>[T1060: Registry Run Keys / Startup Folder](../Triggers/T1060.md)</li></ul>  |
| Severity Level       | high |
| False Positives      | <ul><li>Unkown</li></ul>  |
| Development Status   | test |
| References           | <ul><li>[https://github.com/redcanaryco/atomic-red-team/blob/master/atomics/T1060/T1060.yaml](https://github.com/redcanaryco/atomic-red-team/blob/master/atomics/T1060/T1060.yaml)</li></ul>  |
| Author               | Victor Sergeev, oscd.community |


## Detection Rules

### Sigma rule

```
title: Suspicious netsh Dll persistence
id: 56321594-9087-49d9-bf10-524fe8479452
description: Detects pesitence via netsh helper
status: test
references:
    - https://github.com/redcanaryco/atomic-red-team/blob/master/atomics/T1060/T1060.yaml
tags:
    - attack.persistence
    - attack.t1060
date: 2019/10/25
modified: 2019/10/25
author: Victor Sergeev, oscd.community
logsource:
    category: process_creation
    product: windows   
detection:
    selection:
        Image|endswith: '\netsh.exe'
        CommandLine|contains|all:
            - 'add'
            - 'helper'
    condition: selection
fields:
    - CommandLine
    - ParentCommandLine
falsepositives:
    - Unkown
level: high

```





### es-qs
    
```
(Image.keyword:*\\\\netsh.exe AND CommandLine.keyword:*add* AND CommandLine.keyword:*helper*)
```


### xpack-watcher
    
```
curl -s -XPUT -H \'Content-Type: application/json\' --data-binary @- localhost:9200/_watcher/watch/Suspicious-netsh-Dll-persistence <<EOF\n{\n  "metadata": {\n    "title": "Suspicious netsh Dll persistence",\n    "description": "Detects pesitence via netsh helper",\n    "tags": [\n      "attack.persistence",\n      "attack.t1060"\n    ],\n    "query": "(Image.keyword:*\\\\\\\\netsh.exe AND CommandLine.keyword:*add* AND CommandLine.keyword:*helper*)"\n  },\n  "trigger": {\n    "schedule": {\n      "interval": "30m"\n    }\n  },\n  "input": {\n    "search": {\n      "request": {\n        "body": {\n          "size": 0,\n          "query": {\n            "bool": {\n              "must": [\n                {\n                  "query_string": {\n                    "query": "(Image.keyword:*\\\\\\\\netsh.exe AND CommandLine.keyword:*add* AND CommandLine.keyword:*helper*)",\n                    "analyze_wildcard": true\n                  }\n                }\n              ],\n              "filter": {\n                "range": {\n                  "timestamp": {\n                    "gte": "now-30m/m"\n                  }\n                }\n              }\n            }\n          }\n        },\n        "indices": []\n      }\n    }\n  },\n  "condition": {\n    "compare": {\n      "ctx.payload.hits.total": {\n        "not_eq": 0\n      }\n    }\n  },\n  "actions": {\n    "send_email": {\n      "email": {\n        "to": "root@localhost",\n        "subject": "Sigma Rule \'Suspicious netsh Dll persistence\'",\n        "body": "Hits:\\n{{#ctx.payload.hits.hits}}Hit on {{_source.@timestamp}}:\\n      CommandLine = {{_source.CommandLine}}\\nParentCommandLine = {{_source.ParentCommandLine}}================================================================================\\n{{/ctx.payload.hits.hits}}",\n        "attachments": {\n          "data.json": {\n            "data": {\n              "format": "json"\n            }\n          }\n        }\n      }\n    }\n  }\n}\nEOF\n
```


### graylog
    
```
(Image.keyword:*\\\\netsh.exe AND CommandLine.keyword:*add* AND CommandLine.keyword:*helper*)
```


### splunk
    
```
(Image="*\\\\netsh.exe" CommandLine="*add*" CommandLine="*helper*") | table CommandLine,ParentCommandLine
```


### logpoint
    
```
(event_id="1" Image="*\\\\netsh.exe" CommandLine="*add*" CommandLine="*helper*")
```


### grep
    
```
grep -P '^(?:.*(?=.*.*\\netsh\\.exe)(?=.*.*add.*)(?=.*.*helper.*))'
```



