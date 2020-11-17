
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
