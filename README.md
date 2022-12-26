
# Configure MongoDB Replica set

To configure mongodb replication set, you have to have minimum 2 **mongod** processes running.
And then you can connect them to one replica set. \
2 mongod processes are needed for: **1 primary** and **1 secondary** node


&nbsp;
&nbsp;


### Default mongod configuration
Example configuration options for simple mongod process:
```yaml
storage:
   dbPath: "/var/lib/mongodb/"

net:
   bindIp: 127.0.0.1
   port: 28001

systemLog:
   destination: file
   path: "/var/log/mongodb/mongod.log"
   logAppend: true

processManagement:
   fork: true

setParameter:
   enableLocalhostAuthBypass: false
```


&nbsp;
&nbsp;

### Changing configuration accordingly
To mark mongod process as replica set member you have add replica set name to its config:
```yaml
...
replication:
  replSetName: "REPLICA_SET_NAME"
...
```


&nbsp;
&nbsp;


### Initiating replica set
You have to run following command in one of mongod processes to start replica set:

```bash
rs.initiate({
    _id: "REPLICA_SET_NAME",
    members: [
        { "_id": 1, "host": "myhost:28001", "priority": 1, "votes": 1 },
        { "_id": 2, "host": "myhost:28002", "priority": 1, "votes": 1 },
        { "_id": 3, "host": "myhost:28003", "priority": 1, "votes": 1 }
    ],
})
```

You can give some members higher priority to increase their chances of becoming primary node. 
Before running this command, you have to make sure that all mongod processes are running stable.

After this command there will be created new replica set, and first thing it tries to do is start **election**, and elect **primary node**.
After new primary is elected, secondary nodes all replicate data from primary.

*For more information on initiating replica set: [MongoDB documentation](https://www.mongodb.com/docs/manual/reference/method/rs.initiate/)*


&nbsp;
&nbsp;


### Connecting

After you initiated replica set you can read/write to primary, but cannot write secondary. This is to prevent different versions of document in different locations.

To connect primary or one of secondary nodes, you can just connect their host:
```bash
mongo --host "localhost:28001"
```


Or you can connect to replica set primary only by:
```bash
mongo --host "REPLICA_SET_NAME/localhost:28001"
```
