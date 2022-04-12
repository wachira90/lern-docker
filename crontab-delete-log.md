# Crontab delete log

## clear docker log on command ( crontab -e ) 

````
5 4 * * * /usr/bin/find /var/lib/docker/containers/ -name "*.log" | while read filename; do truncate -s 0 "$filename"; done;
````

## clear docker log on command ( nano /etc/crontab ) 

````
5 4 * * * root /usr/bin/find /var/lib/docker/containers/ -name "*.log" | while read filename; do truncate -s 0 "$filename"; done;
````


## check size
````
find /var/lib/docker/containers/ -name "*.log" | while read filename; do du -sh "$filename"; done;
````
