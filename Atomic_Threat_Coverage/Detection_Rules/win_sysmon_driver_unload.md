| Title                | Sysmon driver unload                                                                                                                                                 |
|:---------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Description          | Detect possible Sysmon driver unload                                                                                                                                           |
| ATT&amp;CK Tactic    |   This Detection Rule wasn't mapped to ATT&amp;CK Tactic yet  |
| ATT&amp;CK Technique |  This Detection Rule wasn't mapped to ATT&amp;CK Technique yet  |
| Data Needed          | <ul><li>[DN_0003_1_windows_sysmon_process_creation](../Data_Needed/DN_0003_1_windows_sysmon_process_creation.md)</li><li>[DN_0002_4688_windows_process_creation_with_commandline](../Data_Needed/DN_0002_4688_windows_process_creation_with_commandline.md)</li></ul>  |
| Enrichment           |  Data for this Detection Rule doesn't require any Enrichments.  |
| Trigger              |  There is no documented Trigger for this Detection Rule yet  |
| Severity Level       | high |
| False Positives      | <ul><li>U</li><li>n</li><li>k</li><li>n</li><li>o</li><li>w</li><li>n</li></ul>  |
| Development Status   | experimental |
| References           | <ul><li>[https://www.darkoperator.com/blog/2018/10/5/operating-offensively-against-sysmon](https://www.darkoperator.com/blog/2018/10/5/operating-offensively-against-sysmon)</li></ul>  |
| Author               | Kirill Kiryanov, oscd.community |


## Detection Rules

### Sigma rule

```
title: Sysmon driver unload
id: 4d7cda18-1b12-4e52-b45c-d28653210df8
status: experimental
author: Kirill Kiryanov, oscd.community
description: Detect possible Sysmon driver unload
date: 2019/10/23
modified: 2019/11/07
references:
    - https://www.darkoperator.com/blog/2018/10/5/operating-offensively-against-sysmon
logsource:
    product: windows
    category: process_creation
detection:
    selection:
        Image|endswith: '\fltmc.exe'
        CommandLine|contains|all:
            - 'unload'
            - 'sys'
    condition: selection
falsepositives: Unknown
level: high
fields:
    - CommandLine
    - Details

```





### es-qs
    
```
(Image.keyword:*\\\\fltmc.exe AND CommandLine.keyword:*unload* AND CommandLine.keyword:*sys*)
```


### xpack-watcher
    
```
curl -s -XPUT -H \'Content-Type: application/json\' --data-binary @- localhost:9200/_watcher/watch/Sysmon-driver-unload <<EOF\n{\n  "metadata": {\n    "title": "Sysmon driver unload",\n    "description": "Detect possible Sysmon driver unload",\n    "tags": "",\n    "query": "(Image.keyword:*\\\\\\\\fltmc.exe AND CommandLine.keyword:*unload* AND CommandLine.keyword:*sys*)"\n  },\n  "trigger": {\n    "schedule": {\n      "interval": "30m"\n    }\n  },\n  "input": {\n    "search": {\n      "request": {\n        "body": {\n          "size": 0,\n          "query": {\n            "bool": {\n              "must": [\n                {\n                  "query_string": {\n                    "query": "(Image.keyword:*\\\\\\\\fltmc.exe AND CommandLine.keyword:*unload* AND CommandLine.keyword:*sys*)",\n                    "analyze_wildcard": true\n                  }\n                }\n              ],\n              "filter": {\n                "range": {\n                  "timestamp": {\n                    "gte": "now-30m/m"\n                  }\n                }\n              }\n            }\n          }\n        },\n        "indices": []\n      }\n    }\n  },\n  "condition": {\n    "compare": {\n      "ctx.payload.hits.total": {\n        "not_eq": 0\n      }\n    }\n  },\n  "actions": {\n    "send_email": {\n      "email": {\n        "to": "root@localhost",\n        "subject": "Sigma Rule \'Sysmon driver unload\'",\n        "body": "Hits:\\n{{#ctx.payload.hits.hits}}Hit on {{_source.@timestamp}}:\\nCommandLine = {{_source.CommandLine}}\\n    Details = {{_source.Details}}================================================================================\\n{{/ctx.payload.hits.hits}}",\n        "attachments": {\n          "data.json": {\n            "data": {\n              "format": "json"\n            }\n          }\n        }\n      }\n    }\n  }\n}\nEOF\n
```


### graylog
    
```
(Image.keyword:*\\\\fltmc.exe AND CommandLine.keyword:*unload* AND CommandLine.keyword:*sys*)
```


### splunk
    
```
(Image="*\\\\fltmc.exe" CommandLine="*unload*" CommandLine="*sys*") | table CommandLine,Details
```


### logpoint
    
```
(event_id="1" Image="*\\\\fltmc.exe" CommandLine="*unload*" CommandLine="*sys*")
```


### grep
    
```
grep -P '^(?:.*(?=.*.*\\fltmc\\.exe)(?=.*.*unload.*)(?=.*.*sys.*))'
```



