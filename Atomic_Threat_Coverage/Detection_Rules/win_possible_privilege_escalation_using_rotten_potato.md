| Title                | Possible Rotten Potato detection - privilege escalation fro Service accounts to SYSTEM                                                                                                                                                 |
|:---------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Description          | Detection of child processes spawned with SYSTEM privileges by parents with LOCAL SERVICE or NETWORK SERVICE privileges                                                                                                                                           |
| ATT&amp;CK Tactic    |  <ul><li>[TA0004: Privilege Escalation](https://attack.mitre.org/tactics/TA0004)</li></ul>  |
| ATT&amp;CK Technique | <ul><li>[T1134: Access Token Manipulation](https://attack.mitre.org/techniques/T1134)</li></ul>  |
| Data Needed          | <ul><li>[DN_0003_1_windows_sysmon_process_creation](../Data_Needed/DN_0003_1_windows_sysmon_process_creation.md)</li></ul>  |
| Enrichment           | <ul><li>[EN_0001_cache_sysmon_event_id_1_info](../Enrichments/EN_0001_cache_sysmon_event_id_1_info.md)</li><li>[EN_0002_enrich_sysmon_event_id_1_with_parent_info](../Enrichments/EN_0002_enrich_sysmon_event_id_1_with_parent_info.md)</li></ul>  |
| Trigger              | <ul><li>[T1134: Access Token Manipulation](../Triggers/T1134.md)</li></ul>  |
| Severity Level       | high |
| False Positives      | <ul><li>Unknown</li></ul>  |
| Development Status   | experimental |
| References           | <ul><li>[https://speakerdeck.com/heirhabarov/hunting-for-privilege-escalation-in-windows-environment](https://speakerdeck.com/heirhabarov/hunting-for-privilege-escalation-in-windows-environment)</li><li>[https://foxglovesecurity.com/2016/09/26/rotten-potato-privilege-escalation-from-service-accounts-to-system/](https://foxglovesecurity.com/2016/09/26/rotten-potato-privilege-escalation-from-service-accounts-to-system/)</li></ul>  |
| Author               | Teymur Kheirkhabarov |


## Detection Rules

### Sigma rule

```
title: Possible Rotten Potato detection - privilege escalation fro Service accounts to SYSTEM
id: 6c5808ee-85a2-4e56-8137-72e5876a5096
description: Detection of child processes spawned with SYSTEM privileges by parents with LOCAL SERVICE or NETWORK SERVICE privileges
references:
    - https://speakerdeck.com/heirhabarov/hunting-for-privilege-escalation-in-windows-environment
    - https://foxglovesecurity.com/2016/09/26/rotten-potato-privilege-escalation-from-service-accounts-to-system/
tags:
    - attack.privilege_escalation
    - attack.t1134
status: experimental
author: Teymur Kheirkhabarov
date: 2019/10/26
modified: 2019/11/11
logsource:
    category: process_creation
    product: windows
detection:
    selection:
        ParentUser:
            - 'NT AUTHORITY\NETWORK SERVICE'
            - 'NT AUTHORITY\LOCAL SERVICE'
        User: 'NT AUTHORITY\SYSTEM'
    rundllexception:
        Image|endswith: '\rundll32.exe'
        CommandLine|contains: 'DavSetCookie'
    condition: selection and not rundllexception
falsepositives:
    - Unknown
level: high
enrichment:
    - EN_0001_cache_sysmon_event_id_1_info                # http://bit.ly/314zc6x
    - EN_0002_enrich_sysmon_event_id_1_with_parent_info   # http://bit.ly/2KmSC0l

```





### es-qs
    
```
((ParentUser:("NT\\ AUTHORITY\\\\NETWORK\\ SERVICE" OR "NT\\ AUTHORITY\\\\LOCAL\\ SERVICE") AND User:"NT\\ AUTHORITY\\\\SYSTEM") AND (NOT (Image.keyword:*\\\\rundll32.exe AND CommandLine.keyword:*DavSetCookie*)))
```


### xpack-watcher
    
```
curl -s -XPUT -H \'Content-Type: application/json\' --data-binary @- localhost:9200/_watcher/watch/Possible-Rotten-Potato-detection---privilege-escalation-fro-Service-accounts-to-SYSTEM <<EOF\n{\n  "metadata": {\n    "title": "Possible Rotten Potato detection - privilege escalation fro Service accounts to SYSTEM",\n    "description": "Detection of child processes spawned with SYSTEM privileges by parents with LOCAL SERVICE or NETWORK SERVICE privileges",\n    "tags": [\n      "attack.privilege_escalation",\n      "attack.t1134"\n    ],\n    "query": "((ParentUser:(\\"NT\\\\ AUTHORITY\\\\\\\\NETWORK\\\\ SERVICE\\" OR \\"NT\\\\ AUTHORITY\\\\\\\\LOCAL\\\\ SERVICE\\") AND User:\\"NT\\\\ AUTHORITY\\\\\\\\SYSTEM\\") AND (NOT (Image.keyword:*\\\\\\\\rundll32.exe AND CommandLine.keyword:*DavSetCookie*)))"\n  },\n  "trigger": {\n    "schedule": {\n      "interval": "30m"\n    }\n  },\n  "input": {\n    "search": {\n      "request": {\n        "body": {\n          "size": 0,\n          "query": {\n            "bool": {\n              "must": [\n                {\n                  "query_string": {\n                    "query": "((ParentUser:(\\"NT\\\\ AUTHORITY\\\\\\\\NETWORK\\\\ SERVICE\\" OR \\"NT\\\\ AUTHORITY\\\\\\\\LOCAL\\\\ SERVICE\\") AND User:\\"NT\\\\ AUTHORITY\\\\\\\\SYSTEM\\") AND (NOT (Image.keyword:*\\\\\\\\rundll32.exe AND CommandLine.keyword:*DavSetCookie*)))",\n                    "analyze_wildcard": true\n                  }\n                }\n              ],\n              "filter": {\n                "range": {\n                  "timestamp": {\n                    "gte": "now-30m/m"\n                  }\n                }\n              }\n            }\n          }\n        },\n        "indices": []\n      }\n    }\n  },\n  "condition": {\n    "compare": {\n      "ctx.payload.hits.total": {\n        "not_eq": 0\n      }\n    }\n  },\n  "actions": {\n    "send_email": {\n      "email": {\n        "to": "root@localhost",\n        "subject": "Sigma Rule \'Possible Rotten Potato detection - privilege escalation fro Service accounts to SYSTEM\'",\n        "body": "Hits:\\n{{#ctx.payload.hits.hits}}{{_source}}\\n================================================================================\\n{{/ctx.payload.hits.hits}}",\n        "attachments": {\n          "data.json": {\n            "data": {\n              "format": "json"\n            }\n          }\n        }\n      }\n    }\n  }\n}\nEOF\n
```


### graylog
    
```
((ParentUser:("NT AUTHORITY\\\\NETWORK SERVICE" "NT AUTHORITY\\\\LOCAL SERVICE") AND User:"NT AUTHORITY\\\\SYSTEM") AND (NOT (Image.keyword:*\\\\rundll32.exe AND CommandLine.keyword:*DavSetCookie*)))
```


### splunk
    
```
(((ParentUser="NT AUTHORITY\\\\NETWORK SERVICE" OR ParentUser="NT AUTHORITY\\\\LOCAL SERVICE") User="NT AUTHORITY\\\\SYSTEM") NOT (Image="*\\\\rundll32.exe" CommandLine="*DavSetCookie*"))
```


### logpoint
    
```
(event_id="1" (ParentUser IN ["NT AUTHORITY\\\\NETWORK SERVICE", "NT AUTHORITY\\\\LOCAL SERVICE"] User="NT AUTHORITY\\\\SYSTEM")  -(Image="*\\\\rundll32.exe" CommandLine="*DavSetCookie*"))
```


### grep
    
```
grep -P '^(?:.*(?=.*(?:.*(?=.*(?:.*NT AUTHORITY\\NETWORK SERVICE|.*NT AUTHORITY\\LOCAL SERVICE))(?=.*NT AUTHORITY\\SYSTEM)))(?=.*(?!.*(?:.*(?=.*.*\\rundll32\\.exe)(?=.*.*DavSetCookie.*)))))'
```



