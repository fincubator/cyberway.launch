
# Golos Deployment How-to

## System requirements

- Debian 10
- Docker 
- Docker compose

## Install system dependencies 

```
apt update && apt install curl git wget jq
```


Ensure that docker and docker-compose have installed.
```
curl -fsSL https://get.docker.com -o get-docker.sh
sh ./get-docker.sh
curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```

## Install Cyberway Full Node on Debian 10

```
git clone https://github.com/cyberway/cyberway.launch.git
cd cyberway.launch
./start_full_node.sh
```

Ensure that all containers have started

```
docker ps
```

Check sync status with [api](https://docs.cyberway.io/software_manuals/api_reference/nodeos_chain_api#get_info)

```
curl -s --request POST --data '' http://127.0.0.1:8888/v1/chain/get_info| jq
``` 

Output. Check `head_block_num`.
```
{
  "server_version": "e945893a",
  "chain_id": "591c8aa5cade588b1ce045d26e5f2a162c52486262bd2d7abcb7fa18247e17ec",
  "head_block_num": 11895594,
  "last_irreversible_block_num": 11895545,
  "last_irreversible_block_id": "00b582f99d7002d43f26b29af7f2914545767e35020fd7283acfdea436b01e50",
  "head_block_id": "00b5832a6fab129098c6dfa18f1d6c836af29789e5a7dd3292cb2bc392798b23",
  "head_block_time": "2020-11-03T04:57:00.000",
  "head_block_producer": "4qvigkm1u4dt",
  "virtual_block_cpu_limit": 1400000000,
  "virtual_block_net_limit": 1048576000,
  "block_cpu_limit": 1399900,
  "block_net_limit": 1048576,
  "server_version_string": "v2.1.1"
}
```

Before continuing, your cyberway node must be fully synchronized to the blockchain.


## (Optional) Recovery cyberway blockchain from shapshot

```
cd cyberway.launch && mkdir snapshot && cd snapshot
wget https://download.cyberway.io/snapshot-20201102-v2.1.1.tar
tar xvf snapshot-20201102-v2.1.1.tar
rm snapshot-20201102-v2.1.1.tar
cd ..

sudo docker stop -t 200 nodeosd
sudo ./start_full_node.sh down
sudo docker volume rm cyberway-mongodb-data cyberway-nodeos-data cyberway-queue cyberway-nats-data
sudo docker volume create cyberway-mongodb-data 
sudo docker volume create cyberway-nodeos-data 
sudo docker volume create cyberway-nats-data
sudo docker volume create --name=cyberway-queue
sudo docker run --rm -ti -v `readlink -f snapshot`:/host:ro -v cyberway-nodeos-data:/data:rw cyberway/cyberway:v2.1.1 tar -xPvf /host/nodeos.tar.bz2
sudo docker run --rm -ti -v `readlink -f snapshot`:/host:ro -v cyberway-mongodb-data:/data:rw cyberway/cyberway:v2.1.1 tar -xPvf /host/mongodb.tar.bz2
sudo docker run --rm -ti -v `readlink -f snapshot`:/host:ro -v cyberway-nats-data:/data:rw cyberway/cyberway:v2.1.1 tar -xPvf /host/nats.tar.bz2
sudo ./start_full_node.sh up
```

```
git clone https://github.com/lexansoft/stihi-backend-1.0/
cd stihi-backend-1.0/
docker run -it  --rm -v ${PWD}:/build -w /build golang:1.14 bash
```

## Build stihi-backend

```
git clone https://github.com/lexansoft/stihi-backend-1.0.git
cd stihi-backend-1.0
docker run -it  --rm -v ${PWD}:/build -w /build golang:1.14 go build
```

Binary file `stihi-backend` will be exists on current directory 

```
./stihi-backend -h
Usage of ./stihi-backend:
  -config string
    	Application config file name
  -db_config string
    	Db config file name
  -mongo_db_config string
    	MongoDb config file name
  -pid string
    	Pid file name (default "/var/tmp/stihi_backend_cyberway.pid")
  -pidfile string
    	If specified, write pid to file.
  -redis_config string
    	Redis config file name
```
