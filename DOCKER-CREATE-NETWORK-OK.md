# **Docker network with a specific subnet** 

and then run a container using that network, follow these steps:

---

## ✅ 1. Create a Docker Network with Specific Subnet

Use the `docker network create` command with `--subnet`:

```bash
docker network create \
  --driver bridge \
  --subnet 192.168.100.0/24 \
  my_custom_network
```

### Explanation:

* `--driver bridge` → Default driver for standalone containers
* `--subnet 192.168.100.0/24` → Your custom subnet
* `my_custom_network` → Network name

---

## ✅ 2. Run a Container Using That Network

### Option A: Auto-assign IP from subnet

```bash
docker run -d \
  --name my_container \
  --network my_custom_network \
  nginx
```

Docker will automatically assign an IP from `192.168.100.0/24`.

---

### Option B: Assign a Specific IP Address

If you want a fixed IP:

```bash
docker run -d \
  --name my_container \
  --network my_custom_network \
  --ip 192.168.100.10 \
  nginx
```

⚠️ The IP must:

* Be inside the subnet
* Not already in use
* Not be the gateway address (usually `.1`)

---

## ✅ 3. Verify Network

### Inspect network:

```bash
docker network inspect my_custom_network
```

### Check container IP:

```bash
docker inspect my_container | grep IPAddress
```

---

## ✅ Optional: Specify Gateway Manually

```bash
docker network create \
  --driver bridge \
  --subnet 192.168.100.0/24 \
  --gateway 192.168.100.1 \
  my_custom_network
```

---

## 🔎 Example Full Workflow

```bash
docker network create --driver bridge --subnet 10.10.0.0/16 mynet

docker run -d --name web1 --network mynet --ip 10.10.0.10 nginx
```

