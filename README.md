

# Project-01
# Multi Tier Web Application Setup, Locally
## :ledger: Index

- [About The Project](#beginner-about-the-project)
- [Problem that this project solves ](#question-problem-that-this-project-solves)
- [Solution to the problem.](#key-solution-to-the-problem)
- [Tools and Technologies](#hammer-tools-and-technologies)
- [Architecture of this project](#house-architecture-of-this-project)
- [Steps to Execute the project](#electric_plug-steps-to-execute-the-project)
  - [Setup tools ](#electric_plug-setup-tools )
  - [Clone source code](#package-slone-source-code)
  - [Go to the vagrant dir](#ledger-go-to-the-vagrant-dir)
  - [Bring up the Virtual machines ](#bulb-bring-up-the-virtual-machines)
  - [Validate](#heavy_check_mark-validate)
  - [Setup All the services](#zap-setup-all-the-services)
    - [Provisioning Mysql](#package-Provisioning-Mysql)
    - [Provisioning Memcached](#package-Provisioning-Memcached)
    - [Provisioning Rabbit MQ](#rabbit-Provisioning-Rabbit-MQ)
    - [Provisioning Tomcat](#cat-Provisioning-Tomcat)
    - [Provisioning Nginx](#engine-Provisioning-Nginx)
- [App Deployment to Tomcat](#rocket-app-deployment-to-tomcat) 
- [Verify from browser](#earth_africa-verify-from-browser) 
- [Resources](#page_facing_up-resources)
- [Acknowledgment](#star2-acknowledgment)


## :beginner: About The Project

## :question: Problem that this project solves 

## :key: Solution to the problem.

## :hammer: Tools
- Oracle VM Virtualbox
- Git bash
- visual studio code
- Vagrant
- Vagrant plugins
- vagrant plugin install vagrant-hostmanager
- vagrant plugin install vagrant-vbguest

<br/>
<div align="right">
    <b><a href="#Project-01">↥ back to top</a></b>
</div>
<br/>

## :house: Architecture of this project.

![architecture](https://github.com/sheygildas/Local_App_Setup/tree/local-setup/images)

<br/>
<div align="right">
    <b><a href="#Project-01">↥ back to top</a></b>
</div>
<br/>

## :electric_plug: Steps to Execute the project. 

### :electric_plug: Setup tools 
 Get tools at [https://example.com](https://example.com)


### :package: Clone source code

- clone the repository from github through the gitbash 
`   
 ```sh
git clone https://github.com/sheygildas/vprofile-project.git
   ```
   
### :ledger: Change to the vagrant dir

- Change to the vigrant directory source code through the gitbash. Mine is at;

 ```sh
 f/vprofile-project/vagrant/manual_provisioning (local-setup)
   ```
   
### :bulb: Bring up the Virtual machines 
- RUN the command below to powerup my VMs.

 ```sh
vagrant up
   ```
   
   NOTE: Bringing VMs can take long time sometimes. If VM setup stops in the middle, run *vagrant up* command again.
- check the VMs from Oracle VM VirtualBox Manager.
![Project Image](project-image-url)

### :heavy_check_mark: Validate
- validate the VMs one after the other with command vagrant ssh *<name_of_VM_given_in_Vagrantfile>* 

 ```sh
vagrant ssh web01
   ```
- checke */etc/hosts* file of web01 using the command below, the */etc/hosts* file will be updated automatically.
 ```sh
cat /etc/hosts
   ```
- Now, try to ping *app01* from *web01* by running the command below.
 ```sh
ping app01
   ```
![Project Image](project-image-url)
- If you were able to connect to *app01* successfully then type *logout* on the terminal and try to ping other services similarly.
- Lets connect to *app01* vbox and check connectivity of *app01* with *rmq01*, *db01* and *mc01*
 ```sh
cat /etc/hosts
ping rmq01
ping db01
ping mc01
logout
   ```
   
<br/>
<div align="right">
    <b><a href="#Project-01">↥ back to top</a></b>
</div>
<br/> 

### :zap: Setup All the services
- There are 6 different services for this application
 ```sh
Services
Web Service:
1. Nginx
Application Server:
2. Tomcat
Broker/Queuing Agent:
3. RabbitMQ
DB Caching:
4. Memcache
Indexing/Search service:
5. ElasticSearch
SQL Database:
6. MySQL
   ```
- These services should be setup in the order mentioned below.
 ```sh
1. MySQL (Database SVC)
2. Memcache (DB Caching SVC)
3. RabbitMQ (Broker/Queue SVC)
4. Tomcat (Application SVC)
5. Nginx (Web SVC)
   ```
   
<br/>
<div align="right">
    <b><a href="#Project-01">↥ back to top</a></b>
</div>
<br/> 

1. **Provisioning Mysql** <br>
- Login in to the *db01* VM using th following command.
```sh
vagrant ssh db01
   ```
- Switch to root user and update all packages using the following command.
```sh
sudo su -
yum update -y
   ```
- Set up db password using *DATABASE_PASS* environment variable and add it to */etc/profile* file.
```sh
DATABASE_PASS='admin123'
   ```
- The above variable is temporary, so, to make it permanent, add the variable above to */etc/profile* file and update it.
 ```sh
vi /etc/profile
source /etc/profile
   ```
- Installed the EPEL(Extra Packages for Enterprise Linux) repository.
 ```sh
yum install epel-release -y
   ```
- Install Maria DB Package
 ```sh
yum install git mariadb-server -y
   ```
- Start and enable mariadb service. We can also check the status of mariadb service to make sure it is *actively(running)*.

```sh
systemctl start mariadb
systemctl enable mariadb
systemctl status mariadb
   ```
- RUN mysql secure installation script.

```sh
mysql_secure_installation
   ```
- NOTE: For db root password, I usedadmin123

```sh
Set root password? [Y/n] Y
New password:
Re-enter new password:
Password updated successfully!
Reloading privilege tables..
... Success!
By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.
Remove anonymous users? [Y/n] Y
... Success!
Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.
Disallow root login remotely? [Y/n] n
... skipping.
By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.
Remove test database and access to it? [Y/n] Y
- Dropping test database...
... Success!
- Removing privileges on test database...
... Success!
Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.
Reload privilege tables now? [Y/n] Y
... Success!
   ```
- Check connectivity to db with command blow: Once it asks password, enter *admin123* . If connection is succesful *exit* from DB.

```sh
mysql -u root -p
exit
   ```
- Next, clone source code to db VM then change directory to *src/main/resources/* to get the `*sql* queries.

```sh
git clone https://github.com/sheygildas/vprofile-project.git
cd vprofile-project/src/main/resources/
   ```
- Run command below before initializing DB

```sh
mysql -u root -p"$DATABASE_PASS" -e "create database accounts"
mysql -u root -p"$DATABASE_PASS" -e "grant all privileges on accounts.* TO 'admin'@'app01' identified by 'admin123' "
cd ../../..
mysql -u root -p"$DATABASE_PASS" accounts < src/main/resources/db_backup.sql
mysql -u root -p"$DATABASE_PASS" -e "FLUSH PRIVILEGES"
   ```
- Login to database and do a quick verification to see if SQL queries start a databases with role ,user and user_role tables.

```sh
mysql -u root -p"$DATABASE_PASS"
MariaDB [(none)]> show databases;
MariaDB [(none)]> use accounts;
MariaDB [(none)]> show tables;
exit
   ```
- Restart our mariadb server and logout

```sh
systemctl restart mariadb
logoutt
   ```

<br/>
<div align="right">
    <b><a href="#Project-01">↥ back to top</a></b>
</div>
<br/>

2. **Provisioning Memcached** <br>
- Login to memcached server, and switch to root user.

```sh
vagrant ssh mc01
sudo su -
   ```
- Update OS with latest patches and download epel repository.

```sh
yum update -y
yum install epel-release -y
   ```
   
 - Install *memcached* package
 
 ```sh
yum install memcached -y
   ```
 - Start/enable the memcached service and check the status of service.

 ```sh
systemctl start memcached
systemctl enable memcached
systemctl status memcache
   ```
- Run command below to enablememcached listen on TCP port 11211 and UDP port 11111.
 ```sh
memcached -p 11211 -U 11111 -u memcached -d
   ```
- Check if port is running and exit with command below.

 ```sh
ss -tunlp | grep 11211
exit
   ```
<br/>
<div align="right">
    <b><a href="#Project-01">↥ back to top</a></b>
</div>
<br/>

3. **Provisioning Rabbit MQ** <br>

- Login to Rabbit MQ server and switch to root user.

 ```sh
vagrant ssh rmq01
sudo su -
   ```
- Updating OS with latest patches and download epel repository.

 ```sh
yum update -y
yum install epel-release -y
   ```

- Install the following dependencies.

 ```sh
yum install wget -y
cd /tmp/
wget http://packages.erlang-solutions.com/erlang-solutions-2.0-1.noarch.rpm
sudo rpm -Uvh erlang-solutions-2.0-1.noarch.rpm
   ```
- Install RabbitMQ ithe command below.

```sh
curl -s https://packagecloud.io/install/repositories/rabbitmq/rabbitmq-server/script.rpm.sh | sudo bash
sudo yum install rabbitmq-server -y
   ```
- Start the rabbitmq service and check the status of service.

```sh
systemctl start rabbitmq-server
systemctl enable rabbitmq-server
systemctl status rabbitmq-server
   ```
- Create a *test* user with password *test*, also create *user_tag* for test user as *administrator* and restart rabbitmq service.

```sh
cd ~
echo "[{rabbit, [{loopback_users, []}]}]." > /etc/rabbitmq/rabbitmq.config
rabbitmqctl add_user test test
rabbitmqctl set_user_tags test administrator
systemctl restart rabbitmq-server
   ```
   
- Check the status to see whether rabbitmq server is running, if yes then exit and go to the net services.

![Project Image](project-image-url)

```sh
systemctl status rabbitmq-server
exit
   ```
   
<br/>
<div align="right">
    <b><a href="#Project-01">↥ back to top</a></b>
</div>
<br/>

4. **Provisioning Tomcat** <br>

- Login to *app01* server first, and switch to root user.

```sh
vagrant ssh app01
sudo su -
   ```
   
- Update OS with latest patches and download epel repository.

```sh
yum update -y
yum install epel-release -y
   ```
   
- Install dependencies for Tomcat server.

```sh
yum install java-1.8.0-openjdk -y
yum install git maven wget -y
   ```

- Switch to */tmp/* directory and download tomcat.

```sh
cd /tmp
wget https://archive.apache.org/dist/tomcat/tomcat-8/v8.5.37/bin/apache-tomcat-8.5.37.tar.
tar xzvf apache-tomcat-8.5.37.tar.gz
   ```
 
- Add tomcat user and copy data to tomcat home directory.
- Check the *new* user tomcat with *id tomcat* command.

```sh
useradd --home-dir /usr/local/tomcat8 --shell /sbin/nologin tomcat
   ```
   
- Copy your data to */usr/local/tomcat8* directory which is the home-directory for tomcat user.

```sh
cp -r /tmp/apache-tomcat-8.5.37/* /usr/local/tomcat8/
ls /usr/local/tomcat8
   ```
   
 - The currently root user has ownership of all files under */usr/local/tomcat8/* directory. change the ownership to tomcat user.


```sh
ls -l /usr/local/tomcat8/
chown -R tomcat.tomcat /usr/local/tomcat8
ls -l /usr/local/tomcat8/
   ```
   
- To setup *systemd* for tomcat, create a file with below content. After creating this file, start tomcat service with *systemctl start tomcat* and stop tomcat with *systemctl stop tomcat* commands. RUN the following commands to accomplish this task.



```sh
[Unit] 
Description=Tomcat 
After=network.target
[Service]
User=tomcat
WorkingDirectory=/usr/local/tomcat8 
Environment=JRE_HOME=/usr/lib/jvm/jre 
Environment=JAVA_HOME=/usr/lib/jvm/jre 
Environment=CATALINA_HOME=/usr/local/tomcat8 
Environment=CATALINE_BASE=/usr/local/tomcat8 
ExecStart=/usr/local/tomcat8/bin/catalina.sh run 
ExecStop=/usr/local/tomcat8/bin/shutdown.sh 
SyslogIdentifier=tomcat-%i
[Install] 
WantedBy=multi-user.target
   ```
   
- Enable tomcat service. The service name tomcat has to be same as given /etc/systemd/system/tomcat.service directory.

```sh
systemctl daemon-reload 
systemctl enable tomcat
systemctl start tomcat
systemctl status tomcat
   ```
 
![Project Image](project-image-url)

<br/>
<div align="right">
    <b><a href="#Project-01">↥ back to top</a></b>
</div>
<br/>
 
 
5. **Provisioning Nginx** <br>

- Login to *web01* server first, and switch to root user.

```sh
vagrant ssh web01
sudo su -
   ```

- update OS with latest patches run below command.


```sh
sudo apt update && sudo apt upgrade
   ```
   
- Install nginx.

```sh
sudo su -
apt install nginx -y
   ```
   
- Create nginx configuration file under directory */etc/nginx/sites-available/* with below content.

```sh
vi /etc/nginx/sites-available/vproapp
Content to add:
upstream vproapp {
server app01:8080;
}
server {
listen 80;
location / {
proxy_pass http://vproapp;
}
}
   ```
   
- Remove default nginx config file

```sh
rm -rf /etc/nginx/sites-enabled/default
   ```

- Create a symbolic link for our configuration file using default config location as below to enable our site. Then restart nginx server.

```sh
ln -s /etc/nginx/sites-available/vproapp /etc/nginx/sites-enabled/vproapp
systemctl restart nginx
   ```
   
<br/>
<div align="right">
    <b><a href="#Project-01">↥ back to top</a></b>
</div>
<br/>

## :rocket: App Deployment to Tomcat

Login in to *app01* and clone our source code in the In /tmp directory.

```sh
git clone https://github.com/devopshydclub/vprofile-project.git
ls
cd vprofile-project/
   ```

- let's update our configuration file (found under the cloned repository) that will be connect to our backend services db, memcached and rabbitmq service.

```sh
vi src/main/resources/application.properties
   ```
   
- From the application.properties file: let's make the these changes. First check DB configuration. Our db server is db01 , and we have admin user with password admin123 as we setup. For memcached service, hostname is mc01 and we validated it is listening on tcp port 11211. Fort rabbitMQ, hostname is rmq01 and we have created user test with pwd test.

```sh
JDBC Configutation for Database Connection
jdbc.driverClassName=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://db01:3306/accounts?useUnicode=true&characterEncoding=UTF-8&zeroDateTimeBehavior=convertToNull
jdbc.username=admin
jdbc.password=admin123
#Memcached Configuration For Active and StandBy Host
#For Active Host
memcached.active.host=mc01
memcached.active.port=11211
#For StandBy Host
memcached.standBy.host=127.0.0.2
memcached.standBy.port=11211
#RabbitMq Configuration
rabbitmq.address=rmq01
rabbitmq.port=5672
rabbitmq.username=test
rabbitmq.password=test
   ```
   
- Run *mvn install* command to create our artifact. Check the artifact in this location */tmp/vprofile-project/target/vprofile-v2.war*.


```sh
mvn install
   ```
   
- Stop the tomcat server and remove default tomcat app from our server.

```sh
systemctl stop tomcat
systemctl status tomcat
rm -rf /usr/local/tomcat8/webapps/ROOT
   ``` 
- Copy our artifact from */tmp/vprofile-project/target/vprofile-v2.war* to */usr/local/tomcat8/webapps/* directory as *ROOT.war* and start tomcat server

```sh
cd ..
cp target/vprofile-v2.war /usr/local/tomcat8/webapps/ROOT.war
systemctl start tomcat
ls /usr/local/tomcat8/webapps/
   ``` 
   
<br/>
<div align="right">
    <b><a href="#Project-01">↥ back to top</a></b>
</div>
<br/>

## :earth_africa: Verify from browser


- Login into *web01* (nginx) server, and run *ifconfig* to get its IP address. My case, the IP address of our web01 is : 192.168.56.11.

```sh
ifconfig
   ``` 
   
- Validate whether Nginx is running by entering *http://<IP_of_Nginx_server>* on your browser .


![Project Image](project-image-url)

- Validate Db connection using credentials by entering *admin_vp* for both username and password on the login page.

![Project Image](project-image-url)

- On the page, Click on RabbitMQ to validate the connection.

![Project Image](project-image-url)

- Validate Memcache connection by clicking MemCache.

![Project Image](project-image-url)

- Click on the user ID AND validate if data is coming from Database.

![Project Image](project-image-url) 

<br/>
<div align="right">
    <b><a href="#Project-01">↥ back to top</a></b>
</div>
<br/>

## CleanUp

- On the terminal, move to the directory carrying our vagrant file, and run the command below to destroy all virtual machines.


```sh
vagrant destroy
   ``` 
 ![Project Image](project-image-url)   
   
<br/>
<div align="right">
    <b><a href="#Project-01">↥ back to top</a></b>
</div>
<br/>

## :page_facing_up: Resources


<br/>
<div align="right">
    <b><a href="#Project-01">↥ back to top</a></b>
</div>
<br/>

## :star2: Acknowledgment


<br/>
<div align="right">
    <b><a href="#Project-01">↥ back to top</a></b>
</div>
<br/>



