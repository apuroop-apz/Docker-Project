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

## Differences between Virtual Machines and Containers

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

## Create a Dockerfile

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

Set the Author’s details here. `MAINTAINER <author’s details>`

`MAINTAINER Apuroop Naidu <apuroop.naidu@gmail.com>`

Set the environment variable. `ENV <key> <value>`

`ENV DEBIAN_FRONTEND noninteractive`

Run the command to update and install the applications vim, sed and netcat-openbsd.
```
RUN apt-get -y update && apt-get install -y apt-utils \
		vim \
		sed \
		netcat-openbsd
```

Run the command to install the Apache2 package.

`RUN apt-get -y install apache2`

Run the command to install the Mysql-server-5.7 package.

`RUN apt-get -y install -y mysql-server-5.7`

Run the command to install PHP-7.0 and other related dependencies.
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
Run the commands to install the phpmyadmin application. Also, set the Boolean value to true for selecting dbconfig to set up the database and choose apache2 for the server selection.
```
RUN echo 'phpmyadmin phpmyadmin/dbconfig-install boolean true' | debconf-set-selections && \
	echo 'phpmyadmin phpmyadmin/reconfigure-webserver multiselect apache2' | debconf-set-selections && \
	apt-get -y install phpmyadmin --no-install-recommends
```
Copy the ‘**essentials**’ folder to the filesystem of the image.

`COPY /essentials /essentials`

Create the necessary directories; Copy files to the appropriate destinations; Give permissions.
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
Copy the ‘**TPC-H_SQL**’ folder to the destination in the filesystem of the image.

`COPY TPC-H_SQL /etc/tpch/`

Create the volume directories in the image filesystem, can later be used for mounting volumes from the host or other containers.

`VOLUME ["/var/lib/mysql", "/etc/mysql"]`

Expose the network ports 80-tcp and 3306-mysql for communication.

`EXPOSE 80 3306`

Run the command in shell. This executes the series of commands in the shell script **run.sh**.

`CMD /usr/local/bin/run.sh`

Save the file '**Dockerfile**'. In vim, Press Esc, type *:wq!* and hit Enter.

## Create a folder 'essentials'
The '**essentials**' folder consists of important shell and configuration files.

Create a folder '**essentials**' inside the '**DockerProject**' folder.

`mkdir essentials`

Navigate to '**essentials**' folder

`cd essentials`

### About run.sh
Create a **run.sh** file with vim, `vim run.sh`

This shell script executes all the shell commands before starting a container.

`#!/bin/bash`

The above line is a must for a bash shell and helps in creating a shell script. Also it should be in the first line in any bash shell file.

Set the environments for Mysql application. These can be invoked when running the **docker run** command
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

