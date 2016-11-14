Testing and Deploying Couchbase Mobile

Deploying to:
- Operating Systems / Virtual Machines
- Containers
- PaaS

recommended number of nodes is 3 in production :8091
recommended number of gateways is 2 :4984
1 load balancer in front of gateway
1 cb lite database running on device

Deploy stages for VM
- start VM
- provision cb server and sync gateway
- config changes
- scaling

(5 vms)
cb-server
sync-gateway1
sync-gateway2
sync-gateway3
load-balancer

using vagrant to "up" all the vms


Deploy stages for Containers (Docker)
- quick to start/restart
- portability
- isolation
- resource allocation

challenges:
- data persistence
	- native file system based on UnionFS, not appropriate for high I/Operating
	- mount volume, (data gets written onto host)
		- persists beyond container instance
	- host directory won't always be there, has to take volume with it
		- PetSets
- addistional networking complexity
	- workaround: host only networking, container runs directly on host's network interface
	- right way: use overlay networking
		- set it up with Weave (Hard)
		- set it up with Docker Swarm (easier)
	- CB Server is sensitive to IP addresses
		- configure CB Server with stable routable DNS names
- microservice overhead

Deploy for PaaS (AWS, Azure, Google Container Engine, Docker Cloud)
- Couchbase Images on DockerHub - officially supported
	- server
	- sync-gateway

have documentation, on how to load balance ngnix for web sockets and long polling
- cb lite, get latest changes have web sockets mode
	- poses some challenges that it doesn't close the connection