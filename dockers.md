# Docker Basic Commands

What is the version of Docker Server Engine running on the Host?

```
docker version
```
How many containers are running on this host?
```
docker ps -q | wc -l
```

Run a container using the redis image
NOTE: Run the container in detached mode if you don't want the container to take over your current terminal. This allows your terminal to remain free for other commands, or you can open a new terminal to run the command.
```
docker run -d --name redis-server redis
```
Stop the container you just created
```
docker stop redis-server
```
How many containers are PRESENT on the host now?
Including both Running and Not Running ones
```
docker ps -a -q | wc -l
```
What is the image used to run the nginx-1 container?
```
docker inspect --format='{{.Config.Image}}' nginx-1
```
What is the name of the container created using the ubuntu image?
```
docker ps -a --filter ancestor=ubuntu --format '{{.Names}}'
```
What is the ID of the container that uses the alpine image and is not running?
```
docker ps -a --filter "ancestor=alpine" --filter "status=exited" --format "{{.ID}}"
```
What is the state of the stopped alpine container?
```
docker inspect --format='{{.State.Status}}' <container_id_or_name>
```
Delete all containers from the Docker Host.
Both Running and Not Running ones. Remember you may have to stop containers before deleting them.
primo
```
docker stop $(docker ps -q)
```
secondo
```
docker rm $(docker ps -a -q)
```
Delete the ubuntu Image.
```
docker rmi -f ubuntu
```
You are required to pull a docker image which will be used to run a container later. Pull the image nginx:1.14-alpine
Only pull the image, do not create a container
```
docker pull nginx:1.14-alpine
```
Run a container with the nginx:1.14-alpine image and name it webapp
```
docker run -d --name webapp nginx:1.14-alpine
```
Cleanup: Delete all images on the host
Remove containers as necessary
Primo
```
docker stop $(docker ps -q)
docker rm $(docker ps -a -q)
```
fino
```
docker rmi $(docker images -q)
```
# Docker Run
Let us first inspect the environment. How many containers are running on this host?
```
docker ps -q | wc -l
```
What is the image used by the container?
```
docker ps
```
How many unique ports are published on this container (count each port only once even if it appears for IPv4 and IPv6)?
```
docker inspect --format '{{range $p, $conf := .NetworkSettings.Ports}}{{$p}}{{"\n"}}{{end}}' nginx | cut -d'/' -f1 | sort -u | wc -l
```
Which of the following ports are mapped on the container side (i.e., exposed inside the container)?
```
{"80/tcp": {}, "443/tcp": {}}
```
Which of the below ports are published on Host?
```
docker inspect --format '{{json .NetworkSettings.Ports}}' <container_name_or_id>
```
Run an instance of kodekloud/simple-webapp:blue and name the container blue-app, mapping port 8080 on the container to port 38282 on the host
```
docker run -d --name blue-app -p 38282:8080 kodekloud/simple-webapp:blue
```

# Docker Environment Variables

Inspect the environment variables set on the running container and identify the value set to the APP_COLOR variable.
```
docker exec c5752e7d79b3 printenv APP_COLOR 
```
Run a container named blue-app using image kodekloud/simple-webapp and set the environment variable APP_COLOR to blue. Make the application available on port 38282 on the host. The application listens on port 8080.
```
docker run -d --name blue-app -p 38282:8080 -e APP_COLOR=blue kodekloud/simple-webapp
```
Deploy a mysql database using the mysql image and name it mysql-db.
Set the database password to use db_pass123. Lookup the mysql image on Docker Hub and identify the correct environment variable to use for setting the root password.
```
docker run -d --name mysql-db -e MYSQL_ROOT_PASSWORD=db_pass123 mysql
```

# Docker Images

How many images are available on this host?
```
docker images -q | sort -u | wc -l
```
What is the size of the ubuntu image?
```
docker images ubuntu --format "{{.Size}}"
```
We just pulled a new image. What is the tag on the newly pulled NGINX image?
```
docker images nginx --format "{{.Tag}}"
```
We just downloaded the code of an application. What is the base image used in the Dockerfile?
Inspect the Dockerfile in the webapp-color directory.
```
head -n 1 webapp-color/Dockerfile
```
To what location within the container is the application code copied to during a Docker build?
Inspect the Dockerfile in the webapp-color directory.
```
grep "^COPY" webapp-color/Dockerfile
```
When a container is created using the image built with this Dockerfile, what is the command used to RUN the application inside it.
Inspect the Dockerfile in the webapp-color directory.
```
grep -E '^(CMD|ENTRYPOINT)' webapp-color/Dockerfile
```
What port is the web application run within the container?
Inspect the Dockerfile in the webapp-color directory.
```
grep "^EXPOSE" webapp-color/Dockerfile
```
Build a docker image using the Dockerfile and name it webapp-color. No tag to be specified.
```
docker build -t webapp-color -f webapp-color/Dockerfile . 
```
Run an instance of the image webapp-color and publish port 8080 on the container to 8282 on the host.
```
docker run -d -p 8282:8080 webapp-color
```
What is the base Operating System used by the python:3.6 image?
If required, run an instance of the image to figure it out.
```
docker run --rm python:3.6 cat /etc/os-release
```
What is the approximate size of the webapp-color image?
```
docker images webapp-color --format "{{.Size}}"
```
Build a new smaller docker image by modifying the same Dockerfile and name it webapp-color and tag it lite.
Hint: Find a smaller base image for python:3.6. Make sure the final image is less than 150MB.
```
go to the directory where the file is stored, edit it using nano, then change the first line FROM python:3.6-alpine
```
Run an instance of the new image webapp-color:lite and publish port 8080 on the container to 8383 on the host.
```
docker run -d -p 8383:8080 webapp-color:lite
```
# Docker CMD & Entrypoint

