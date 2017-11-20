
Make service assessible from outside world
==========================================

To forward port 8080 on host to port 80 on the container:

```
$ docker run -d -p 8080:80 nginx
```

If you don't want to specify the port and want docker to automatically assign the port number:

```
$ ID=$(docker run -d -P nginx)
$ docker port $ID 80
```

Best practices for Dockerfiles
==============================

https://docs.docker.com/engine/reference/builder/
https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/

Docker build examples
=====================

```
$ docker build -f /path/to/a/Dockerfile .
$ docker build -t vieux/apache:2.0 .
$ docker build - < Dockerfile
$ curl example.com/remote/Dockerfile | docker build -f - .
```

Parser directives
=================

- A Dockerfile must begin with a FROM instruction.
- A line that begins with # is a comment unless it is a valid parser directive

Parser directives are optional, and affect the way in which subsequent lines in a Dockerfile are handled. Parser directives do not add layers to the build, and will not be shown as a build step. Parser directives are written as a special type of comment in the form # directive=value. A single directive may only be used once.

Once a comment, empty line or builder instruction has been processed, Docker no longer looks for parser directives. Instead it treats anything formatted as a parser directive as a comment and does not attempt to validate if it might be a parser directive. Therefore, all parser directives must be at the very top of a Dockerfile.

A suppoted parser directive is escape. The backslash is default. The backtick is useful for windows:

```
# escape=\ 
# escape=`
```

Environment variables
=====================

Environment variables (declared with the ENV statement) can also be used in certain instructions as variables to be interpreted by the Dockerfile. Escapes are also handled for including variable-like syntax into a statement literally.

Environment variables are notated in the Dockerfile either with $variable_name or ${variable_name}. They are treated equivalently and the brace syntax is typically used to address issues with variable names with no whitespace, like ${foo}_bar.

The ${variable_name} syntax also supports a few of the standard bash modifiers as specified below:

    ${variable:-word} indicates that if variable is set then the result will be that value. If variable is not set then word will be the result.
    ${variable:+word} indicates that if variable is set then word will be the result, otherwise the result is the empty string.

In all cases, word can be any string, including additional environment variables.

.dockerignore file
==================

Before the docker CLI sends the context to the docker daemon, it looks for a file named .dockerignore in the root directory of the context. If this file exists, the CLI modifies the context to exclude files and directories that match patterns in it. This helps to avoid unnecessarily sending large or sensitive files and directories to the daemon and potentially adding them to images using ADD or COPY.

Here is an example .dockerignore file:

```
# comment
*/temp*
*/*/temp*
temp?
```

Understand how ARG and FROM interact
====================================

- ARG is build time, ENV is run time

FROM instructions support variables that are declared by any ARG instructions that occur before the first FROM.

```
ARG  CODE_VERSION=latest
FROM base:${CODE_VERSION}
CMD  /code/run-app

FROM extras:${CODE_VERSION}
CMD  /code/run-extras
```

An ARG declared before a FROM is outside of a build stage, so it can’t be used in any instruction after a FROM. To use the default value of an ARG declared before the first FROM use an ARG instruction without a value inside of a build stage:

```
ARG VERSION=latest
FROM busybox:$VERSION
ARG VERSION
RUN echo $VERSION > image_version
```

RUN
===

RUN has 2 forms:

    RUN <command> (shell form, the command is run in a shell, which by default is /bin/sh -c on Linux or cmd /S /C on Windows)
    RUN ["executable", "param1", "param2"] (exec form)

The RUN instruction will execute any commands in a new layer on top of the current image and commit the results. The resulting committed image will be used for the next step in the Dockerfile.
