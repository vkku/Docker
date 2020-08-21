# Docker
```
cd $HOME
mkdir docker-class
cd docker-class
docker pull debian:latest
docker pull alpine:3.8
docker pull alpine:latest
docker pull httpd:2.4
docker pull golang:1.9.4
docker pull fedora:28
docker pull spkane/quantum-game:latest
docker pull spkane/outyet:1.9.4-small
docker pull spkane/train-os:latest
docker pull spkane/alpine-base:latest
git clone https://github.com/spkane/balance_game.git --config core.autocrlf=input
git clone https://github.com/spkane/outyet.git --config core.autocrlf=input
git clone https://github.com/spkane/docker-registry.git --config core.autocrlf=input
git clone https://github.com/spkane/docker-volumes-example.git --config core.autocrlf=input
wget https://raw.githubusercontent.com/spkane/train-os/master/default.json # optional
wget https://raw.githubusercontent.com/spkane/train-os/master/tcpdump.json # optional
alt to prev
wget https://raw.githubusercontent.com/spkane/train-os/master/tcpdump.json
```
https://gist.github.com/spkane/98ac0c87a019f9305c831966e4d5f0b8

```
docker-machine ip
usually 192.168.99.100
```
```
$ docker system prune -a
WARNING! This will remove:
  - all stopped containers
  - all networks not used by at least one container
  - all images without at least one container associated to them
  - all build cache
```

```
You can push a new image to this repository using the CLI
docker tag local-image:tagname new-repo:tagname
docker push new-repo:tagname
```
```
docker build -t vkku/balance_game .
docker push vkku/balance_game:latest
docker search vkku
```

```
docker run -d --rm --name balance_game -p 18081:80 vkku/balance_game:latest
-ti : for interactive machine
```

Layering and Imaging on each of ```DockerFile``` statements
```tar -tvf export.zip```
