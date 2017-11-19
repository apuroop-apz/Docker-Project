# Project on Docker
## Introduction to Docker
**Docker** is a software tool to run applications in isolated environments with the help of containers. It is an open source containerization engine, which automates the packaging, shipping and deployment of any software applications that are presented as lightweight, portable and self-sufficient containers, that will run virtually anywhere.

A **Docker container** is a package filled with all the required libraries, dependencies, configuration files, scripts and operating system to run a software application independently. There can be numerous containers in a single machine and these containers are isolated from each other and from the host machine as well. There are sensible workarounds for running Docker service on different operating systems.

A **Docker image** is a set of instructions lined up to make a software application. Each change or commit that is made to the original image is stored in a separate layer. A docker image will have a base image on which additional modules can be attached. These images are typically read-only type. 

A **Docker layer** could represent either read-only or read-write images, but the top layer of a container stack is always read-write (writable) layer, which hosts a Docker container.
 
A **Docker Registry** is a place where the Docker images can be stored and are accessible to the public, used by the developers all over the world. Using the Docker push command, we can upload the Docker image to the Registry so that it is registered and deposited. Not to be confused with the Docker Repository.

A **Docker Repository** is a namespace that is used for storing Docker images. Suppose the name of a docker image is hello-world and the username for the Docker is dockuser, the image would be stored as dockuser/hello-world. There are several different images for a single application by different organizations and talented individuals, detailed information is available for each. To be clear, the Docker Registry is for registering the Docker images and the Docker Repository is for storing these registered images.

The following image shows that there are three containers running on a Linux machine and the Docker engine produces, monitors and bears full responsibility.

