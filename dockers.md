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



