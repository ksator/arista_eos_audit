![GitHub](https://img.shields.io/github/license/ksator/arista_eos_audit)   
 
## Table of content

[About this repository](#about-this-repository)  
[Requirements](#requirements)   
[Repository details](#repository-details)   

## About this repository 

This repo has python content to collect eos commands from Arista devices.  
It also has python content to audit offline the data collected and to generate a report.  

## Requirements

I am using this Python version
```
python -V
Python 3.7.7
```
And I installed netmiko to interact with EOS devices.  
```
pip freeze | grep netmiko
netmiko==3.1.1
```

## Repository details 

### [input.yml](input.yml) file 

The file [input.yml](input.yml) has the required input for the files [collect_eos_commands.py](collect_eos_commands.py) and  [audit_eos_files.py](audit_eos_files.py)   
It is used to define these variables:    
- devices: list of EOS devices
- username: devices username 
- password: devices password
- text_cmds: list EOS commands to collect in text format
- json_cmds: list EOS commands to collect in JSON format
- text_and_json_cmds: list EOS commands to collect in text and JSON format 
- output_directory: directory to save the show commands collected and the reports generated
- custom_show_tech_support: list of files (show commands) to include in a custom show tech-support text file. 
- audit: list of topics to audit and to include in the report.  

### [collect_eos_commands.py](collect_eos_commands.py) file 

The file [collect_eos_commands.py](collect_eos_commands.py) uses the variables defined in the file [input.yml](input.yml) to collect show commands from EOS devices.  
It supports collecting show commands in both text and JSON format.  
The commands output is saved in device directory in the [output](output) directory. 

### [custom_show_tech_support.py](custom_show_tech_support.py)

The file [custom_show_tech_support.py](custom_show_tech_support.py) uses the variables defined in the file [input.yml](input.yml) to generate offline a custom show tech-support text file.  

For each devices indicated in [input.yml](input.yml), it assembles the files indicated in [input.yml](input.yml) file to generate offline a custom show tech-support text file.  
It supports only the text format (no JSON format support).  
The name of the output file is "custom show tech-support.txt".  It is saved in device directory in the [output](output) directory. 

  
### [audit_eos_files.py](audit_eos_files.py) file 

The file [audit_eos_files.py](audit_eos_files.py) uses the variables defined in the file [input.yml](input.yml) to audit offline some of the collected files and to generate a report.  

For each device it generates 2 reports: 
- The file "main.txt" includes all the tests. It is saved in device directory in the [output](output) directory. 
- The file "failures_only.txt" includes only the tests that failed. It is saved in device directory in the [output](output) directory. 

It also assembles the devices report in one file: 
- The file [main.txt](output/main.txt) includes for all the devices all the tests. It is saved in the [output](output) directory. 
- The file [failures_only.txt](output/failures_only.txt) includes for all the devices only the tests that failed. It is saved in the [output](output) directory.  

It currently support these features:  
- hostname
  - requirements: ```show hostname | json```
  - description: add to the report the device hostname and fqdn 
  - failure conditions: This is a report without any test so there is no failure/passing condition
- version
  - requirements: ```show version | json```
  - description: add to the report some details regarding the device (HW model, SN, SW release, uptime)
  - Failure conditions: This is a report without any test so there is no failure/passing condition
- inventory 
  - requirements: ```show inventory | json```
  - description: 
  - Failure conditions: A test fails if the manufacturer of a transceiver is not Arista Networks or if a power supply slot has no power supply unit inserted
- power 
  - requirements: ```show system environment power| json```
  - description: 
  - Failure conditions: A test fails if the status of a power supply is not ok
- cooling
  - requirements: ```show system environment cooling | json```
  - description: 
  - Failure conditions: A test fails if the status of a fan is not ok
- temperature
  - requirements: ```show system environment temperature | json```
  - description: 
  - Failure conditions: A test fails if a sensor HW status is not OK or if a sensor alert count is > 0 or if a sensor is currently in alert state. The system temperature test fails if the system status is not OK
- temperature_transceivers
  - requirements: ```show system environment temperature transceiver | json```
  - description: 
  - Failure conditions: Failure conditions: A test fails if a sensor HW status is not OK or if a sensor alert count is > 0 or if a sensor is currently in alert state
- reload_cause_history
  - requirements: ```show reload cause history | json```
  - description: 
  - Failure conditions: A test fails if a device reload was not requested by user
- reload_cause_full
  - requirements: ```show reload cause full | json```
  - description: 
  - Failure conditions: The test fails if the device reload was not requested by user
- lldp
  - requirements: ```show lldp neighbors | json```
  - description: 
  - Failure conditions: This is a report without any test so there is no failure/passing condition
- bgp
  - requirements: ```show ip bgp summary vrf all | json```
  - description: 
  - Failure conditions: A test fails if a BGP session is not established
- mlag
  - requirements: ```show mlag detail | json```
  - description: 
  - Failure conditions: The test fails if the MLAG state is active and the negotiation status is not connected
  

 

