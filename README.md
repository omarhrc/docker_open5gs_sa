# docker_open5gs_sa
Docker files to build and run open5gs in a docker

## Tested Setup

Docker host machine

- Ubuntu 18.04 and 20.04

UERANSIM (gNB + UE) simulator

## Build and Execution Instructions

* Mandatory requirements:
	* [docker-ce](https://docs.docker.com/install/linux/docker-ce/ubuntu)
	* [docker-compose](https://docs.docker.com/compose)


Clone repository and build base docker image of open5gs, ueransim

```
git clone https://github.com/omarhrc/docker_open5gs_sa
cd docker_open5gs_sa/base
docker build --no-cache --force-rm -t docker_open5gs_sa .

cd ../ueransim
docker build --no-cache --force-rm -t docker_ueransim .
```

### Build and Run using docker-compose

```
cd ..
set -a
source .env
docker-compose build --no-cache
docker-compose up

# UERANSIM gNB
docker-compose -f nr-gnb.yaml up -d && docker attach nr_gnb

# UERANSIM NR-UE
docker-compose -f nr-ue.yaml up -d && docker attach nr_ue
```

## Configuration

For the quick run (eNB/gNB, CN in same docker network), edit only the following parameters in .env as per your setup

```
MCC
MNC
TEST_NETWORK --> Change this only if it clashes with the internal network at your home/office
DOCKER_HOST_IP --> This is the IP address of the host running your docker setup
SGWU_ADVERTISE_IP --> Change this to value of DOCKER_HOST_IP set above only if eNB/gNB is not running the same docker network/host
UPF_ADVERTISE_IP --> Change this to value of DOCKER_HOST_IP set above only if eNB/gNB is not running the same docker network/host
```

If eNB/gNB is NOT running in the same docker network/host as the host running the dockerized Core then follow the below additional steps

```

Under amf section in docker compose file (docker-compose.yaml), uncomment the following part
```
...
    # ports:
    #   - "38412:38412/sctp"
...
```

Uncomment the following part under upf section
```
...
    # ports:
    #   - "2152:2152/udp"
...
```

## Register a UE information

Open (http://<DOCKER_HOST_IP>:3000) in a web browser, where <DOCKER_HOST_IP> is the IP of the machine/VM running the open5gs containers. Login with following credentials
```
Username : admin
Password : 1423
```

Using Web UI, add a subscriber:
UE1_IMEI=356938035643803
UE1_IMEISV=4370816125816151
UE1_IMSI=001011234567895
UE1_KI=8baf473f2f8fd09487cccbd7097c6862
UE1_OP=11111111111111111111111111111111


## Not supported
- IPv6 usage in Docker

