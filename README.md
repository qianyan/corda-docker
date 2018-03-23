# Corda on Docker

**Docker 17.09 and up is required for this Dockerfile.**

Docker configuration files to create and spin up Docker images for a few Corda Nodes (PartyA/PartyB), Networkmap, Notary and one CordApp.

The docker image is based on Alpine/OpenJDK (https://hub.docker.com/_/openjdk/)

## Usage (automatic way - using docker compose)

* Check Dockerfile and docker-compose.yml (e.g. to adjust version or exposed ports)
* `docker-compose build` - to build base Corda images for Corda Node/Networkmap/Notary
* `docker-compose up` - to spin up all Corda containers (Nodes + Networkmap + Notary)
* `docker exec -it PartyA /bin/sh` - to log in to one of the running Node containers
* `docker exec -it NetworkMapAndNotary /bin/sh` - to log in to the running NetworkmapAndNotary container

## Corda configuration
At the moment java options are put into **corda_docker.env**. All the others are in Dockerfile/docker_compose.yml

If you need to modify Corda parameters (node or/and networkmap) change their configuration file(s) and restart container:
### For Corda node (i.e. PartyA):
* modify `nodes/PartyA/node.conf`
* restart Corda node: `docker restart PartyA`
### For Networkmap node:
* modify `nodes/NetworkMapAndNotary/node.conf`
* restart Corda networkmapandnotary: `docker restart NetworkMapAndNotary`

## Show installed CordApps
In your web browser open:
* `http://localhost:10007` (for PartyA)
* `http://localhost:10010` (for PartyB)

etc

## Issues
If you get the following message: `<containername> exited with code 137` it's likely because the Linux OOM killer is getting triggered inside of running Docker instance.
Please adjust memory size for Docker environment:
On MacOS: https://docs.docker.com/docker-for-mac/#memory
On Windows: https://docs.docker.com/docker-for-windows/#advanced

## TODO
* add more to README.md (how to customise the build, potential manual spin up without docker compose) 
