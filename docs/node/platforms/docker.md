# Docker

To run last Ergo version in mainnet as a console application with logs printed to console:



## Docker

```
sudo docker run --rm -p 9030:9030 -p 127.0.0.1:9053:9053 -v /path/on/host/to/ergo/data:/home/ergo/.ergo ergoplatform/ergo --mainnet
```
This will connect to Ergo mainnet with default config and open port 9030 globally and 9053 locally on the host system. 

All data will be stored in your host directory `/path/on/host/to/ergo/data`.




## Docker Compose

```
version: "2"

services:
  node:
    image: ergoplatform/ergo
    container_name: ergo-mainnet
    ports:
      - 9053:9053
      - 9030:9030
    environment:
      - MAX_HEAP=3G
    volumes:
      - ~/.data/ergo/mainnet:/home/ergo/.ergo
      - ./myergo.conf:/etc/myergo.conf
    restart: unless-stopped
    command: --mainnet -c /etc/myergo.conf
```

Run the node with

```
docker-compose up -d
```

Follow the logs with
```
docker logs -f ergo-mainnet -n 200
```


## Running other versions

To run specific Ergo version `<VERSION>` as a service with custom config `/path/on/host/system/to/myergo.conf`:

```bash
    sudo docker run -d \
        -p 9030:9030 \
        -p 127.0.0.1:9053:9053 \
        -v /path/on/host/to/ergo/data:/home/ergo/.ergo \
        -v /path/on/host/system/to/myergo.conf:/etc/myergo.conf \
        -e MAX_HEAP=3G \
        ergoplatform/ergo:<VERSION> --<networkId> -c /etc/myergo.conf
```
Available versions can be found on [Ergo Docker image page](https://hub.docker.com/r/ergoplatform/ergo/tags), for example, `v4.0.23`.

This will connect to the Ergo mainnet or testnet following your configuration passed in `myergo.conf` and network flag `--<networkId>`. Every default config value would be overwritten with corresponding value in `myergo.conf`. `MAX_HEAP` variable can be used to control how much memory can the node consume.

This command also would store your data in `/path/on/host/to/ergo/data` on host system, and open ports `9030` (node communication) globally and `9053` (REST API) locally on host system. The `/path/on/host/to/ergo/data` directory must has `777` permissions or has owner/group numeric id equal to `9052` to be writable by container, as `ergo` user inside Docker image (please refer to [Dockerfile](Dockerfile)).

Ergo node works normally behind NAT, so you can keep closed your `9030` port, hence other nodes could not discover and connect to yours one, only your node could initiate connections.

It is also a good practice to keep closed REST API port `9053`, and connect to your node from inside another container in the same Docker network (this case not covered by this short quick start manual).