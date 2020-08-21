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
