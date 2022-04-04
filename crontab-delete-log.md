# Crontab delete log

## clear docker log

````
5 4 * * * /usr/bin/find /var/lib/docker/containers/ -name "*.log" | while read filename; do truncate -s 0 "$filename"; done;
````

## check size
````
find /var/lib/docker/containers/ -name "*.log" | while read filename; do du -sh "$filename"; done;
````
