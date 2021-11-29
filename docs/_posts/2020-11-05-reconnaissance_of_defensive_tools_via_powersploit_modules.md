---
title: "Reconnaissance of Defensive Tools via PowerSploit modules"
excerpt: "Software, Vulnerability Scanning, Gather Victim Host Information, Active Scanning"
categories:
  - Endpoint
last_modified_at: 2020-11-05
toc: true
toc_label: ""
tags:
  - Software
  - Reconnaissance
  - Vulnerability Scanning
  - Reconnaissance
  - Gather Victim Host Information
  - Reconnaissance
  - Active Scanning
  - Reconnaissance
  - Splunk Behavioral Analytics
---



[Try in Splunk Security Cloud](https://www.splunk.com/en_us/cyber-security.html){: .btn .btn--success}

#### Description

This detection identifies use of PowerSploit modules for assessment of presence of defensive tools.

- **Type**: TTP
- **Product**: Splunk Behavioral Analytics
- **Datamodel**: 
- **Last Updated**: 2020-11-05
- **Author**: Stanislav Miskovic, Splunk
- **ID**: 24b4e659-63a2-4e7b-89ac-87dd659c7110


#### [ATT&CK](https://attack.mitre.org/)

| ID          | Technique   | Tactic         |
| ----------- | ----------- |--------------- |
| [T1592.002](https://attack.mitre.org/techniques/T1592/002/) | Software | Reconnaissance |

| [T1595.002](https://attack.mitre.org/techniques/T1595/002/) | Vulnerability Scanning | Reconnaissance |

| [T1592](https://attack.mitre.org/techniques/T1592/) | Gather Victim Host Information | Reconnaissance |

| [T1595](https://attack.mitre.org/techniques/T1595/) | Active Scanning | Reconnaissance |

#### Search

```

| from read_ssa_enriched_events()

| eval timestamp=parse_long(ucast(map_get(input_event, "_time"), "string", null)), cmd_line=ucast(map_get(input_event, "process"), "string", null), event_id=ucast(map_get(input_event, "event_id"), "string", null) 
| where cmd_line != null AND ( match_regex(cmd_line, /(?i)Find-AVSignature/)=true )

| eval start_time = timestamp, end_time = timestamp, entities = mvappend( ucast(map_get(input_event, "dest_user_id"), "string", null), ucast(map_get(input_event, "dest_device_id"), "string", null)), body=create_map(["event_id", event_id,  "cmd_line", cmd_line]) 
| into write_ssa_detected_events();
```

#### Associated Analytic Story
* [Windows Discovery Techniques](/stories/windows_discovery_techniques)


#### How To Implement
You must be ingesting Windows Security logs from devices of interest, including the event ID 4688 with enabled command line logging.

#### Required field
* _time
* process
* dest_device_id
* dest_user_id


#### Kill Chain Phase
* Actions on Objectives


#### Known False Positives
None identified.


#### RBA

| Risk Score  | Impact      | Confidence   | Message      |
| ----------- | ----------- |--------------|--------------|
| 40.0 | 40 | 100 | PowerSploit malware is looking for presence of anti virus software. Operation is performed at the device $dest_device_id$, by the account $dest_user_id$ via command $cmd_line$ |




#### Reference

* [https://github.com/PowerShellMafia/PowerSploit](https://github.com/PowerShellMafia/PowerSploit)



#### Test Dataset
Replay any dataset to Splunk Enterprise by using our [`replay.py`](https://github.com/splunk/attack_data#using-replaypy) tool or the [UI](https://github.com/splunk/attack_data#using-ui).
Alternatively you can replay a dataset into a [Splunk Attack Range](https://github.com/splunk/attack_range#replay-dumps-into-attack-range-splunk-server)




[*source*](https://github.com/splunk/security_content/tree/develop/detections/endpoint/reconnaissance_of_defensive_tools_via_powersploit_modules.yml) \| *version*: **1**