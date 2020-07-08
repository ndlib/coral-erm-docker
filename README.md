# Coral-Docker

This repository allows a base installation of the [CORAL-ERM](https://github.com/coral-erm/coral) software to be run within Docker containers. It is currently hardcoded to pull CORAL 3.0.0 as its code.

For those who are members of [GitHub's ndlib Organization](https://github.com/ndlib), more information may be available in the [Service Runbook](https://github.com/ndlib/TechnologistsPlaybook/tree/master/run-books/coral-erm-docker.md).

## Up and Running

### Prerequisites

 This repository assumes that Docker is installed on the local machine. If not, please install Docker via the instructions on the [official Docker site](https://docs.docker.com/v17.09/engine/installation/).

### Basic Running Instance

 To start CORAL-ERM in Docker containers, run the following command:

 `docker-compose up --build`

 This will build two containers for CORAL - the PHP frontend and the Database backend.

### What gets created?

#### Database

 The database container is based on MariaDB and currently uses the most recent version available via [Docker Hub](https://hub.docker.com/_/mariadb/).

 It will use the environment variables listed in the [Docker Compose](docker-compose.yml) file, including setting a root MySQL password, creating a database for CORAL-ERM, and a username and password for the created CORAL-ERM database. These values can be changed by editing the docker-compose.yml file.

 The MariaDB container will have port 3306 exposed to the localhost on port 3306. This can be adjusted as needed.

 The MariaDB container will start and run a healthcheck to ensure that the database service is running and reachable via the root name and password. This will preclude using the MYSQL_RANDOM_ROOT_PASSWORD environment variable.

#### PHP Apache Server

 In the [Dockerfile](docker/Dockerfile) within the repository, we define the front-end PHP server. Based on the current [CORAL-ERM documentation](http://docs.coral-erm.org/en/latest/install.html), we begin with a [PHP-provided Apache container](https://hub.docker.com/_/php/) and install the git command line tools and the required PHP extensions.

 Next, we copy in a PHP configuration file to ensure that the proper PHP extensions are installed on the machine.

 Our next step is to clone the 3.0 tagged version of [CORAL-ERM](https://github.com/coral-erm/coral/tree/v3.0.0) into the /var/www/html folder, and change permissions to allow CORAL's setup to write configuration files.

 Next, we write some basic values to the base apache2/httpd.conf file to handle some basic errors that are thrown.

 Lastly, Apache is launched as a daemon in the foreground to ensure the front-end container stays up and running.

### The containers are running: Now What?

 Once the containers are running, you can access the CORAL-ERM installation page by visiting http://localhost:8080/coral/ and step through the installation - the documentation for this is located on the [official CORAL-ERM page](http://docs.coral-erm.org/en/latest/install.html).
 
### What needs to be done outside of docker-compose?

There is one issue where after CORAL-ERM installation completes, the application would like to remove the write access to the configuration files.

 On a basic stack, this can be accomplished by running the following from a Linux command line prompt:

  `docker exec -t coral-docker_web_1 chmod 755 -R /var/www/html/`

 This will run a permission change on the needed directories. Once it is complete, you should be able to click the "Try Again" button on the bottom of the error page within CORAL-ERM.

### What's left?

- [ ] We would like to move from the PHP 5.6-apache image to a more lightweight image such as [Alpine Linux](https://hub.docker.com/_/alpine/) but this is a premature optimization for the containerization project.
