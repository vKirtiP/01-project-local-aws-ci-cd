
## Multi-Tier Web Application Setup-Locally

This project involves multiple applications and begins with a local setup.

The project architecture includes the following services:

NGINX | TOMCAT | RABBITMQ | MEMCACHED | MYSQL

The initial setup takes place locally, allowing for configuration, optimization, and testing of the applications before further deployment steps are undertaken.


##  Project Description

When a user logs in to the system, the following steps occur in the local setup:

Instead of using a domain name, the user accesses the application using the IP address since it is set up locally.

The request then reaches the load balancer, which is facilitated by Nginx. Nginx acts as a proxy server, handling requests on behalf of the web server and directing them to the appropriate backend.

The load balancer (Nginx) routes the request to the Tomcat server, which is a Java-based web application service. Tomcat hosts the website and serves as the application runtime environment.

If the application requires additional storage, it can utilize NFS (Network File System) storage. The NFS storage contains the MySQL database, providing a centralized location for data storage.

Additionally, the system employs RabbitMQ, which serves as a messaging broker, enabling communication and integration between two applications.

When the user logs in, the system interacts with Memcached, a high-performance in-memory caching system connected to the MySQL database.

Initially, the data is accessed from the database. However, subsequent requests for the same data are served from the cached data in Memcached, optimizing performance and reducing the load on the database.

These components, including Nginx, Tomcat, MySQL, RabbitMQ, and Memcached, work together in the local setup to ensure efficient handling of user requests and optimal performance of the web application.


## Deployment

Cloning the source code

After cloning the source code, change to the "local-setup" branch using the command **git checkout local-setup.**

Using Vagrant to set up VM machines

Create a Vagrantfile containing the configuration for the desired number of virtual machines. Then, use vagrant up to bring up all the virtual machines.

Provisioning Services

Services will be provisioned in the following order:

**MySQL**

- Log in to the "db" virtual machine.
- Verify the host configuration using cat /etc/hosts.
- Update the operating system with the latest patches: yum update -y.
- Install MariaDB: yum install git mariadb-server -y.
- Enable and start the MariaDB service: systemctl enable mariadb and systemctl start mariadb.
- Set up a permanent database password:
- Create a password and assign it to a variable.
- Echo the variable command and add it to /etc/profile for persistence.
- Make it permanent by running source /etc/profile.

Now, run the MySQL secure installation:

- Set the same password as the one set earlier for the root user.
- Remove anonymous users: Y.
- Disallow root login remotely: N.
- Remove the test database and access to it: Y.
- Reload the privilege tables: Y.
- Clone the source code into the virtual machine and set up the database:

Run the following commands:

- mysql -u root -p"$DATABASE_PASS" -e "create database accounts"
- mysql -u root -p"$DATABASE_PASS" -e "grant all privileges on accounts.* TO 'admin'@'app01' identified by 'admin123'"
- cd vprofile-project
- mysql -u root -padmin123 accounts < src/main/resources/db_backup.sql
- Verify the data:

Run the following commands:
- mysql -u root -p"$DATABASE_PASS"
- show databases;
- use accounts;
- show tables;

**Then exit from the "db" VM and log in to the "mc"VM.**

**Memcache**

- Install, start, and enable Memcache on port 11211:
    - yum install epel-release -y
    - yum install memcached -y
    - systemctl start memcached
    - systemctl enable memcached

- Verify if Memcache is running on port 11211:
    - ss -tunlp | grep 11211

**RabbitMQ**

- Log in to the "rmq" VM and update the host entries in the /etc/hosts file if necessary.
- Update the operating system with the latest patches: yum update -y.
-   Install dependencies and RabbitMQ Server:
    sudo yum install wget -y
    cd /tmp/
    wget http://packages.erlang-solutions.com/erlang-solutions-2.0-1.noarch.rpm
    sudo rpm -Uvh erlang-solutions-2.0-1.noarch.rpm
    sudo yum -y install erlang socat

- Install RabbitMQ Server: curl -s https://packagecloud.io/install/repositories/rabbitmq/rabbitmq-server/script.rpm.sh | sudo bash 
- sudo yum install rabbitmq-server -y
- Start and enable RabbitMQ:
- sudo systemctl start rabbitmq-server
- sudo systemctl enable rabbitmq-server

Configure RabbitMQ and create a test user.

**Tomcat**

- Log in to the "app" VM and update the host entries in the /etc/hosts file if necessary.
- Update the operating system with the latest patches: yum update -y.
- Install Java, Git, Maven, and other dependencies.
- Download and set up Apache Tomcat.
- Configure Tomcat as a systemd service and start it.

Building Artifacts In the "app" VM:

- Clone the source code repository: git clone -b local-setup https://github.com/vKirtiP/project-local-aws-ci-cd.git
- Update the configuration files as needed.
- Build the code: mvn install.
- Deploy the artifact:
- Stop Tomcat.
- Remove the existing ROOT.
- Copy the newly built WAR file: cp target/vprofile-v2.war /usr/local/tomcat8/webapps/ROOT.war.
- Start Tomcat.
- Change ownership: chown tomcat.tomcat /usr/local/tomcat8/webapps -R.
- Restart Tomcat.

**Installing NGINX**

- Log in to the "web" VM.
- Install NGINX: sudo apt install nginx -y.
- Create an NGINX configuration file and update it with the appropriate upstream server details.
- Remove the default NGINX configuration file.
- Create a symbolic link to activate the website.
- Restart NGINX.

Once these steps are completed, the system should be accessible as desired.