Navigate to **/etc/tpch/dbgen/** folder and generate the files for population. The flags -v is for Verbose and -s is volume of the data to be populated (0.1 = 100 mb ; 1 = 1 gb)

`cd /etc/tpch/dbgen/ && ./dbgen -v -s 0.1`

Start the mysql server and also run the netcat command with -v (Verbose) -z (scan for listening daemons, without sending any data to them)
```
/usr/bin/mysqld_safe &
while ! nc -vz localhost 3306; do sleep 1; done
```

This if condition is true when we use `MYSQL_USERNAME` and `MYSQL_PASSWORD` at docker run. It creates a Mysql User and a password, if the password is not specified then it will be blank.
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
This if condition is true when we use `MYSQL_DBNAME` at docker run. It creates a database with specified database name.
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
Shut down the Mysql service and repeat the netcat command with -v (Verbose) -z (scan for listening daemons, without sending any data to them)
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
The following commands are for giving the logs at the end of the 'container-starting' process. If a `MYSQL_USER` is used at docker run then the Username & Password will be shown. It's the same with the rest.
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

Start the apache2 and mysql services. The bash command will help the container to stay up when it is started in -d (detached mode).

`service apache2 start && service mysql start && bash`

### About load.sh
Create a **load.sh** file with vim, `vim load.sh`

The load.sh file is used to indicate the progress of the processes once the container is started in an interactive mode. It is a progress/loading spinning wheel.

```
#!/bin/bash

function _spinner() {
    # $1 start/stop
    #
    # on start: $2 display message
    # on stop : $2 process exit status
    #           $3 spinner function pid (supplied from stop_spinner)

    local on_success="DONE"
    local on_fail="FAIL"
    local white="\e[1;37m"
    local green="\e[1;32m"
    local red="\e[1;31m"
    local nc="\e[0m"

    case $1 in
        start)
            # calculate the column r and status msg will be displayed
           #  let column=$(tput cols)-${#2}-0
            # display message and position the cursor in $column column
            echo -ne ${2}
            printf "LOADING... %5s"

            # start spinner
            i=1
            sp='\|/-'
            delay=${SPINNER_DELAY:-0.15}

            while :
            do
                printf "\b${sp:i++%${#sp}:1}"
                sleep $delay
            done
            ;;
        stop)
            if [[ -z ${3} ]]; then
                echo "spinner is not running.."
                exit 1
            fi

            kill $3 > /dev/null 2>&1

            # inform the user uppon success or failure
            echo -en "\b["
            if [[ $2 -eq 0 ]]; then
                echo -en "${green}${on_success}${nc}"
            else
                echo -en "${red}${on_fail}${nc}"
            fi
            echo -e "]"
            ;;
        *)
            echo "invalid argument, try {start/stop}"
            exit 1
            ;;
    esac
}

function start_spinner {
    # $1 : msg to display
    _spinner "start" "${1}" &
    # set global spinner pid
    _sp_pid=$!
    disown
}

function stop_spinner {
    # $1 : command exit status
    _spinner "stop" $1 $_sp_pid
    unset _sp_pid
}
```

### About index.php
Create an **index.php** file with vim, `vim index.php`

The code in index.php is for testing the php package once the container is started. We can check with, *localhost/index.php*
```
<?php
phpinfo();
?>
```

### About my.cnf
Create a **my.cnf** file with vim, `vim my.cnf`

This is a configuration file for mysql application. 
```
[mysqld]
bind-address = 0.0.0.0
console=1
general_log=1
general_log_file=/dev/stdout
log_error=/dev/stderr
```

### About tpch_test.sql
Create a **tpch_test.sql** file with vim, `vim tpch_test.sql`

It consists of a series of SQL commands to create the Databases for the TPC-H schema. The source of the codes is */TPC-H_SQL/dbgen/dss.ddl*. It creates tables in tpch database, populates tables with generated dummy data and alters the schema dependencies.
```
USE tpch;

CREATE TABLE NATION  ( N_NATIONKEY  INTEGER NOT NULL,
                            N_NAME       CHAR(25) NOT NULL,
                            N_REGIONKEY  INTEGER NOT NULL,
                            N_COMMENT    VARCHAR(152));

CREATE TABLE REGION  ( R_REGIONKEY  INTEGER NOT NULL,
                            R_NAME       CHAR(25) NOT NULL,
                            R_COMMENT    VARCHAR(152));

CREATE TABLE PART  ( P_PARTKEY     INTEGER NOT NULL,
                          P_NAME        VARCHAR(55) NOT NULL,
                          P_MFGR        CHAR(25) NOT NULL,
                          P_BRAND       CHAR(10) NOT NULL,
                          P_TYPE        VARCHAR(25) NOT NULL,
                          P_SIZE        INTEGER NOT NULL,
                          P_CONTAINER   CHAR(10) NOT NULL,
                          P_RETAILPRICE DECIMAL(15,2) NOT NULL,
                          P_COMMENT     VARCHAR(23) NOT NULL );

CREATE TABLE SUPPLIER ( S_SUPPKEY     INTEGER NOT NULL,
                             S_NAME        CHAR(25) NOT NULL,
                             S_ADDRESS     VARCHAR(40) NOT NULL,
                             S_NATIONKEY   INTEGER NOT NULL,
                             S_PHONE       CHAR(15) NOT NULL,
                             S_ACCTBAL     DECIMAL(15,2) NOT NULL,
                             S_COMMENT     VARCHAR(101) NOT NULL);

CREATE TABLE PARTSUPP ( PS_PARTKEY     INTEGER NOT NULL,
                             PS_SUPPKEY     INTEGER NOT NULL,
                             PS_AVAILQTY    INTEGER NOT NULL,
                             PS_SUPPLYCOST  DECIMAL(15,2)  NOT NULL,
                             PS_COMMENT     VARCHAR(199) NOT NULL );

CREATE TABLE CUSTOMER ( C_CUSTKEY     INTEGER NOT NULL,
                             C_NAME        VARCHAR(25) NOT NULL,
                             C_ADDRESS     VARCHAR(40) NOT NULL,
                             C_NATIONKEY   INTEGER NOT NULL,
                             C_PHONE       CHAR(15) NOT NULL,
                             C_ACCTBAL     DECIMAL(15,2)   NOT NULL,
                             C_MKTSEGMENT  CHAR(10) NOT NULL,
                             C_COMMENT     VARCHAR(117) NOT NULL);

CREATE TABLE ORDERS  ( O_ORDERKEY       INTEGER NOT NULL,
                           O_CUSTKEY        INTEGER NOT NULL,
                           O_ORDERSTATUS    CHAR(1) NOT NULL,
                           O_TOTALPRICE     DECIMAL(15,2) NOT NULL,
                           O_ORDERDATE      DATE NOT NULL,
                           O_ORDERPRIORITY  CHAR(15) NOT NULL,  
                           O_CLERK          CHAR(15) NOT NULL, 
                           O_SHIPPRIORITY   INTEGER NOT NULL,
                           O_COMMENT        VARCHAR(79) NOT NULL);

CREATE TABLE LINEITEM ( L_ORDERKEY    INTEGER NOT NULL,
                             L_PARTKEY     INTEGER NOT NULL,
                             L_SUPPKEY     INTEGER NOT NULL,
                             L_LINENUMBER  INTEGER NOT NULL,
                             L_QUANTITY    DECIMAL(15,2) NOT NULL,
                             L_EXTENDEDPRICE  DECIMAL(15,2) NOT NULL,
                             L_DISCOUNT    DECIMAL(15,2) NOT NULL,
                             L_TAX         DECIMAL(15,2) NOT NULL,
                             L_RETURNFLAG  CHAR(1) NOT NULL,
                             L_LINESTATUS  CHAR(1) NOT NULL,
                             L_SHIPDATE    DATE NOT NULL,
                             L_COMMITDATE  DATE NOT NULL,
                             L_RECEIPTDATE DATE NOT NULL,
                             L_SHIPINSTRUCT CHAR(25) NOT NULL,
                             L_SHIPMODE     CHAR(10) NOT NULL,
                             L_COMMENT      VARCHAR(44) NOT NULL);


LOAD DATA LOCAL INFILE 'customer.tbl' INTO TABLE CUSTOMER FIELDS TERMINATED BY '|';
LOAD DATA LOCAL INFILE 'orders.tbl' INTO TABLE ORDERS FIELDS TERMINATED BY '|';
LOAD DATA LOCAL INFILE 'lineitem.tbl' INTO TABLE LINEITEM FIELDS TERMINATED BY '|';
LOAD DATA LOCAL INFILE 'nation.tbl' INTO TABLE NATION FIELDS TERMINATED BY '|';
LOAD DATA LOCAL INFILE 'partsupp.tbl' INTO TABLE PARTSUPP FIELDS TERMINATED BY '|';
LOAD DATA LOCAL INFILE 'part.tbl' INTO TABLE PART FIELDS TERMINATED BY '|';
LOAD DATA LOCAL INFILE 'region.tbl' INTO TABLE REGION FIELDS TERMINATED BY '|';
LOAD DATA LOCAL INFILE 'supplier.tbl' INTO TABLE SUPPLIER FIELDS TERMINATED BY '|';


-- ALTER TABLE REGION DROP PRIMARY KEY;
-- ALTER TABLE NATION DROP PRIMARY KEY;
-- ALTER TABLE PART DROP PRIMARY KEY;
-- ALTER TABLE SUPPLIER DROP PRIMARY KEY;
-- ALTER TABLE PARTSUPP DROP PRIMARY KEY;
-- ALTER TABLE ORDERS DROP PRIMARY KEY;
-- ALTER TABLE LINEITEM DROP PRIMARY KEY;
-- ALTER TABLE CUSTOMER DROP PRIMARY KEY;


-- For table REGION
ALTER TABLE REGION
ADD PRIMARY KEY (R_REGIONKEY);

-- For table NATION
ALTER TABLE NATION
ADD PRIMARY KEY (N_NATIONKEY);

ALTER TABLE NATION
ADD FOREIGN KEY NATION_FK1 (N_REGIONKEY) references REGION(R_REGIONKEY);


-- For table PART
ALTER TABLE PART
ADD PRIMARY KEY (P_PARTKEY);


-- For table SUPPLIER
ALTER TABLE SUPPLIER
ADD PRIMARY KEY (S_SUPPKEY);

ALTER TABLE SUPPLIER
ADD FOREIGN KEY SUPPLIER_FK1 (S_NATIONKEY) references NATION(N_NATIONKEY);


-- For table PARTSUPP
ALTER TABLE PARTSUPP
ADD PRIMARY KEY (PS_PARTKEY,PS_SUPPKEY);


-- For table CUSTOMER
ALTER TABLE CUSTOMER
ADD PRIMARY KEY (C_CUSTKEY);

ALTER TABLE CUSTOMER
ADD FOREIGN KEY CUSTOMER_FK1 (C_NATIONKEY) references NATION(N_NATIONKEY);


-- For table LINEITEM
ALTER TABLE LINEITEM
ADD PRIMARY KEY (L_ORDERKEY,L_LINENUMBER);


-- For table ORDERS
ALTER TABLE ORDERS
ADD PRIMARY KEY (O_ORDERKEY);


-- For table PARTSUPP
ALTER TABLE PARTSUPP
ADD FOREIGN KEY PARTSUPP_FK1 (PS_SUPPKEY) references SUPPLIER(S_SUPPKEY);


ALTER TABLE PARTSUPP
ADD FOREIGN KEY PARTSUPP_FK2 (PS_PARTKEY) references PART(P_PARTKEY);


-- For table ORDERS
ALTER TABLE ORDERS
ADD FOREIGN KEY ORDERS_FK1 (O_CUSTKEY) references CUSTOMER(C_CUSTKEY);


-- For table LINEITEM
ALTER TABLE LINEITEM
ADD FOREIGN KEY LINEITEM_FK1 (L_ORDERKEY)  references ORDERS(O_ORDERKEY);


ALTER TABLE LINEITEM
ADD FOREIGN KEY LINEITEM_FK2 (L_PARTKEY,L_SUPPKEY) references 
        PARTSUPP(PS_PARTKEY, PS_SUPPKEY);
```