What is the ENTRYPOINT configured on the mysql image?
```
docker inspect --format='{{json .Config.Entrypoint}}' mysql
```
What is the CMD configured on the wordpress image?
primo pull the docker image
```
docker pull wordpress
```
secondo
```
docker inspect --format='{{json .Config.Cmd}}' wordpress
```
What is the command run at startup when the ubuntu image is run?
```
docker inspect --format='ENTRYPOINT: {{json .Config.Entrypoint}}, CMD: {{json .Config.Cmd}}' ubuntu
```
Run an instance of the ubuntu image to run the sleep 1000 command at startup.
Run it in detached mode.
```
docker run -d ubuntu sleep 1000
```
# Docker Compose

First create a redis database container called redis, image redis:alpine.
```
docker run --name redis -d redis:alpine
```
Next, create a simple container called clickcounter with the image kodekloud/click-counter, link it to the redis container that we created in the previous task and then expose it on the host port 8085
The clickcounter app run on port 5000.
if you are unsure, check the hints section for the exact commands.
```
docker run -d --name=clickcounter --link redis:redis -p 8085:5000 kodekloud/click-counter
```
Let's clean up the actions carried out in previous steps. Delete the redis and the clickcounter containers.
```
docker stop redis clickcounter
```
```
docker rm redis clickcounter
```
Already done, don't you think dude?


Create a docker-compose.yml file under the directory /root/clickcounter. Once done, run docker-compose up.
The compose file should have the exact specification as follows -
redis service specification - Image name should be redis:alpine.
clickcounter service specification - Image name should be kodekloud/click-counter, app is run on port 5000 and expose it on the host port 8085 in the compose file.
primo
```
mkdir -p /root/clickcounter
```
Secondo
```
nano /root/clickcounter/docker-compose.yml
```
thirtio
```
version: '3'

services:
  redis:
    image: redis:alpine

  clickcounter:
    image: kodekloud/click-counter
    ports:
      - "8085:5000"
    depends_on:
      - redis

```
Fino
```
cd /root/clickcounter
docker-compose up -d
```
# Docker Storage
What location are the files related to the docker containers and images stored?
```
/var/lib/docker
```

Run a mysql container named mysql-db using the mysql image. Set database password to db_pass123
Note: Remember to run it in the detached mode.
```
docker run -d \
  --name mysql-db \
  -e MYSQL_ROOT_PASSWORD=db_pass123 \
  mysql
```
We have just written some data into the database. To view the information we wrote, run the get-data.sh script available in the /root directory. How many customers data have been written to the database?
Command: sh get-data.sh
firstly
```
docker exec -it mysql-db bash
```
secondly,
```
cd /root
sh get-data.sh
```
thirdly
```
SELECT * FROM customers;
```
The database crashed. Are you able to view the data now?
Use the same command to try and view data. Try to find the container.
```
NO
```
Run a mysql container again, but this time map a volume to the container so that the data stored by the container is stored at /opt/data on the host.
Use the same name : mysql-db and same password: db_pass123 as before. Mysql stores data at /var/lib/mysql inside the container
```
docker run -v /opt/data:/var/lib/mysql -d --name mysql-db -e MYSQL_ROOT_PASSWORD=db_pass123 mysql
```
Disaster strikes.. again! And the database crashed again. But this time we have the data stored at /opt/data directory. Re-deploy a new mysql instance using the same options as before.
Just run the same command as before. Here it is for your convenience: docker run -v /opt/data:/var/lib/mysql -d --name mysql-db -e MYSQL_ROOT_PASSWORD=db_pass123 mysql
```
docker run -v /opt/data:/var/lib/mysql -d --name mysql-db -e MYSQL_ROOT_PASSWORD=db_pass123 mysql
```



















