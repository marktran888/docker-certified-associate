https://docs.docker.com/engine/swarm/networking/#attach-a-service-to-an-overlay-network
https://docs.docker.com/engine/swarm/services/#publish-ports

Add networks, publish ports
===========================

To create an overlay network, specify the overlay driver when using the docker network create command:

```
$ docker network create \
  --driver overlay \
  my-network

$ docker network inspect my-network
```

Configure the subnet and gateway
================================

By default, the network’s subnet and gateway are configured automatically when the first service is connected to the network. You can configure these when creating a network using the --subnet and --gateway flags. The following example extends the previous one by configuring the subnet and gateway.

```
$ docker network create \
  --driver overlay \
  --subnet 10.0.9.0/24 \
  --gateway 10.0.9.99 \
  my-network
```

Overlay network size limitations
================================

You should create overlay networks with /24 blocks (the default), which limits you to 256 IP addresses, when you create networks using the default VIP-based endpoint-mode. This recommendation addresses limitations with swarm mode. If you need more than 256 IP addresses, do not increase the IP block size. You can either use dnsrr endpoint mode with an external load balancer, or use multiple smaller overlay networks. See Configure service discovery for more information about different endpoint modes.

Attach a service to an overlay network
======================================

To attach a service to an existing overlay network, pass the --network flag to docker service create, or the --network-add flag to docker service update.

```
$ docker service create \
  --replicas 3 \
  --name my-web \
  --network my-network \
  nginx
```

Publish ports
=============

When you create a swarm service, you can publish that service’s ports to hosts outside the swarm in two ways:

    You can rely on the routing mesh. When you publish a service port, the swarm makes the service accessible at the target port on every node, regardless of whether there is a task for the service running on that node or not. This is less complex and is the right choice for many types of services.

    You can publish a service task’s port directly on the swarm node where that service is running. This bypasses the routing mesh and provides the maximum flexibility, including the ability for you to develop your own routing framework. However, you are responsible for keeping track of where each task is running and routing requests to the tasks, and load-balancing across the nodes.

#Publish a service’s ports using the routing mesh

To publish a service’s ports externally to the swarm, use the --publish <TARGET-PORT>:<SERVICE-PORT> flag. The swarm makes the service accessible at the target port on every swarm node. If an external host connects to that port on any swarm node, the routing mesh routes it to a task. The external host does not need to know the IP addresses or internally-used ports of the service tasks to interact with the service. When a user or process connects to a service, any worker node running a service task may respond. For more details about swarm service networking, see Manage swarm service networks.
Example: Run a three-task Nginx service on 10-node swarm

Imagine that you have a 10-node swarm, and you deploy an Nginx service running three tasks on a 10-node swarm:

```
$ docker service create --name my_web \
                        --replicas 3 \
                        --publish 8080:80 \
                        nginx
```

Three tasks will run on up to three nodes. You don’t need to know which nodes are running the tasks; connecting to port 8080 on any of the 10 nodes will connect you to one of the three nginx tasks. You can test this using curl. The following example assumes that localhost is one of the swarm nodes. If this is not the case, or localhost does not resolve to an IP address on your host, substitute the host’s IP address or resolvable host name.

The HTML output is truncated:

```
$ curl localhost:8080

<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...truncated...
</html>
```

Subsequent connections may be routed to the same swarm node or a different one.

#Publish a service’s ports directly on the swarm node

Using the routing mesh may not be the right choice for your application if you need to make routing decisions based on application state or you need total control of the process for routing requests to your service’s tasks. To publish a service’s port directly on the node where it is running, use the mode=host option to the --publish flag.

    Note: If you publish a service’s ports directly on the swarm node using mode=host and also set published=<PORT> this creates an implicit limitation that you can only run one task for that service on a given swarm node. You can work around this by specifying published without a port definition, which causes Docker to assign a random port for each task.

    In addition, if you use mode=host and you do not use the --mode=global flag on docker service create, it will be difficult to know which nodes are running the service in order to route work to them.

Example: Run a nginx web server service on every swarm node

nginx is an open source reverse proxy, load balancer, HTTP cache, and a web server. If you run nginx as a service using the routing mesh, connecting to the nginx port on any swarm node will show you the web page for (effectively) a random swarm node running the service.

The following example runs nginx as a service on each node in your swarm and exposes nginx port locally on each swarm node.

```
$ docker service create \
  --mode global \
  --publish mode=host,target=80,published=8080 \
  --name=nginx \
  nginx:latest
```

You can reach the nginx server on port 8080 of every swarm node. If you add a node to the swarm, a nginx task will be started on it. You cannot start another service or container on any swarm node which binds to port 8080.

    Note: This is a naive example. Creating an application-layer routing framework for a multi-tiered service is complex and out of scope for this topic.
