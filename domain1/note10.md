https://docs.docker.com/engine/swarm/services/#give-a-service-access-to-volumes-or-bind-mounts

Mount volumes
=============

Give a service access to volumes or bind mounts

For best performance and portability, you should avoid writing important data directly into a container’s writable layer, instead using data volumes or bind mounts. This principle also applies to services.

You can create two types of mounts for services in a swarm, volume mounts or bind mounts. Regardless of which type of mount you use, configure it using the --mount flag when you create a service, or the --mount-add or --mount-rm flag when updating an existing service.. The default is a data volume if you don’t specify a type.
Data volumes

Data volumes are storage that remain alive after a container for a task has been removed. The preferred method to mount volumes is to leverage an existing volume:

```
$ docker service create \
  --mount src=<VOLUME-NAME>,dst=<CONTAINER-PATH> \
  --name myservice \
  <IMAGE>
```