![Docker](https://github.com/apuroop-apz/Docker-Project/blob/master/figures/dock_1.png)

## Differences between a Virtual Machine and Docker

Virtual Machines | Containers
---------------- | -------------
Hardware level virtualization | Operating system virtualization
Heavyweight | Lightweight
Needs more resources | Needs minimal resources
Full isolation with guaranteed resources | Isolates processes from each other
Limit performance | Native performance
More secure since system-level isolation | Less secure since process-level isolation

![VM & Containers](https://github.com/apuroop-apz/Docker-Project/blob/master/figures/dock_2.png)

## What is a Dockerfile?

A **Dockerfile** is a text-based built script that obtains a set of instructions in a pattern such that we can build the image for a particular application from base images. The instructions in the Dockerfile can include the base image selection, setting up environment variables, installing the required application, adding or copying the configuration and the data files, exposing the networking ports and automatically running the services as well as exposing those services to the external world

INSTRUCTION | DESCRIPTION
----------- | -----------
FROM | This important one and it is the first valid instruction of a Dockerfile. It sets the base image for the build process. The command docker build will return an error if it cannot find the specified image in the Docker host and the Registry.
MAINTAINER | Provides the information about the owner.
COPY | Copy the files from the Docker host to the filesystem of the new image.
ADD | Like the COPY instruction but can also handle TAR files and the remote URLs.
ENV | Sets an environment variable in the new image. An environment variable is a key-value pair which can be accessed by any script or application.
USER | Sets the start-up user ID or user Name in the new image. By default, the containers will be launched with root as UID.
WORKDIR | Changes the current working directory from / to the path specified by the instruction. If the specified directory is not found in the target image filesystem, then the directory will be created.
VOLUME | Creates a directory in the image filesystem, which can later be used for mounting volumes from the Docker host or the other containers.
EXPOSE | Opens a container’s network port for communicating between the container and the external world.
RUN | Runs any command during the build time.
CMD | Runs any command or application, much like RUN. Difference being that the RUN instruction is executed during the build time, whereas the CMD instruction is executed when the container is freshly launched from the image.
ENTRYPOINT | Helps in running an application during the complete life cycle of the container, which would have spun out of the image. When the entry point application is terminated, the container is also terminated and vice versa.
ONBUILD | Registers a build instruction to an image and this is triggered when another image is built by using this image as its base image.

## Creating a Dockerfile

In linux, Open the terminal. Gain the root privileges.

`su – root`

Type the password for root.

Create a folder '**DockerProject**'

`mkdir DockerProject`

Create a text file '**Dockerfile**' with vim.

`vim Dockerfile`

#### Give the instructions for creating a LAMP - phpMyadmin - 100MB tpch of test data stack.

From the base image ubuntu:16.04. `FROM <image>[:tag]`

`FROM ubuntu:16.04`

Setting the Author’s details here. `MAINTAINER <author’s details>`

`MAINTAINER Apuroop Naidu <apuroop.naidu@gmail.com>`

Setting the environment variable. `ENV <key> <value>`

`ENV DEBIAN_FRONTEND noninteractive`

Running the command to update and install the applications vim, sed and netcat-openbsd
```
RUN apt-get -y update && apt-get install -y apt-utils \
		vim \
		sed \
		netcat-openbsd
```

Running the command to install the Apache2 package

`RUN apt-get -y install apache2`

Running the command to install the Mysql-server-5.7 package

`RUN apt-get -y install -y mysql-server-5.7`

Running the command to install PHP-7.0 and other related dependencies
```
RUN apt-get install -y \
        php7.0 \
        7.0-bz2 \
        php7.0-cgi \
        php7.0-cli \
        php7.0-common \
        php7.0-curl \
        php7.0-dev \
        php7.0-enchant \
        php7.0-fpm \
        php7.0-gd \
        php7.0-gmp \
        php7.0-imap \
        php7.0-interbase \
        php7.0-intl \
        php7.0-json \
        php7.0-ldap \
        php7.0-mcrypt \
        php7.0-mysql \
        php7.0-odbc \
        php7.0-opcache \
        php7.0-pgsql \
        php7.0-phpdbg \
        php7.0-pspell \
        php7.0-readline \
        php7.0-recode \
        php7.0-snmp \
        php7.0-sqlite3 \
        php7.0-sybase \
        php7.0-tidy \
        php7.0-xmlrpc \
        php7.0-xsl \
        libapache2-mod-php7.0
```
Running the commands to install the phpmyadmin application. Also, setting the Boolean value to true for selecting dbconfig to set up the database and choosing apache2 for the server selection.
```
RUN echo 'phpmyadmin phpmyadmin/dbconfig-install boolean true' | debconf-set-selections && \
	echo 'phpmyadmin phpmyadmin/reconfigure-webserver multiselect apache2' | debconf-set-selections && \
	apt-get -y install phpmyadmin --no-install-recommends
```
Copying the ‘**essentials**’ folder to the filesystem of the image.

`COPY /essentials /essentials`

Making the necessary directories; Copying files to the appropriate destinations; Giving permissions.
```
RUN phpenmod mcrypt && phpenmod mbstring && \
		mkdir -m777 /etc/tpch/ && \
		mkdir -p /var/run/mysqld && \
		touch /var/run/mysqld/mysqld.sock && \
		cp /essentials/index.php /var/www/html/ && \
		cp /essentials/my.cnf /etc/mysql/conf.d/my.cnf && \
		cp /essentials/run.sh /usr/local/bin/run.sh && \
		ln -s /usr/share/phpmyadmin /var/www/phpmyadmin && \
    chmod +x /usr/local/bin/run.sh && \
		chmod +x /essentials/*.sh && \
    chown -R www-data:www-data /var/www/html && \
		chown mysql:mysql /var/run/mysqld && \
		usermod -d /var/lib/mysql/ mysql && \
		apt-get clean
```
Copying the ‘**TPC-H_SQL**’ folder to the destination in the filesystem of the image.

`COPY TPC-H_SQL /etc/tpch/`

Creating the volume directories in the image filesystem, can later be used for mounting volumes from the host or other containers.

`VOLUME ["/var/lib/mysql", "/etc/mysql"]`

Exposing the network ports 80-tcp and 3306-mysql for communication.

`EXPOSE 80 3306`

Running the command in shell. This executes the series of commands in the shell script.

`CMD /usr/local/bin/run.sh`

Save the file '**Dockerfile**'. In vim, Press Esc, type *:wq!* and hit Enter.

## Build the docker image with the tag *lamp_pma_tpch*

`docker build -t lamp_pma_tpch .`

## Creating a folder with important shell and configuration files

Create a folder '**essentials**' inside the '**DockerProject**' folder.

`mkdir essentials`

Navigate to '**essentials**' folder

`cd essentials`

Create a **run.sh** file with vim. This shell script executes all the shell commands before starting a container.

Create a **load.sh** file with vim. This shell script is for progress/loading spinning wheel.

Create a **index.php** file with vim. Add a small php code to test the php later. This file opens the php.info page for testing.

Create a **my.cnf** file with vim. Add the configurations for [mysqld].

Create a **tpch_test.sql** file with vim. Add the series of SQL commands to create the Databases for the TPC-H schema in '**tpch_test.sql**'. The source of the codes is */TPC-H_SQL/dbgen/dss.ddl*.

## About run.sh

`#!/bin/bash`

The above line is a must for a bash shell and helps in creating a shell script. Also it should be in the first line in any bash shell file.

Setting the environments for Mysql application. These can be invoked when running the **docker run** command
```
set -e

USER=${MYSQL_USERNAME:-}
PASS=${MYSQL_PASSWORD:-}
DB=${MYSQL_DBNAME:-}
ROOTPASS=${MYSQL_ROOT_PASSWORD:-}
```

The following commands are used to edit few different files using the application *sed*
```
sed -i 's/AllowOverride\ None/AllowOverride\ All/g' /etc/apache2/apache2.conf

sed -i "s/short_open_tag\ \=\ Off/short_open_tag\ \=\ On/g" /etc/php/7.0/apache2/php.ini

echo "ServerName localhost" >> /etc/apache2/apache2.conf

sed -i "77s@.*@\/\/$cfg['Servers\'][\$i]['controluser'] = \$dbuser;@" /etc/phpmyadmin/config.inc.php
sed -i "78s@.*@\/\/$cfg['Servers\'][\$i]['controlpass'] = \$dbpass;@" /etc/phpmyadmin/config.inc.php
```

Run the *make* command in the **/etc/tpch/dbgen/** folder

`make -C /etc/tpch/dbgen/`

Navigating to **/etc/tpch/dbgen/** folder and generating the files for population. The flags -v is for Verbose and -s is volume of the data to be populated (0.1 = 100 mb ; 1 = 1 gb)

`cd /etc/tpch/dbgen/ && ./dbgen -v -s 0.1`

Starting the mysql server and also running the netcat command with -v (Verbose) -z (scan for listening daemons, without sending any data to them)
```
/usr/bin/mysqld_safe &
while ! nc -vz localhost 3306; do sleep 1; done
```

This if condition is true when we use `MYSQL_USERNAME` and `MYSQL_PASSWORD` on docker run. It creates a Mysql User and a password, if the password is not specified then it will be blank.
```
if [ ! -z $USER ]; then
	echo "Creating user: \"$USER\""
		source /essentials/load.sh
		start_spinner
		sleep 2

		/usr/bin/mysql -uroot -e "CREATE USER '$USER'@'%' IDENTIFIED BY '$PASS'"
		/usr/bin/mysql -uroot -e "GRANT ALL PRIVILEGES ON *.* TO '$USER'@'%' WITH GRANT OPTION"
		/usr/bin/mysql -uroot -e "FLUSH PRIVILEGES"

		source /essentials/load.sh
		stop_spinner $?
fi
```
This if condition is true when we use `MYSQL_DBNAME` on docker run. It creates a database with specified database name.
```
if [ ! -z $DB ]; then
	echo "Creating database: \"$DB\""
		source /essentials/load.sh
                start_spinner
                sleep 2

		/usr/bin/mysql -uroot -e 'CREATE DATABASE $DB'
		
		source /essentials/load.sh
                stop_spinner $?
fi
```
The following commands are for creating a database named **tpch** and the next line is for executing a .sql file which is packed with SQL commands. This **tpch_test.sql** creates tables in the tpch database, populate tables with dummy data, alter the schema dependencies.
```
	echo "Creating the TPC-H Database ..."
		source /essentials/load.sh
		start_spinner
		sleep 2
	
		/usr/bin/mysql -u root -e "CREATE DATABASE tpch"
		/usr/bin/mysql -u root tpch < /essentials/tpch_test.sql

		source /essentials/load.sh
		stop_spinner $?
	
		/usr/bin/mysql -u root -e "optimize table tpch.LINEITEM"
```
The following commands are for creating tables in phpmyadmin database. This is done to avoid warnings in the phpmyadmin web interface.
```
	echo "Creating tables for phpmyadmin: ..."
	        source /essentials/load.sh
	        start_spinner
	        sleep 2

		/usr/bin/mysql -u root < /usr/share/doc/phpmyadmin/examples/create_tables.sql
		/usr/bin/mysql -u root -e 'GRANT SELECT, INSERT, DELETE, UPDATE ON phpmyadmin.* TO 'pma'@'localhost' IDENTIFIED BY "pmapassword"'

	        source /essentials/load.sh
	        stop_spinner $?
```
Shutting down the Mysql service and repeating the netcat command with -v (Verbose) -z (scan for listening daemons, without sending any data to them)
```
mysqladmin -uroot shutdown
while  nc -vz localhost 3306; do sleep 1; done
```
This is condition is true when we use `MYSQL_ROOT_PASSWORD` on docker run. Since the root user is already created while installation, the command below will just grant all privileges identified by the root password which is specified.
```
if [ ! -z $ROOTPASS ]; then
service mysql start
	echo "Creating password for root: ..."
	        source /essentials/load.sh
	        start_spinner
	        sleep 2
	
	       /usr/bin/mysql -u root -e "GRANT ALL PRIVILEGES ON *.* TO 'root'@'localhost' IDENTIFIED BY '$ROOTPASS' WITH GRANT OPTION"

	        source /essentials/load.sh
	        stop_spinner $?
service mysql stop
fi
```
The following commands are for giving the logs at the end of the container-starting process. If a `MYSQL_USER` is used at docker run then the Username & Password will be shown. It's the same with the rest.
```
if [ ! -z $USER ]; then
	echo "========================================================================"
	echo "MySQL User: \"$USER\""
	echo "MySQL Password: \"$PASS\""
	echo "========================================================================"
fi

if [ ! -z $ROOTPASS ]; then
	echo "========================================================================"
	echo "MySQL root user password: \"$ROOTPASS\""
	echo "========================================================================"
fi

if [ ! -z $DB ]; then
	echo "========================================================================"
	echo "MySQL Database: \"$DB\""
	echo "========================================================================"
fi
```
`cd /`

Starting the apache2 and mysql services. The bash command will help the container to stay up when it is started in -d (detached mode).

`service apache2 start && service mysql start && bash`

## Creating a Docker Repository
- Go to https://hub.docker.com/
- Sign in
- If your Github account is not linked with Docker hub then go to **Settings**
- Click **Linked Accounts & Services**
- Link your Github account
- Back to the home page, on the top right, click **Create**
- Select **Automated Build**
- Select **Create Auto-Build Github**
- Choose your repository from Github. You must create a repository if you don't have one already.
- The name of the repository can be edited and click **Create**

## Pushing the image to Docker Registry
In the terminal, we have to change the docker image's name. This has to match the Docker Repository that we created earlier. It should be `username/imagename` 

`docker tag lamp_pma_tpch apuroopapz/lamp_pma_tpch`

`docker login`

Type in the username and password for the Docker hub. A push cannot happen without logging in.

`docker push apuroopapz/lamp_pma_tpch`
