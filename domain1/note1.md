https://docs.docker.com/engine/swarm/

https://docs.docker.com/engine/swarm/key-concepts/

https://docs.docker.com/engine/swarm/how-swarm-mode-works/nodes/

Complete the setup of a swarm mode cluster with managers and worker nodes
=========================================================================

This tutorial requires three Linux hosts which have Docker installed and can communicate over a network. These can be physical machines, virtual machines, Amazon EC2 instances, or hosted in some other way.
One of these machines will be a manager (called manager1) and two of them will be workers (worker1 and worker2).

The IP address of the manager machine
=====================================

The IP address must be assigned to a network interface available to the host operating system. All nodes in the swarm must be able to access the manager at the IP address.
Because other nodes contact the manager node on its IP address, you should use a fixed IP address.

The tutorial uses manager1 : 192.168.99.100

Open protocols and ports between the hosts
==========================================

The following ports must be available. On some systems, these ports are open by default.

    TCP port 2377 for cluster management communications
    TCP and UDP port 7946 for communication among nodes
    UDP port 4789 for overlay network traffic

If you are planning on creating an overlay network with encryption (--opt encrypted), you will also need to ensure ip protocol 50 (ESP) traffic is allowed.

manager setup
==============

On manager1 run:

    docker swarm init --advertise-addr <MANAGER-IP>

```
    $ docker swarm init --advertise-addr 192.168.99.100
Swarm initialized: current node (dxn1zf6l61qsb1josjja83ngz) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join \
    --token SWMTKN-1-49nj1cmql0jkz5s954yi3oex3nedyz0fb0xx14ie39trti4wxv-8vxv8rssmk743ojnwacrr2e7c \
    192.168.99.100:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```

After running the docker swarm join command we can view the state of the cluster with:
    docker info
    docker node ls

Worker setup
============

On worker nodes run the command from the manager init output:

```
$ docker swarm join \
  --token  SWMTKN-1-49nj1cmql0jkz5s954yi3oex3nedyz0fb0xx14ie39trti4wxv-8vxv8rssmk743ojnwacrr2e7c \
  192.168.99.100:2377

This node joined a swarm as a worker.
```
If the manager init output is lost find the join command again by running:

```
$ docker swarm join-token worker

To add a worker to this swarm, run the following command:

    docker swarm join \
    --token SWMTKN-1-49nj1cmql0jkz5s954yi3oex3nedyz0fb0xx14ie39trti4wxv-8vxv8rssmk743ojnwacrr2e7c \
    192.168.99.100:2377
```

Now view the state of the cluster consisting of 1 manager and 2 workers:

    docker node ls

```
ID                           HOSTNAME  STATUS  AVAILABILITY  MANAGER STATUS
03g1y59jwfg7cf99w4lt0f662    worker2   Ready   Active
9j68exjopxe7wfl6yuxml7a7j    worker1   Ready   Active
dxn1zf6l61qsb1josjja83ngz *  manager1  Ready   Active        Leader
```
