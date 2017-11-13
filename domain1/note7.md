https://docs.docker.com/engine/reference/commandline/stack_services/

Manipulate a running stack of services
======================================

Usage
=====

```
docker stack services [OPTIONS] STACK
```

Options
=======

```
Name, shorthand 	Description
--filter , -f       Filter output based on conditions provided
--format            Pretty-print services using a Go template
--quiet , -q        Only display IDs
```

Commands
========

```
docker stack deploy     Deploy a new stack or update an existing stack
docker stack ls         List stacks
docker stack ps         List the tasks in the stack
docker stack rm         Remove one or more stacks
docker stack services 	List the services in the stack
```

Example
=======

The following command shows all services in the myapp stack:

```
$ docker stack services myapp

ID            NAME            REPLICAS  IMAGE                                                                          COMMAND
7be5ei6sqeye  myapp_web       1/1       nginx@sha256:23f809e7fd5952e7d5be065b4d3643fbbceccd349d537b62a123ef2201bc886f
dn7m7nhhfb9y  myapp_db        1/1       mysql@sha256:a9a5b559f8821fe73d58c3606c812d1c044868d42c63817fa5125fd9d8b7b539
```

Filtering
=========

The filtering flag (-f or --filter) format is a key=value pair. If there is more than one filter, then pass multiple flags (e.g. --filter "foo=bar" --filter "bif=baz"). Multiple filter flags are combined as an OR filter.

The following command shows both the web and db services:

```
$ docker stack services --filter name=myapp_web --filter name=myapp_db myapp

ID            NAME            REPLICAS  IMAGE                                                                          COMMAND
7be5ei6sqeye  myapp_web       1/1       nginx@sha256:23f809e7fd5952e7d5be065b4d3643fbbceccd349d537b62a123ef2201bc886f
dn7m7nhhfb9y  myapp_db        1/1       mysql@sha256:a9a5b559f8821fe73d58c3606c812d1c044868d42c63817fa5125fd9d8b7b539
```

The currently supported filters are:

```
    id / ID (--filter id=7be5ei6sqeye, or --filter ID=7be5ei6sqeye)
    name (--filter name=myapp_web)
    label (--filter label=key=value)
```

Formatting
==========

The formatting options (--format) pretty-prints services output using a Go template.

Valid placeholders for the Go template are listed below:

```
Placeholder 	Description
.ID          	Service ID
.Name 	        Service name
.Mode 	        Service mode (replicated, global)
.Replicas 	    Service replicas
.Image 	        Service image
```

```
$ docker stack services --format "{{.ID}}: {{.Mode}} {{.Replicas}}"

0zmvwuiu3vue: replicated 10/10
fm6uf97exkul: global 5/5
```