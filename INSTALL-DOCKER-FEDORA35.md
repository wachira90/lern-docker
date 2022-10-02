# INSTALL_DOCKER_FEDORA

````
sudo dnf remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine
````


````
curl -fsSL https://get.docker.com -o get-docker.sh

DRY_RUN=1 sh ./get-docker.sh

sh ./get-docker.sh
````


````
[root@fedora-ansible ~]# curl -fsSL https://get.docker.com -o get-docker.sh
[root@fedora-ansible ~]# DRY_RUN=1 sh ./get-docker.sh
# Executing docker install script, commit: 4f282167c425347a931ccfd95cc91fab041d414f
dnf install -y -q dnf-plugins-core
dnf config-manager --add-repo https://download.docker.com/linux/fedora/docker-ce.repo
dnf makecache
dnf install -y -q docker-ce docker-ce-cli containerd.io docker-scan-plugin docker-compose-plugin docker-ce-rootless-extras
[root@fedora-ansible ~]# sh ./get-docker.sh
# Executing docker install script, commit: 4f282167c425347a931ccfd95cc91fab041d414f
+ sh -c 'dnf install -y -q dnf-plugins-core'
+ sh -c 'dnf config-manager --add-repo https://download.docker.com/linux/fedora/docker-ce.repo'
Adding repo from: https://download.docker.com/linux/fedora/docker-ce.repo
+ '[' stable '!=' stable ']'
+ sh -c 'dnf makecache'
Docker CE Stable - x86_64                                                                                                        25 kB/s |  15 kB     00:00
Fedora 35 - x86_64                                                                                                              5.7 kB/s | 5.3 kB     00:00
Fedora 35 openh264 (From Cisco) - x86_64                                                                                        484  B/s | 989  B     00:02
Fedora Modular 35 - x86_64                                                                                                      9.1 kB/s | 5.2 kB     00:00
Fedora 35 - x86_64 - Updates                                                                                                     11 kB/s | 5.4 kB     00:00
Fedora Modular 35 - x86_64 - Updates                                                                                            3.3 kB/s | 5.1 kB     00:01
Metadata cache created.
+ sh -c 'dnf install -y -q docker-ce docker-ce-cli containerd.io docker-scan-plugin docker-compose-plugin docker-ce-rootless-extras'
Importing GPG key 0x621E9F35:
 Userid     : "Docker Release (CE rpm) <docker@docker.com>"
 Fingerprint: 060A 61C5 1B55 8A7F 742B 77AA C52F EB6B 621E 9F35
 From       : https://download.docker.com/linux/fedora/gpg

Installed:
  container-selinux-2:2.169.0-1.fc35.noarch         containerd.io-1.6.8-3.1.fc35.x86_64                      docker-ce-3:20.10.18-3.fc35.x86_64
  docker-ce-cli-1:20.10.18-3.fc35.x86_64            docker-ce-rootless-extras-20.10.18-3.fc35.x86_64         docker-compose-plugin-2.10.2-3.fc35.x86_64
  docker-scan-plugin-0.17.0-3.fc35.x86_64           fuse-overlayfs-1.9-1.fc35.x86_64                         libcgroup-2.0-3.fc35.x86_64
  slirp4netns-1.1.12-2.fc35.x86_64
````


````
To run Docker as a non-privileged user, consider setting up the
Docker daemon in rootless mode for your user:

    dockerd-rootless-setuptool.sh install

Visit https://docs.docker.com/go/rootless/ to learn about rootless mode.


To run the Docker daemon as a fully privileged service, but granting non-root
users access, refer to https://docs.docker.com/go/daemon-access/

WARNING: Access to the remote API on a privileged Docker daemon is equivalent
         to root access on the host. Refer to the 'Docker daemon attack surface'
         documentation for details: https://docs.docker.com/go/attack-surface/
````


````
[root@fedora-ansible ~]# docker ps
Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?
[root@fedora-ansible ~]# systemctl status docker
````



