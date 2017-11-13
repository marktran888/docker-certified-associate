https://docs.docker.com/engine/swarm/swarm-tutorial/inspect-service/

Interpret the output of "docker inspect" commands
=================================================

On the manager (manager1) node to view details about helloworld service:

```
$ docker service inspect --pretty helloworld

ID:		9uk4639qpg7npwf3fn2aasksr
Name:		helloworld
Service Mode:	REPLICATED
 Replicas:		1
Placement:
UpdateConfig:
 Parallelism:	1
ContainerSpec:
 Image:		alpine
 Args:	ping docker.com
Resources:
Endpoint Mode:  vip
```

Run docker service ps <SERVICE-ID> to see which nodes are running the service:

```
$ docker service ps helloworld

NAME                                    IMAGE   NODE     DESIRED STATE  LAST STATE
helloworld.1.8p1vev3fq5zm0mi8g0as41w35  alpine  worker2  Running        Running 3 minutes
```

Run docker ps on the node where the task is running to see details about the container for the task.

```
$docker ps

CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
e609dde94e47        alpine:latest       "ping docker.com"   3 minutes ago       Up 3 minutes                            helloworld.1.8p1vev3fq5zm0mi8g0as41w35
```



