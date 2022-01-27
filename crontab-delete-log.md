# Crontab delete log

## clear docker log

````
5 4 * * * /usr/bin/find /var/lib/docker/containers/ -name "*.log" | while read filename; do truncate -s 10k "$filename"; done;
````
