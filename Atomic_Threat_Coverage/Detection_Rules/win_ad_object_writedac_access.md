| Title                | T1000 AD Object WriteDAC Access                                                                                                                                                 |
|:---------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Description          | Detects WRITE_DAC access to a domain object                                                                                                                                           |
| ATT&amp;CK Tactic    |   This Detection Rule wasn't mapped to ATT&amp;CK Tactic yet  |
| ATT&amp;CK Technique |  This Detection Rule wasn't mapped to ATT&amp;CK Technique yet  |
| Data Needed          | <ul><li>[DN_0030_4662_operation_was_performed_on_an_object](../Data_Needed/DN_0030_4662_operation_was_performed_on_an_object.md)</li></ul>  |
| Enrichment           |  Data for this Detection Rule doesn't require any Enrichments.  |
| Trigger              |  There is no documented Trigger for this Detection Rule yet  |
| Severity Level       | critical |
| False Positives      | <ul><li>Unknown</li></ul>  |
| Development Status   | experimental |
| References           | <ul><li>[https://github.com/Cyb3rWard0g/ThreatHunter-Playbook/tree/master/playbooks/windows/05_defense_evasion/T1222_file_permissions_modification/ad_replication_user_backdoor.md](https://github.com/Cyb3rWard0g/ThreatHunter-Playbook/tree/master/playbooks/windows/05_defense_evasion/T1222_file_permissions_modification/ad_replication_user_backdoor.md)</li></ul>  |
| Author               | Roberto Rodriguez @Cyb3rWard0g |


## Detection Rules

### Sigma rule

```
title: T1000 AD Object WriteDAC Access
id: 028c7842-4243-41cd-be6f-12f3cf1a26c7
description: Detects WRITE_DAC access to a domain object
status: experimental
date: 2019/09/12
author: Roberto Rodriguez @Cyb3rWard0g
references:
    - https://github.com/Cyb3rWard0g/ThreatHunter-Playbook/tree/master/playbooks/windows/05_defense_evasion/T1222_file_permissions_modification/ad_replication_user_backdoor.md
logsource:
    product: windows
    service: security
detection:
    selection: 
        EventID: 4662
        ObjectServer: 'DS'
        AccessMask: 0x40000
        ObjectType:
            - '19195a5b-6da0-11d0-afd3-00c04fd930c9'
            - 'domainDNS'
    condition: selection
falsepositives:
    - Unknown
level: critical

```





### es-qs
    
```
(EventID:"4662" AND ObjectServer:"DS" AND AccessMask:"262144" AND ObjectType:("19195a5b\\-6da0\\-11d0\\-afd3\\-00c04fd930c9" OR "domainDNS"))
```


### xpack-watcher
    
```
curl -s -XPUT -H \'Content-Type: application/json\' --data-binary @- localhost:9200/_watcher/watch/T1000-AD-Object-WriteDAC-Access <<EOF\n{\n  "metadata": {\n    "title": "T1000 AD Object WriteDAC Access",\n    "description": "Detects WRITE_DAC access to a domain object",\n    "tags": "",\n    "query": "(EventID:\\"4662\\" AND ObjectServer:\\"DS\\" AND AccessMask:\\"262144\\" AND ObjectType:(\\"19195a5b\\\\-6da0\\\\-11d0\\\\-afd3\\\\-00c04fd930c9\\" OR \\"domainDNS\\"))"\n  },\n  "trigger": {\n    "schedule": {\n      "interval": "30m"\n    }\n  },\n  "input": {\n    "search": {\n      "request": {\n        "body": {\n          "size": 0,\n          "query": {\n            "bool": {\n              "must": [\n                {\n                  "query_string": {\n                    "query": "(EventID:\\"4662\\" AND ObjectServer:\\"DS\\" AND AccessMask:\\"262144\\" AND ObjectType:(\\"19195a5b\\\\-6da0\\\\-11d0\\\\-afd3\\\\-00c04fd930c9\\" OR \\"domainDNS\\"))",\n                    "analyze_wildcard": true\n                  }\n                }\n              ],\n              "filter": {\n                "range": {\n                  "timestamp": {\n                    "gte": "now-30m/m"\n                  }\n                }\n              }\n            }\n          }\n        },\n        "indices": []\n      }\n    }\n  },\n  "condition": {\n    "compare": {\n      "ctx.payload.hits.total": {\n        "not_eq": 0\n      }\n    }\n  },\n  "actions": {\n    "send_email": {\n      "email": {\n        "to": "root@localhost",\n        "subject": "Sigma Rule \'T1000 AD Object WriteDAC Access\'",\n        "body": "Hits:\\n{{#ctx.payload.hits.hits}}{{_source}}\\n================================================================================\\n{{/ctx.payload.hits.hits}}",\n        "attachments": {\n          "data.json": {\n            "data": {\n              "format": "json"\n            }\n          }\n        }\n      }\n    }\n  }\n}\nEOF\n
```


### graylog
    
```
(EventID:"4662" AND ObjectServer:"DS" AND AccessMask:"262144" AND ObjectType:("19195a5b\\-6da0\\-11d0\\-afd3\\-00c04fd930c9" "domainDNS"))
```


### splunk
    
```
(EventID="4662" ObjectServer="DS" AccessMask="262144" (ObjectType="19195a5b-6da0-11d0-afd3-00c04fd930c9" OR ObjectType="domainDNS"))
```


### logpoint
    
```
(event_source="Microsoft-Windows-Security-Auditing" event_id="4662" ObjectServer="DS" AccessMask="262144" ObjectType IN ["19195a5b-6da0-11d0-afd3-00c04fd930c9", "domainDNS"])
```


### grep
    
```
grep -P '^(?:.*(?=.*4662)(?=.*DS)(?=.*262144)(?=.*(?:.*19195a5b-6da0-11d0-afd3-00c04fd930c9|.*domainDNS)))'
```



