To auto-confirm the `docker builder prune` command, use the `-f` or `--force` flag:

```bash
docker builder prune -f
```

To remove **all** build cache (not just dangling), add `-a`:

```bash
docker builder prune -af
```
