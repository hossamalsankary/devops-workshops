


### 🎯 Objectives


### 📝 Steps

## instll docker 

```bash 
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh ./get-docker.sh --dry-run
```

```bash

# 1️⃣ Confirm engine

docker version

docker info | head

  

# running container  for just testing 
docker run hello-world

  
# add process to the container 
docker run -it ubuntu  /bin/sleep 1000


# 3️⃣ Long‑running service

docker run -d --name web -p 8080:80 nginx:latest

  

# 4️⃣ Inspect & explore
docker ps # running containers

# live resource usage
docker stats 

docker logs web --tail 20

# shell inside the container
docker exec -it web bash 

docker inspect web | jq '.[0].NetworkSettings.IPAddress'



```diff 
docker run -d --name api --memory=512m --memory-swap=1g     --memory-reservation=256m        --cpus="1.5"       myimage:latest

```

# 5️⃣ Clean‑up

docker stop web && docker rm web

docker system prune -f # free disk space

```
```bash 
Usage:  docker exec [OPTIONS] CONTAINER COMMAND [ARG...]

Execute a command in a running container

Aliases:
  docker container exec, docker exec

Options:
  -d, --detach               Detached mode: run command in the background
      --detach-keys string   Override the key sequence for detaching a container
  -e, --env list             Set environment variables
      --env-file list        Read in a file of environment variables
  -i, --interactive          Keep STDIN open even if not attached
      --privileged           Give extended privileges to the command
  -t, --tty                  Allocate a pseudo-TTY
  -u, --user string          Username or UID (format: "<name|uid>[:<group|gid>]")
  -w, --workdir string       Working directory inside the container
```