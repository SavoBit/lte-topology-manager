# LTE TOPOLOGY MANAGER

-----
Key | Value
------------ | -------------
Name | LTE TOPOLOGY MANAGER
Acronym | LTETM
Use case | ALL
Instantiation | Physical Network Function (PNF) and Virtual Network Function (VNF)
Type | Inventory
Scope  | LTE Infrastructure
Management Protocol | XML
Deliverable | [3.4](https://github.com/Selfnet-5G/WP3_SO/blob/master/Doku/D3.4/D3.4_master.pdf) 

------

# Description

LTE Topology Manager is gathering information from different txt files, combining it together and sending it to a Message Broker.
It could work as a Daemon (background) or not. It combines a file with the topological information from the user with another one with the names (headers) of the fields in order to understand the topological information stored from a given the user. The txt files are
produced by the modified version of the OpenAirInterface project. It has publically a branch entitled "lte_topology" with the modification required to run this component. 

# Usage

An example of command line to execute the LTE Topology Manager could be (working directory: ./):
```sh
selfnet@selfnet:~$ ./TopologyManager.bin /home/selfnet/ltetopology.txt /home/selfnet/headersltetopology.txt /home/selfnet/sqlconfig.ini /home/selfnet/rabbitconfig.ini oai /home/selfnet/send.bin nosql daemon
```

# Input Interface
The information stored in the file ltetopology.txt is as follow 
```csv
ue_event,update,dc001ca0,0001,5,0001,717f3e8c,192.168.12.2,40.3.54.7,188.0.0.2,4157624828
```
Names/Headers are provided to understand every field about the above information. An example of headers for the above example is below.
```csv
eventType,type,mme_teid_S11,sgw_teid_S11,EPS_Bearer_ID,sgw_teid_S1,eNB_teid_S1u,SGW_IP_S1u,eNB_IP_S1u,UE_IP,UEID
```

# Output Interface
What the RabbitMQ "Topology" exchange will receive would be a formatted and valid json as we can see below.
```json
{
	"eventType": "ue_event",
	"type": "delete",
	"mme_teid_S11": "dc001ca0",
	"sgw_teid_S11": "0001",
	"EPS_Bearer_ID": "5",
	"sgw_teid_S1": "0001",
	"eNB_teid_S1u": "717f3e8c",
	"SGW_IP_S1u": "192.168.12.198",
	"eNB_IP_S1u": "192.168.12.201",
	"UE_IP": "192.188.0.2",
	"UEID": "3221228960"
}
```
### Installation

There is no installation needed, there is an executable file :which needs the following parameters:
 - Parameter 1: Absolute path for the file where the information about topological information is stored.
 - Parameter 2: Absolute par for the path where the headers to understand the topological information is stored.
 - Parameter 3: Absolute path for the configuration file of mysql.
 - Parameter 4: Absolute path for the configuration file of rabbitMQ.
 - Parameter 5: Mode in which daemon has to work ("oai" or "pipe" - "oai" -> It read from txt files and sent it to RabbitMQ Exchange ;  "pipe" (to revise) -> It read from screen and sent it to RabbitMQ Exchange)
 - Parameter 6: Absolute path for the "send" executable used to send information to rabbitMQ.
 - Parameter 7: Enable/Disable send the topological information to a MysqlSQL database. ("sql" ,"nosql") (Tables for DB creating described in db.sql file)
 - Parameter 8: Enable/Disable working in daemon mode or not. ("daemon" , "nodaemon" -> "nodaemon" block console and "daemob" running in background)


### Working Modes

Essential working mode: LTE Topology Manager in "oai" mode running inthe of the SGW and sending information to a RabbitMQ in the same tenant network. 

Extended working mode: It is the essential working mode +:
 - LTE Topology Proxy running in the OpenStack Neutrong using the name space of the tenant in order to stablish a connection to the RabitMQ of such tenant. (shows topology info in screen) 
 - LTE Topology Manager running in the OpenStack Neutrong (no namespace) in "pipe mode". It will read from screen and sent to the Admin RabbitMQ. 
 
 
 
# License
## Authors
5G Flow Monitoring Agent. Copyright (C) 2016-2018 Ricardo Marco Alaez, Jose M. Alcaraz Calero, Qi wang. University of the West of Scotland
  
## License
Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at
  http://www.apache.org/licenses/LICENSE-2.0
  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License.
  
# Acknowledge
Software Supported by H2020 5G-PPP SELFNET PROJECT with project ID H2020-ICT-2014-2;


