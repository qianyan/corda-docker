# Corda on Docker

## 1. Install Docker in Ubuntu
```
$ sudo apt install -y docker.io
```
Verify if docker is installed successfully by running `sudo docker` to check information.

**Docker 17.09 and up is required for this Dockerfile.**

Docker configuration files to create and spin up Docker images for a few Corda Nodes.

The docker image is based on Alpine/OpenJDK (https://hub.docker.com/_/openjdk/)

## 2. Bootstrapping network for corda
The nodes see each other using the network map. This is a collection of statically signed node-info files, one for each node in the network. Most production deployments will use a highly available, secure distribution of the network map via HTTP.

In addition to the network map, all the nodes on a network must use the same set of network parameters. These are a set of constants which guarantee interoperability between nodes. The HTTP network map distributes the network parameters which the node downloads automatically

```
$ java -jar network-bootstrapper-corda-3.0.jar nodes
$ cd nodes
$ cp -r notary-dockerfile/ Notary/
```
the command will generate nodes' directory for us as below:

```
$ tree -L 2 nodes
nodes
├── BMBS
│   ├── additional-node-infos
│   ├── certificates
│   ├── network-parameters
│   └── node.conf
├── BMBS.conf
├── Notary
│   ├── Dockerfile
│   ├── additional-node-infos
│   ├── certificates
│   ├── network-parameters
│   ├── node.conf
│   └── run-corda.sh
├── Notary.conf
├── notary-dockerfile
│   ├── Dockerfile
│   └── run-corda.sh
└── whitelist.txt
```

## 3. Using docker compose
*  Check Dockerfile and docker-compose.yml (e.g. to adjust version or exposed ports)
* `docker-compose build` - to build base Corda images for Corda Node
* `docker-compose up` - to spin up all Corda containers
* `docker exec -it BMBS /bin/sh` - to log in to one of the running Node containers

## Corda configuration
At the moment java options are put into **corda_docker.env**. All the others are in Dockerfile/docker_compose.yml

## Use installed CordApps

### API
Issue a vehicle

```
curl -X POST \
  http://localhost:10007/api/smartvin/vehicles \
  -d '{
  "VIN": "1HGBH41JXMN109187",
  "issuer": "BMBS",
  "msrp": "",
  "titleDoc": "",
  "gpsLocation": "",
  "manufacturer": "",
  "baumuster": "",
  "body": "",
  "engine": "",
  "steering": "",
  "factory": ""
}'
```

Get the vehicle by VIN
```
curl -X GET http://localhost:10007/api/smartvin/vehicles/1HGBH41JXMN109187
```

Get all issued vehicles

```
curl -X GET http://localhost:10007/api/smartvin/vehicles
```

## Issues

If you get the following message: `<containername> exited with code 137` it's likely because the Linux OOM killer is getting triggered inside of running Docker instance.
Please adjust memory size for Docker environment:

On MacOS: https://docs.docker.com/docker-for-mac/#memory

On Windows: https://docs.docker.com/docker-for-windows/#advanced
