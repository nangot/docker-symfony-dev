# docker-symfony-dev

Docker development environment for symfony application.

## Usage
Build services

    docker-compose build

> Warning: If a 404 error is encountered during the build on a repository url, add the option `--no-cache` to the command.

Run containers

    docker-compose up


Connect to PHP container

    docker exec -it container_name bash

> Note: the container_name is generated based on the name of the parent folder and the name of the service. Enter `docker-compose ps` to list the containers to identify which one is running the web server.

Check symfony container

    symfony check:requirements

Everything should be ok without warning or error.

