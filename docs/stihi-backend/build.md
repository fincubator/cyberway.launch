### Build

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