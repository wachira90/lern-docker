Use Docker’s **bridge network** driver with the `--subnet` (and optionally `--gateway`) flags.

### Basic command

```bash
docker network create \
  --driver bridge \
  --subnet 172.25.0.0/16 \
  my_bridge_net
```

### With a specific gateway

```bash
docker network create \
  --driver bridge \
  --subnet 172.25.0.0/16 \
  --gateway 172.25.0.1 \
  my_bridge_net
```

### With IP range for containers (optional)

Limits the IPs Docker assigns to containers:

```bash
docker network create \
  --driver bridge \
  --subnet 172.25.0.0/16 \
  --ip-range 172.25.5.0/24 \
  --gateway 172.25.0.1 \
  my_bridge_net
```

### Assign a static IP to a container on that network

```bash
docker run -d \
  --name app1 \
  --network my_bridge_net \
  --ip 172.25.5.10 \
  nginx
```

### Verify the network

```bash
docker network inspect my_bridge_net
```

⚠️ **Notes**

* Make sure the subnet does **not overlap** with existing Docker or host networks.
* Static IP assignment works only on **user-defined bridge networks**, not the default `bridge`.

If you want this configured via **docker-compose** or need IPv6 as well, tell me your setup.
