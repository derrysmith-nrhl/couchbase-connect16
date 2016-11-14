# Clustering

> Create 2 or more docker containers with couchbase

docker run -d --name db1 couchbase:4.5.1
docker run -d --name db2 couchbase:4.5.1
docker run -d --name db3 couchbase:4.5.1

```
docker ps
docker exec -it db2 /bin/bash

```

## get IP
```
/sbin/ifconfig
```

couchbase-cli
	server-add -c 17217.0.2:8091
	--server-add=172.17.0.3:8091
	--server-add-username=Administrator
	--server-add-password=***
	