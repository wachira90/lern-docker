# install docker ubuntu 20.04

````
apt update

apt install apt-transport-https ca-certificates curl software-properties-common -y

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"

apt update

apt install docker-ce -y

systemctl start docker

docker run hello-world
````
