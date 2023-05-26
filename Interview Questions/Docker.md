## 1. What is Hypervisor?
- A hypervisor is a software that makes virtualization possible. It divides the host system and allocates the resources to each divided virtual environment.
## 2. What is virtualization?
- Virtualization lets you split one system into many different sections which act like separate, distinct individual systems. Virtualization is the process of creating a software based, virtual version of something (compite storage, servers, application etc).
## 3. What is containerization?
- An application that is being developed and deployed is bundled and wrapped together with all its configuration files and dependencies. This bundle is called container.
## 4. Docker:
- Docker is a containerization platform which packages your application and all its dependencies together in the form of containers so as to ensure that the app works in any environment.
- Docker container include the application and all its dependencies, it shares the kernel with other containers, running as isolated processes in user space on the host operating system.
- **Docker Architecture**: It is a server-client architecture composed of a client and a Docker daemon which is a long running program exposing REST APIs for clients to call.
- **Docker compose**: YAML file which contains details about the services, networks and volumes for setting up the Docker application.
- **Docker Swarm**: is native clustering for docker. It turns a pool of Docker hosts into a single, virtual Docker host. 
-  `Docker rmi image-id`:  delete an image from the local system.
- `Docker system prune`: removes all stopped containers, all networks that are not used, all dangling images and all build caches. It's one of the most useful commands.
- There are six possible states a container can be at any given point: `Created`, `Running`, `Paused`, `Restarting`, `Exited` and `Dead`.
- The container has to be in the stopped state in order to remove it.
- It is allways better to stop the containers and then remove them. Stopping a container and then removing it will allow sending SIG_HUP signal to recipients. This will ensure that all containers have enough time to clean up their tasks.
- **Docker Compose** always runs in the dependency order. These dependencies are in specifications like `depends_on`, `links` & `volumes_from`.
## 5. Will you lose your data, when Docker container exits?
- No you won't lose any data when Docker container exits. Any data that your application writes to the container gets preserved on the Disk until you explicitly delete the container.
 