## Build the docker image
Build the docker image with the tag or name **lamp_pma_tpch**. This command should be run by staying in the folder where the Dockerfile is at. In this case, `cd /DockerProject` and now build,

`docker build -t lamp_pma_tpch .`

## Run the docker image
Following are the various types of docker run usage,

Run the docker image '**lamp_pma_tpch**' with -i (--interactive, Keep STDIN open even if not attached) -t (--tty, Allocate a pseudo-TTY) -p (--publish list, Publish a container's port(s) to the host)  --name (Assign a name to the container). To be clear, `-p containerPort:hostPort`

`docker run -i -t -p 8080:80 --name anyname apuroopapz/lamp_pma_tpch`

Run the docker image '**lamp_pma_tpch**' with -d (--detach, Run container in the background and print container ID) -t (--tty, Allocate a pseudo-TTY) -p (--publish list, Publish a container's port(s) to the host)  --name (Assign a name to the container).

`docker run -d -t -p 8080:80 --name anyname apuroopapz/lamp_pma_tpch`

Run the docker image with same options above but also set -e (--env list, Set environment variables), here we are assigning a Mysql root password = **anyrootpass**

`docker run -d -t -p 8080:80 --name anyname -e MYSQL_ROOT_PASSWORD=anyrootpass apuroopapz/lamp_pma_tpch`

Run the docker image with same options above but also set -e (--env list, Set environment variables), here we are assigning a Mysql username = **anyusername** and password = **anypass**

`docker run -d -t -p 8080:80 --name anyname -e MYSQL_USERNAME=anyusername -e MYSQL_PASSWORD=anypass apuroopapz/lamp_pma_tpch`

Run the docker image with same options as above but also set -e (--env list, Set environment variables), here we are assigning a Mysql username = **anyusername** and password = **anypass** and database = **anydbname**

`docker run -d -t -p 8080:80 --name anyname -e MYSQL_USERNAME=anyusername -e MYSQL_PASSWORD=anypass -e MYSQL_DBNAME=anydbname apuroopapz/lamp_pma_tpch`

Run the docker image with same options as above but we are assigning a -v option (--volume, mount a volume directory), this creates a volume directory in the container filesystem when the container is started. To be clear, `-v <container mount path>`

`docker run -d -t -p 8080:80 --name anyname -v /dir1/dir2/ apuroopapz/lamp_pma_tpch`

Run the docker image with same options as above but we are assigning a -v option (--volume, mount a volume directory), this mounts /hostDir1/hostDir2/ of the host filesystem to the /containerDir1/containerDir2/ of the container filesystem. To be clear, `-v <host path>:<container mount path>`

`docker run -d -t -p 8080:80 --name anyname -v /hostDir1/hostDir2/:/containerDir1/containerDir2/ apuroopapz/lamp_pma_tpch`

## Test the docker image
- Assuming that you have already installed curl application in your host OS, this will give an output of the phpmyadmin page inside the terminal.

`curl localhost:8080/phpmyadmin`

- Open a web browser > In the address bar, type **localhost:8080/phpmyadmin** > You will see the phpmyadmin login page.

- Check if the services apache2, Mysql are running inside the container.

`docker exec -i -t anyname /bin/bash`

Inside the container, 

`service apache2 status`

`service mysql status`

## Create a Docker Repository
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

## Push the docker image to Docker Repository
In the terminal, we have to change the docker image's name. This has to match the Docker Repository that we created earlier. It should be `username/imagename` 

`docker tag lamp_pma_tpch apuroopapz/lamp_pma_tpch`

`docker login`

Type in the username and password for the Docker hub. A push cannot happen without logging in.

`docker push apuroopapz/lamp_pma_tpch`

## Download the docker image

In a different OS,

`docker pull apuroopapz/lamp_pma_tpch`
