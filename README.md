#### pcdocker
#####Chapter 8. Sharing Data with Containers
######Sharing data between containers
It can be transitive
```
sudo docker run –it --volumes-from datavol ubuntu:latest /bin/bash
sudo docker run --name vol1 --volumes-from datavol busybox:latest /bin/true
sudo docker run --name vol2 --volumes-from vol1 busybox:latest /bin/true
```
######Avoiding common pitfalls
```
FROM ubuntu:14.04
VOLUME /MountPointDemo
RUN date > /MountPointDemo/date.txt
RUN cat /MountPointDemo/date.txt
```
this will fail. never use a data volume as storage during the build process.
#####Chapter 9. Docker Machine
######
```
 docker-machine create \
   --driver amazonec2 \
   --amazonec2-access-key <aws_access_key> \
   --amazonec2-secret-key <aws_secret_key> \
   --amazonec2-vpc-id <vpc_id> \
   --amazonec2-subnet-id <subnet_id> \
   --amazonec2-zone <zone> \
     <name>
```
######Docker Machine commands
```
docker-machine scp host1:/file host2:/file
```
get url
```
docker-machine url <name>
```
#####
######Docker Swarm usage
```
docker-machine create -d virtualbox swarm
eval $(docker-machine env swarm)
docker run --rm swarm create   //generate token
docker-machine create -d virtualbox --swarm --swarm-master --swarm-discovery token:// swarm-master
eval $(docker-machine env swarm-master)
docker-machine create -d virtualbox --swarm --swarm-discovery token:// swarm-node1
docker machine ls
docker run --rm swarm list token://
```
managing s cluster
```
docker -H tcp://192.   2376 info
docker -H tcp://192.   2376 ps
```
docker run manage swarm
```
docker run --rm swarm manage -H tcp://192.168.99.104:2376 token://
```
#####Chapter 12. Testing with Docker
######test code inside docker
```
# Base image is python
FROM python:latest

# Author: Dr. Peter
MAINTAINER Dr. Peter <peterindia@gmail.com>

# Install redis driver for python and the redis mock
RUN pip install redis && pip install mockredispy

# Copy the test and source to the Docker image
ADD src/ /src/

# Change the working directory to /src/
WORKDIR /src/

# Make unittest as the default execution
ENTRYPOINT python3 -m unittest
```
build and run
```
sudo docker build -t hit_unittest .
sudo docker run --rm -it hit_unittest .
```
######Integrating Docker(Jenkins) 2.9
port=8080
```
wget -q -O - https://jenkins-ci.org/debian/jenkins-ci.org.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins-ci.org/debian binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt-get update
sudo apt-get install jenkins
service jenkins start
```
get password to unlock:
```
cat /var/lib/jenkins/secrets/initialAdminPassword
```
add user jenkins to docker group
```
gpasswd -a jenkins docker
service jenkins restart
```

visudo
```
# User privilege specification
root    ALL=(ALL:ALL) ALL
jenkins ALL=(ALL) NOPASSWD:ALL
```
for build step ,use sudo.
#####Chapter 13. Debugging Containers
######Process level isolation for Docker containers
```
docker run -it --rm ubuntu /bin/bash
docker inspect --format "{{.State.Pid}}" 12345
ps -pf 123
cat -v /proc/123/environ
```
