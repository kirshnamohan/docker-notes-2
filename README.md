# Docker Notes

## Table of Contents

- [Hypervisor-based Virtualization](#hypervisor-based-virtualization)
- [Container Based](#container-based)
- [Docker Client](#docker-client)
- [Docker Concepts](#docker-concepts)
- [Commands](#commands)
- [Docker Ports & Logging](#docker-ports--logging)
- [Docker Images](#docker-images)
- [Ways to Build an Image](#ways-to-build-an-image)
- [Dockerfile](#dockerfile)
- [Chain Run Command](#chain-run-command)
- [Sort Multi-Lines Arguments Alphanumerically](#sort-multi-lines-arguments-alphanumerically)
- [CMD](#cmd)
- [Push Images to DockerHub](#push-images-to-dockerhub)
- [Containerization An Application](#containerize-an-application)
- [Docker Container Links](#docker-container-links)
- [Docker Compose](#docker-compose)
- [Docker Networking](#docker-networking)
- [Unit Tests in Containers](#unit-tests-in-containers)
- [Continuous Integration](#continuous-integration)
- [Running in Production](#running-in-production)
- [Kubernetes](#kubernetes)

## Hypervisor-based Virtualization

- Physical Server
- Host OS
- Hypervisor
  - Debian
    - Libs/bins
  - Ubuntu
    - Libs/bins

### Benefits

- Cost effective
- Easy to Scale

### Limitations

- Kernel Resource Duplication
- Application Portability Issue

## Container-based

Containers share the same kernel resource.

### Runtime Isolation

- Uses only the minimal requirements
- Allows for fast deployment
- Guaranteed Portability

Example:

- Application A - Container A
  - JRE 8
  - JVM
- Application B - Container B
  - JRE 7

## Docker Client

docker build - Docker daemon
docker pull - Docker daemon
docker run - Docker daemon

Docker Daemon -> Registry (Redis) -> Redis Image
Image -> Container

Docker Daemon

Docker engine - run on remote docker daemon or local.

## Docker Concepts

### Images

- Images are read as templates used to create containers.
- Images are created with the docker build command, either by us or by other docker users.
- Images are composed of layers of other images.
- Images are stored in Docker registry.

### Containers

- If an image is a class, then a container is an instance of a class - a runtime object.
- Containers are lightweight and portable encapsulations of an environment in which to run programs.
- Containers are created from images. Inside a container, it has all the binaries and dependencies needed to run the application.

### Registries and Repositories

- A registry is where we store our images.
- You can host your own registry, or you can use Docker public registry which is called DockerHub.
- Inside a registry, images are stored repositories.
- Docker repository is a collection of different docker images with the same name, that have different tags, each tag usually means a different version of the image.
- DockerHub is a popular service for repositories.
- Official names are reviewed by DockerHub.
- If you don't specify a tag, it defaults to latest.
- Docker will use the local image first if it's available, otherwise it will download it from the network.

## Commands

To list available images use:

```
docker images
```

To use an image to echo "hello world":

```
docker run image echo "hello world"
```

Use -i -t for interactive.

To view currently running Docker containers use:

```
docker ps
```

To use all previous ran containers use -a.

To remove a container use:

```
docker run image --rm
```

To name a container use --name.

Use -d to run in detached mode.

To view low-level docker container information use:

```
docker inspect
```

## Docker Ports & Logging

To run docker on a specific port use:

```
docker run -it -p host_port:container_port tomcat:8.0
```

To view logs from a container use:

```
docker logs container_id
```

## Docker Images

Docker images contain a series of layers. To see the layers an image has use:

```
docker history image
```

1. All changes made to a running containers will be written to a writable layer.
2. When the container is deleted, the writable layer is also deleted, but the underlying image remains unchanged.
3. Multiple containers can share access to the same underlying image.

## Ways to Build an Image

1. Create a Dockerfile.
2. Commit changes made to a Docker container.

To commit your Docker changes use:

```
docker commit container_id repository_name:tag
```

## Dockerfile

To install an image use:

```
FROM image
```

To run a command use:

```
RUN apt-get update
```

To build a container from a Dockerfile use:

```
docker build
```

- Optionally you can specify the build context.
- When the build starts the build context gets saved to a tarball.
- The tarball is then transferred to the daemon.

## Chain Run Command

- Each run command executes on the top writeable layer and then commits the change.
- The new image is then used for the next step in the Dockerfile.
- Each run command creates a new image layer.
- Its recommended to chain the run commands to minimize the new image layers.

## Sort Multi-Lines Arguments Alphanumerically

- This will help you avoid duplication.

## CMD

- Specifies which command to run when the container starts.
- If we don't specify the CMD command in the Dockerfile, Docker will use the default command from the image.
- The CMD instruction doesn't run when building the image.
- You can specify the command in either exec form or shell form.

## Push Images to DockerHub

- List docker images by using:

```
docker images
```

- In order to push the image you must first link the image to a Docker account.
- Name the repository something like username/repo
- To push a repo use:

```
docker tag hash_id username/repo
```

## Latest Tag

- Docker will use latest as a default tag when no tag is provided.
- A lot of repositories use it to tag the most up-to-date stable image, however,
this is still only a convention and is entirely not enforced.
- Images which are tagged latest will not be updated automatically when a newer version of the image is pushed to the repository.
- Avoid using latest tag.

### Login

To login use:

```
docker login --username=dsims
```

To push your repo use:

```
docker push username/repo
```

## Containerize An Application

To build the container use:

```
docker build -t dockerapp:v1.0 .
```

To run docker container use:

```
docker run -d -p 5000:5000 image_id
```

To find out where the docker container is running use:

```
docker-machine ls
```

To run a command inside a container use:

```
docker exec -it image_id bash
```

## Docker Container Links

Allows containers to communicate with each other. Requires a recipient container (i.e. Dockerapp) and a source container (i.e. Redis).

First run the container with:

```
docker run -d --name redis redis:3.2.0
```

Then run the Dockerapp container by using:

```
docker run -d -p 5000:5000 --link redis dockerapp:v0.3
```

Benefits of Docker Container Links:

- When you build an application with a micro-service architecture, it allows to run many
independent applications in different containers to connect with one another.
- Creates a secure tunnel between containers that doesn't need to expose any ports externally.

## Docker Compose

- Manual linking containers doesn't make sense when there's a lot of different containers (20+).
- Docker Network allows all services to connect to each other.
- Removes the burden of maintaining scripts for Docker orchestration.

To start docker services use:

```
docker-compose up
```

To view Docker compose logs use:

```
docker-compose logs
```

To stop Docker containers use:

```
docker-compose stop
```

To remove all Docker containers use:

```
docker-compose rm --all
```

To force a rebuild of an image use:

```
docker-compose build
```

## Docker Networking

Each container connects to a bridge network to connect to the host machine.

### Docker Network Types

- Closed Network / None Network
- Bridge Network
- Host Network
- Overlay Network

To see Docker networks use:

```
docker network ls
```

### None Network

It's an isolated container that has no connection to the outside world.

To run a closed network use:

```
docker run -d --net none image
```

- Provides the maximum level of network protection.
- Not a good choice if network or Internet connection is required.
- Suites well when the container requires maximum level of security.

### Bridge Network Interface

To create a network use:

```
docker network create --driver bridge my_bridge_network
```

To inspect a network use:

```
docker network inspect my_bridge_network
```

To disconnect from a network use:

```
docker network disconnect bridge container_3
```

- In a bridge network containers have access to 2 network interfaces (loopback and private).
- All containers in the same network can communicate with each other.
- Containers from different bridge networks can't connect with each other by default.

### Host Network

- Least protected network model, add container to host network.
- Containers hosted on host network have full access to host's interface.
- These containers are called open containers.
- No isolation.
- Host containers have best performance.

### Overlay Network

- Supports multi-host networks out-of-the-box.
- Require some pre-existing conditions before it can be created (running in swarm mode).

### Define Network in Compose

By default it sets a single network.

## Unit Tests in Containers

- Should test some functionality in a container without any outside resources.
- Should run quickly as possible to avoid being blocked.
- Spin up quick and provide a clean and isolated environment for unit tests.

To run a service inside of a container use:

```
docker-compose run dockerapp python test.py
```

Pros:
- A single image is used throughout development and production. Increases reliability.

Cons:
- Increases size of the image.

## Continuous Integration

- Isolated changes are immediately tested and reported when they're added to a larger code base.
- Provides rapid feedback so if a bug is introduced it will be identified as soon as possible.

### Configuring Circle CI

Set DockerHub credentials in Circle CI by going to Project > Settings > Build Settings > Environmental Variables.

Then set:

```
DOCKER_HUB_EMAIL
DOCKER_HUB_PWD
DOCKER_HUB_USER_ID
```

To their respective DockerHub credentials.

### Tag the Docker Images with Two Tags

1. Git commit hash of the source code.
2. Latest

## Running In Production

Concerns:

- There are some missing pieces about Docker around data persistence, networking, security, and identity management.
- The ecosystem to support monitoring and logging are not still fully ready yet.

A lot of companies are already using Docker in production.

### Why Run It In a VM?

- To address security concerns
- Hardware level isolation

Amazon EC2 still uses VMs internally

### Docker Machine

- Can provision new VMs
- Install Docker Engine
- Configure Docker Client

### Creating a New VM

To create a new Docker VM use:

```
docker-machine create --driver digitalocean --digitalocean-access-token tag docker-app-machine
```

To setup the Docker environment use:

```
docker-machine env docker-app-machine
```

Then to see the environment variable use:

```
eval $(docker-machine env docker-app-machine)
```

### Production deployment

```
docker-compose -f prod.yml up
```

### Extends keyword

- Allows for sharing of common configurations among different files or projects.
- Can be useful if you have several different environments that re-use most configuration options.

Use:

```
extends:
  file: common.yml
  service: redis
```

### Docker Swarm

- A tool that clusters many Docker Engines and scheduled containers.
- Decides which host to run the container based on scheduling methods.

Set Digital Ocean configuration information by using:

```
export DIGITALOCEAN_ACCESS_TOKEN=
export DIGITALOCEAN_PRIVATE_NETWORKING=true
export DIGITALOCEAN_IMAGE=
```

### Service Discovery

- Key component of most distributed systems and service oriented architectures.
- In the context of Docker Swarm, service discovery is about how the Swarm Manager keeps track of the state of all the nodes in a cluster.

### Deploy Docker App In a Swarm Cluster

1. Provision a consul machine and run machine as a key-value store for service discovery.
2. Provision a Docker swarm master node.
3. Provision a Docker swarm slave node.
4. Define the overlay network to support multi-host networking.
5. Deploy out Docker app services on the Swarm cluster via Docker compose.

To display the network configuration use:

```
docker-machine ssh consul ifconfig
```

To ping the private and public IPs use:

```
ping -c 1 $(docker-machine ssh consul 'ifconfig eth0 | grep "inet addr:" | cut -d: -f2 | cut -d" " -f1')
ping -c 1 $(docker-machine ssh consul 'ifconfig eth1 | grep "inet addr:" | cut -
```

To configure Docker client to connect to consul use:

```
eval $(docker-machine env consul)
```

To create a Swarm master node use:

```
docker-machine create -d digitalocean --swarm --swarm-master --swarm-discovery="consul://${KP_IP}:8500" --engine-opt="cluster-store=consul://${KP_IP}:8500" --engine-opt="cluster-advertise=eth1:2376" master
```

To create slave nodes use:

```
docker-machine create -d digitalocean --swarm --swarm-discovery="consul://${KP_IP}:8500" --engine-opt="cluster-store=consul://${KP_IP}:8500" --engine-opt="cluster-advertise=eth1:2376" slave
```

## Kubernetes

Is an open-source orchestration system for Docker containers.

- Lets you schedule containers on a cluster of machines
- You can run multiple containers on one machine
- You can run long running services (like web applications)
- Managed the state of these containers
  - Can start container on specific nodes
  - Will restart a container when it gets killed
  - Can move containers from one node to another node

- Instead of running a few docker containers manually. Kubernetes manages it for you.
- Clusters can start with one node until thousands of nodes
- Some other popular docker orchestrations are:
  - Docker Swarm
  - Mesos

Kubernetes:
  - On-premise
  - Public (AWS)
  - Hybrid: public & private

- Highly modular
- Open source
- Backed by Google

Docker Engine

- Docker runtime
- Software to run docker images

Docker Hub

- Online service to store and fetch docker images
- Also allows you to build docker images online

Kubernetes can be ran anywhere (except more integrations exists for AWS/GCE)

- Things like Volumes and External Load Balancers work only with supported Cloud Providers
- Minikube - run Kubernetes locally
- Kops - used to spin up highly available production cluster

### MiniKube

Starting MiniKube:

```
minikube start
```

MiniKube config:

```
cat ~/.kube/config
```

Expose minikube test:

```
kubectl expose deployment hello-minikube --type=NodePort
```

Run minikube service:

```
kubectl run hello-minikube --image=gcr.io/google_containers/echoserver:1.4 --port=8080
minikube service hello-minikube --url
```

Stop minikube:

```
minikube stop
```

### Kops

Virtual Machine setup:

```
vagrant init ubuntu/xenial64
```

Virtual Machine run:

```
vagrant up
```

Download Kops on Machine:

```
brew update && brew install kops
```

Install AWS CLI using pip. Create Kops user on AWS and give it Administrative Access.

Create a subdomain using Route53 for Kubernetes: (kubernetes.domain.com).

To create a kops cluster use:

```
kops create cluster --name=kubernetes.domain.com --state=s3://kobs-state-blah --zones=eu-west-1a --node-count=2 --node-size=t2.micro --master-size=t2.micro --dns-zone=kubernetes.domain.com
```

To configure the cluster use:

```
kops update cluster kubernetes.domain.com --yes
```

To edit the cluster use:

```
kops edit cluster kubernetes.domain.com
```

Or

```
kops edit cluster kubernetes.domain.com --state=s3://kops-state-blah
```

Docker/Kubernetes Remarks:

- You should only run one process in one container
  - Don't try to create one giant docker image for your app, split it up if necessary.
- All the data in the container is not preserved, when a container stops, all the changes within a container are lost.
  - You can preserve data, using volumes, which is covered later in this course

- A pod describes an application running on Kubernetes.
- A pod can contain one or more tightly coupled containers, that make up the app.
- Those apps can easily communicate with each other using their local port numbers.
- Our app only has one container.
- Create pod-helloworld.yml

```
apiVersion: v1
kind: Pod
metadata:
  name: nodehelloworld.example.com
  labels:
    app: helloworld
spec:
  containers:
  - name: k8s-demo
    image: my-image
  ports:
  - name: nodejs-port
  - containerPort: 3000
```

To create a pod-helloworld.yml with the pod definition use:

```
kubectl create -f k8s-demo/pod-helloworld.yml
```

Useful commands:

```
kubectl get pod - Get information about all running pods
kubectl describe pod <pod> - Describe one pod
kubectl expose pod <pod> --port=444 --name=frontend - Expose the port of a pod (creates a new service)
kubectl port-forward <pod> 8080 - Port forward the exposed post port to your local machine
kubectl attach <podname> -i - Attach to the pod
kubectl exec <pod> -- command - Execute a command on the pod
kubectl label pods <pod> mylabel=awesome - Add a new label to a pod
kubectl run -i --tty busybox --image=busybox --restart=Never -- sh - Run a shell in a pod - very useful for debugging
```

### Load Balancers

On AWS the load balancer will route the traffic to the correct pod in Kubernetes.

You can use haproxy or nginx load balancer in front of your cluster. Expose port directly.

To setup a service with a load balancer use:

```
apiVersion: v1
kind: Service
metadata:
  name: helloworld-service
spec:
  ports:
  - port: 80
    targetPort: nodejs-port
    protocol: TCP
  selector:
    app: helloworld
type: LoadBalancer
```

### Basics

Internet traffic is directed to a load balancer which then does a look-up on the iptables. IPTables then directs traffic to pods containing Docker containers. Kubelet/kube-proxy also does a look-up against iptables.

### Scaling

If your application is stateless then you can horizontally scale it.

- Stateless means that it doesn't write any local files or keep local sessions.
- All traditional databases (MySQL/Postgres) are stateful, they have database files that can't be split over multiple instances.

Most web applications can be made stateless.

- Session management needs to be done outside the container.
- Scaling in Kubernetes can be done using the Replication Controller.
- The replication controller will ensure a specified number of pod replicas will run at all time.
- A pods created with the replica controller will automatically be replaced if they fail, get deleted, or are terminated.
- Using the replication controller is also recommended if you just want to make sure 1 pod is always running, even after reboots
  - You can then run a plication controller with just 1 replica
  - This makes sure that the pod is always running

To replicate an app 2 times using the Replication controller:

```
apiVersion: v1
kind: ReplicationController
metadata:
  name: helloworld-controller
spec:
  replicas: 2
  selector:
    app: helloworld
  template:
    metadata:
      labels:
        app: helloworld
    spec:
      containers:
      - name: k8s-demo
        image: k8s-demo
        port:
        - containerPort: 3000
```

Via CLI you can use:

```
kubectl scale --replicas=4 -f config.yml
```

Replication Set

- Replica Set is the next-generation Replication Controller
- It supports a new selector that can do selection based on filtering according to a set of values
  - "environment" can be "dev" or "qa"
  - not based on equality

- Replica Set is used by Deployment object

- A deployment declaration in Kubernetes allows you to do app deployments and updates
- When using the deployment object, you define the state of your application
  - Kubernetes will then make sure the clusters matches your desired state
- Using the replication controller or replication set might be cumbersome to deploy apps
  - The Deployment Object is easier to use and gives you more possibilities

With a deployment object you can:

- Create a deployment (deploy an app)
- Update a deployment (deploy a new version of an app)
- Rolling updates (zero downtime deployments)
- Roll back to a previous version
- Pause/resume a deployment (e.g. to roll-out to only a certain percentage)

Example deployment:

```
apiVersion: v1
kind: Deployment
metadata:
  name: helloworld-deployment
spec:
  replicas: 3
  template:
    metadata:
      labels:
        app: helloworld
    spec:
      containers:
      - name: k8s-demo
        image: k8s-demo
        ports:
        - containerPort: 3000
```

## Credit

- https://www.github.com/jleetutorial/
