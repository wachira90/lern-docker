To **disable IPv6** for a container when running `docker run`, you can use the `--sysctl` option to turn off IPv6 at the container’s network stack level.

Here’s the syntax:

```bash
docker run --sysctl net.ipv6.conf.all.disable_ipv6=1 your_image_name
```

### Example:

```bash
docker run -d \
  --name my_container \
  --sysctl net.ipv6.conf.all.disable_ipv6=1 \
  nginx:latest
```

### Explanation:

* `--sysctl net.ipv6.conf.all.disable_ipv6=1`:
  Disables IPv6 within the container network namespace.
* This affects **only the container**, not the host.
* Works on **bridge networks** and **custom user-defined networks**.

---

If you want to **disable IPv6 globally** for all Docker containers, edit `/etc/docker/daemon.json` and add:

```json
{
  "ipv6": false
}
```

Then restart Docker:

```bash
sudo systemctl restart docker
```

Would you like me to include the command for a specific Docker network (e.g., bridge or custom network)?
