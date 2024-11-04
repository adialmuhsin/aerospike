# Deploy

## Community
### 1 Node
```sh
$ podman run -d --name aerospike -p 3000-3002:3000-3002 aerospike/aerospike-server
```

#### Connect to aerospike server
#### aql
```sh
$ podman run -ti aerospike/aerospike-tools:latest aql -h  $(podman inspect -f '{{.NetworkSettings.IPAddress }}' aerospike)
```

#### asadm
```sh
$ podman run -ti aerospike/aerospike-tools:latest asadm -h  $(podman inspect -f '{{.NetworkSettings.IPAddress }}' aerospike)
```

### 2 Node
```sh
$ podman run --rm -tid --name aerospike1 -p 3000:3000 -p 3001:3001 -p 3002:3002 -p 3003:3003 aerospike/aerospike-server

$ podman run --rm -tid --name aerospike2 -p 6000:3000 -p 6001:3001 -p 6002:3002 -p 6003:3003 aerospike/aerospike-server
```

#### Create local config aerospike.conf
```sh
$ podman cp aero1/aerospike.conf aerospike1:/etc/aerospike/aerospike.conf
$ podman cp aero2/aerospike.conf aerospike2:/etc/aerospike/aerospike.conf
```

#### Connect between nodes to create cluster
```sh
$ podman exec -ti aerospike1 asinfo -v service

$ podman exec -ti aerospike2 asinfo -v service

$ podman exec -ti aerospike1 asinfo -v 'tip:host=<IP ADDRESS from aerospike2>;port=3002'

```

#### Verify 2 clusters up
```sh
$ podman exec -ti aerospike1 asadm

Found 2 nodes
Online:  172.17.0.3:3000, 172.17.0.2:3000
```

#### Confirm containerized app connection using aerospike-tools container
```sh
$ podman run --rm -ti --name aerospike-tools aerospike/aerospike-tools aql -h <IP ADDRESS from aerospike1/aerospike2>

aql> INSERT INTO test.demo (foo, bar) VALUES (123, 'abc')
aql> select * from test.demo
+-----+-------+
| foo | bar   |
+-----+-------+
| 123 | "abc" |
+-----+-------+
1 row in set (0.147 secs)
```

#### Benchmarking
```sh
$ podman exec -it aerospike1 asadm
Admin> info
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~Network Information (2024-11-04 09:57:03 UTC)~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
             Node|         Node ID|            IP|    Build|Migrations|~~~~~~~~~~~~~~~~~~Cluster~~~~~~~~~~~~~~~~~~|Client|  Uptime
                 |                |              |         |          |Size|         Key|Integrity|      Principal| Conns|
10.88.0.5:3000   |*BB9DD5DC12DC062|10.88.0.5:3000|C-7.2.0.1|   0.000  |   2|5DCDEFD00B4C|True     |BB9DD5DC12DC062|     7|00:22:24
6376ab85349e:3000| BB9294618065DB2|10.88.0.4:3000|C-7.2.0.1|   0.000  |   2|5DCDEFD00B4C|True     |BB9DD5DC12DC062|     7|00:22:24
Number of rows: 2

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~Namespace Usage Information (2024-11-04 09:57:03 UTC)~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Namespace|             Node|Evictions|  Stop|~System~|~~~~Primary Index~~~~|~~Secondary~~|~~~~~~~~~~~~~~~~~~Storage Engine~~~~~~~~~~~~~~~~~
         |                 |         |Writes|~Memory~|Type|     Used|Evict%|~~~~Index~~~~|  Type|     Used|Used%|Evict%|  Used|Avail%|Avail
         |                 |         |      |  Avail%|    |         |      |Type|    Used|      |         |     |      | Stop%|      |Stop%
test     |10.88.0.5:3000   |  0.000  |False |      89|mem |64.000 B | 0.0 %|mem |0.000 B |device|80.000 B |0.0 %| 0.0 %|70.0 %|99.0 %|5.0 %
test     |6376ab85349e:3000|  0.000  |False |      89|mem | 0.000 B | 0.0 %|mem |0.000 B |device| 0.000 B |0.0 %| 0.0 %|70.0 %|99.0 %|5.0 %
test     |                 |  0.000  |      |        |    |64.000 B |      |    |0.000 B |      |80.000 B |0.0 %|      |      |      |
Number of rows: 2

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~Namespace Object Information (2024-11-04 09:57:03 UTC)~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Namespace|             Node|Rack|  Repl|Expirations|  Total|~~~~~~~~~~Objects~~~~~~~~~~|~~~~~~~~~Tombstones~~~~~~~~|~~~~Pending~~~~
         |                 |  ID|Factor|           |Records| Master|  Prole|Non-Replica| Master|  Prole|Non-Replica|~~~~Migrates~~~
         |                 |    |      |           |       |       |       |           |       |       |           |     Tx|     Rx
test     |10.88.0.5:3000   |   0|     1|    0.000  |1.000  |1.000  |0.000  |    0.000  |0.000  |0.000  |    0.000  |0.000  |0.000
test     |6376ab85349e:3000|   0|     1|    0.000  |0.000  |0.000  |0.000  |    0.000  |0.000  |0.000  |    0.000  |0.000  |0.000
test     |                 |    |      |    0.000  |1.000  |1.000  |0.000  |    0.000  |0.000  |0.000  |    0.000  |0.000  |0.000
Number of rows: 2
```

## Reporting Issues

Aerospike Enterprise evaluation users, if you have any problems with or questions about this image, please post on the [Aerospike discussion forum](https://discuss.aerospike.com) or open an issue in [aerospike/aerospike-server-enterprise.docker](https://github.com/aerospike/aerospike-server-enterprise.docker/issues).

Enterprise customers are welcome to participate in the community forum, but can also report issues through the [enterprise support system](https://support.aerospike.com/).

Community Edition users may report problems or ask questions about this image on the [Aerospike Forums](https://discuss.aerospike.com/) or open an issue in [aerospike/aerospike-server.docker](https://github.com/aerospike/aerospike-server.docker/issues).

## License

If you are using Aerospike Database CE refer to the license information in the [aerospike/aerospike-server](https://github.com/aerospike/aerospike-server) repository.