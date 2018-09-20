# LTE TOPOLOGY MANAGER

LTE Topology Manager is gathering information from different txt files, combining it together and sending it to a Message Broker.
It could work as a Daemon (background) or not. It combines a file with the topological information from the user with another one with the names (headers) 
of the fields in order to understand the topological information stored from a given the user.

An example of command line to execute the LTE Topology Manager could be (working directory: ./):
```sh
selfnet@selfnet:~$ ./TopologyManager.bin /home/selfnet/ltetopology.txt /home/selfnet/headersltetopology.txt /home/selfnet/sqlconfig.ini /home/selfnet/rabbitconfig.ini oai /home/selfnet/send.bin nosql daemon
```
The information stored in the file ltetopology.txt is as follow 
```csv
ue_event,update,dc001ca0,0001,5,0001,717f3e8c,192.168.12.2,40.3.54.7,188.0.0.2,4157624828
```
Names/Headers are provided to understand every field about the above information. An example of headers for the above example is below.
```csv
eventType,type,mme_teid_S11,sgw_teid_S11,EPS_Bearer_ID,sgw_teid_S1,eNB_teid_S1u,SGW_IP_S1u,eNB_IP_S1u,UE_IP,UEID
```
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

