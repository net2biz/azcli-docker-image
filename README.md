# azcli-docker-image

We will be running a container from this CLI and executing all subsequent commands from within the shell running inside this container. Now, there is a little problem we need to overcome. This container will not have a Docker client installed. But we will also run some Docker commands, so we have to create a custom image derived from the preceding image, which contains a Docker client. The Dockerfile that's needed to do so can be found in the ~/fod/ch18 folder and has this content:

FROM mcr.microsoft.com/azure-cli:latest<br>
RUN apk update && apk add docker

On line 2, we are just using the Alpine package manager, apk, to install Docker. We can then use Docker Compose to build and run this custom image. The corresponding docker-compose.yml file looks like this:

version: "2.4"
services:
    az:
        image: fundamentalsofdocker/azure-cli
        build: .
        command: tail -F anything
        working_dir: /app
        volumes:
            - /var/run/docker.sock:/var/run/docker.sock
            - .:/app

Please note the command that is used to keep the container running, as well as the mounting of the Docker socket and the current folder in the volumes section. 
If you are running Docker for Desktop on Windows, then you need to define the COMPOSE_CONVERT_WINDOWS_PATHS environment variable to be able to mount the Docker socket. Use
export COMPOSE_CONVERT_WINDOWS_PATHS=1 from a Bash shell or $Env:COMPOSE_CONVERT_WINDOWS_PATHS=1 when running PowerShell. Please refer to the following link for more details: https://github.com/docker/compose/issues/4240.
Now, let's build and run this container:

$ docker-compose up --build -d
Then, let's execute into the az container and run a Bash shell in it with the following:

$ docker-compose exec az /bin/bash

bash-5.0#
We will find ourselves running in a Bash shell inside the container. Let's first check the version of the CLI:

bash-5.0# az --version
This should result in an output similar to this (shortened):

azure-cli 2.0.78
...
Your CLI is up-to-date.
OK, we're running on version 2.0.78. Next, we need to log in to our account. Execute this command:

bash-5.0# az login
