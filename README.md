# Corda on Docker

## 1. nstall Docker in Ubuntu
```
$ sudo apt install -y docker.io
```
Verify if docker is installed successfully by running `sudo docker` to check infomation.

**Docker 17.09 and up is required for this Dockerfile.**

Docker configuration files to create and spin up Docker images for a few Corda Nodes (PartyA/PartyB), Networkmap, Notary and one CordApp.

The docker image is based on Alpine/OpenJDK (https://hub.docker.com/_/openjdk/)


## 2. Download network-bootstrapper-corda-3.0.jar in project working directory, refer to the chapter [corda network](https://docs.corda.net/setting-up-a-corda-network.html?highlight=bootstrap#whitelisting-contracts)
```
$ curl -O http://downloads.corda.net/network-bootstrapper-corda-3.0.jar

```

## 3. Bootstrapping network for corda
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

## 4. Usage (automatic way - using docker compose)
* `cd ..` to working directory, Check Dockerfile and docker-compose.yml (e.g. to adjust version or exposed ports)
* `docker-compose build` - to build base Corda images for Corda Node/Networkmap/Notary
* `docker-compose up` - to spin up all Corda containers (Nodes + Networkmap + Notary)
* `docker exec -it BMBS /bin/sh` - to log in to one of the running Node containers

## Corda configuration
At the moment java options are put into **corda_docker.env**. All the others are in Dockerfile/docker_compose.yml

## Use installed CordApps
Post to issue a vehicle :

```
POST http://localhost:10007/api/smartvin/vehicles
{
  "VIN": "1HGBH41JXMN109187",
  "issuer": "BMBS", // only BMBS is legal entity here because there is only one node.
  "msrp": "",
  "titleDoc": "",
  "gpsLocation": "",
  "manufacturer": "",
  "baumuster": "",
  "body": "",
  "engine": "",
  "steering": "",
  "factory": ""
}
```

## Issues

If you get the following message: `<containername> exited with code 137` it's likely because the Linux OOM killer is getting triggered inside of running Docker instance.
Please adjust memory size for Docker environment:
On MacOS: https://docs.docker.com/docker-for-mac/#memory
On Windows: https://docs.docker.com/docker-for-windows/#advanced
