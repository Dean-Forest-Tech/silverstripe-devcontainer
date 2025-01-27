# Silverstripe Devcontainer Environment

A Silverstripe development environment that uses Docker and VSCode [Dev Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)

Dev Containers are a way of connecting a current project to a Docker container for development, this project intends to create a modular development environment that can
be cloned and then linked to a Silverstripe project using VSCode.

## Further reading

Some great resources on Dev Containers:

* https://code.visualstudio.com/docs/devcontainers/create-dev-container
* https://dev.to/graezykev/dev-containers-part-5-multiple-projects-shared-container-configuration-2hoi

## Initial Setup

Making use of this project is pretty simple, just clone this repo:

    git clone https://github.com/Dean-Forest-Tech/silverstripe-devcontainer.git


Then navigate to your Silverstripe project and add either:

    /path/to/your/silverstripe/root/.devcontainer.json

OR

    /path/to/your/silverstripe/root/.devcontainer/devcontainer.json

Next, add some configuration for your project:

```JSON
{
    "name": "Dev server",
    "dockerComposeFile": [
        "/path/to/silverstripe-devcontainer/docker-compose.yml"
    ]
    "service": "php",
    "workspaceFolder": "/var/www/html",
    "shutdownAction": "stopCompose",
    "remoteUser": "www-data"
}
```

You will then need to add a [Docker Compose Extension](https://docs.docker.com/compose/how-tos/multiple-compose-files/extends/#multiple-compose-files) to your project, which is used to map your local project files to the correct directory within the container.

Do this by creating either:

    /path/to/your/silverstripe/root/docker-compose.yml

OR

    /path/to/your/silverstripe/root/.devcontainer/docker-compose.yml


```YAML
# Extended from ../silverstripe-dev/docker-compose.yml
# via devcontainer.json

services:
php:
    volumes:
    - type: bind
        source: ~/Projects/far-open-store
        target: /var/www/html

nginx:
    volumes:
    - type: bind
        source: ~/Projects/far-open-store
        target: /var/www/html
```

Then, you must tell your devcontainer config to extend the core `docker-compose.yml` with the extension file you just created. You can do this by editing `dockerComposeFile` in your `devcontainer.json` config to look something like (either absolute or relative paths are acceptable):

```JSON
    "dockerComposeFile": [
        "/path/to/silverstripe-devcontainer/docker-compose.yml",
        "/path/to/silverstripe/project/docker-compose.yml"
    ]
```

## Environmental variables

This project makes use of several environmental variables that you need to configure (either via your extended `docker-compose.yml` or a `.env` file). These are:

`WWW_USER_ID` The ID of the user in the system that the webserver and PHP processes will run as. In *nix based host sytstems, this is required to avoid permission errors

`WWW_GROUP_ID` The ID of the system group that the webserver and PHP processes will run as. In *nix based host sytstems, this is required to avoid permission errors

`XDEBUG_CLIENT` Use for XDebug to listen for debuging traffic on the internal docker network, usually this would be set to `host.docker.internal`

`PHP_IDE_CONFIG` IDE name to share with VSCode.

`WEBROOT` The path to the webroot of the site inside the docker container.

`DB_ROOT_PASSWORD` Root password to use for the database server (make sure you also use this in your Silverstripe environment config)

For example, if you are using an environment file, yours may look something like this:

```ENV
WWW_USER_ID=1000
WWW_GROUP_ID=1000
XDEBUG_CLIENT="host.docker.internal"
PHP_IDE_CONFIG=serverName=silverstripedebugconfig
WEBROOT="/var/www/html/public"
DB_ROOT_PASSWORD="dev"
```

## Running

Once configures, when your project is open in VSCode, press `F1` then run `Dev Containers: Rebuild Container`.

This should generate the relevent containers and link your VSCode window to the active PHP container