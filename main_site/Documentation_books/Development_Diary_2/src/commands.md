# Commands


## Docker
- installation ---> install from Software Manager ... docker.io
- post-installation ---> 
  - sudo groupadd docker
  - sudo usermod -aG docker $USER

- docker images ---> Check which images I have locally
- docker ps     ---> show running containers ps :: processes
- docker ps --all ----> show all processes (both runing and closed)

- docker pull nginx:1.25.1         ---> download an image locally
- docker run --detach nginx:1.25.1  ---> run a container without letting the logs flood the current terminal
- docker run  nginx:1.25.           ---> run a container in the current terminal

- docker logs <container_ID_found_under_PS_comand>
- docker stop <container_ID_found_under_PS_comand>
- docker run --detach --publish 1080:80 nginx:1.25.1  ---> Bind a local port to a container port

- docker run --name web_server --detach --publish 80:80 nginx:1.25.1   ---> give a container a name
- docle

## Minikube
   - curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
  - sudo install minikube-linux-amd64 /usr/local/bin/minikube

- minikube start                                ---> chooses Qemu by default in my machine
- minikube start --driver=docker                ---> use docker as the runtime for minikube
- minikube config set driver docker             ---> make docker the default driver for minikube
- minikube kubectl -- get pods -A               ---> Install kubectl

