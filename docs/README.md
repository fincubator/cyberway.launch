
# Install Cyberway full node on debian 10

System requirements: 
- Debian 10
- Docker 
- Docker compose

Install system dependencies:
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

**Install Cyberway full node on debian 10.**

```
git clone https://github.com/cyberway/cyberway.launch.git
./start_full_node.sh
```

Ensure that all containers have started

```
docker ps
```
Output
```

```

Check sync status with [api](https://docs.cyberway.io/software_manuals/api_reference/nodeos_chain_api#get_info)

```
curl --request POST --data '' http://127.0.0.1:8888/v1/chain/get_info| jq
``` 

Before continuing, your cyberway node must be fully synchronized to the blockchain.


## (Optional) Recovery cyberway blockchain from shapshot**

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