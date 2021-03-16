# SailPoint (Identity IQ 8.1 Lab SetUp)

Main purpose of this guide, is to combine the resources used to setup the lab. In an all in one location. I tried to condense as much as posible, while providing links to the orginal guides followed. Hope It Helps! 

## Lab Enviroment
- Virtual Box
- Centos8
- MySQL 8
- Apache Tomcat 9
- IdentityIQ 8.1

## Install Apache Tomcat
[Link to resource that was followed for Apache Install](https://www.linode.com/docs/guides/apache-tomcat-on-centos-8/)

- `yum install java-1.8.0-openjdk-headless`
    
- `java -version`
    
- `yum install wget -y && sudo yum install tar`
- `yum install java-devel`
    
- `mkdir /usr/local/tomcat`
    
- `wget https://archive.apache.org/dist/tomcat/tomcat-9/v9.0.33/bin/apache-tomcat-9.0.33.tar.gz`
    
- `tar xvf apache-tomcat-9.0.33.tar.gz --strip-components=1 -C /usr/local/tomcat`
    
- `ln -s /usr/local/tomcat/apache-tomcat-9.0.33 /usr/local/tomcat/tomcat`
    

* * *

## Create Tomcat User

- `useradd -r tomcat`
- `chown -R tomcat:tomcat /usr/local/tomcat`

* * *

## Create a new systemd service file

In a text editor open /etc/systemd/system/tomcat.service, in the text editor add the following details:

- `vim /etc/systemd/system/tomcat.service`

* * *
```
\[Unit\]
Description=Tomcat Server
After=syslog.target network.target

\[Service\]
Type=forking
User=tomcat
Group=tomcat

Environment=JAVA_HOME=/usr/lib/jvm/jre
Environment='JAVA_OPTS=-Djava.awt.headless=true'
Environment=CATALINA_HOME=/usr/local/tomcat
Environment=CATALINA_BASE=/usr/local/tomcat
Environment=CATALINA_PID=/usr/local/tomcat/temp/tomcat.pid
Environment='CATALINA_OPTS=-Xms512M -Xmx1024M'
ExecStart=/usr/local/tomcat/bin/catalina.sh start
ExecStop=/usr/local/tomcat/bin/catalina.sh stop

\[Install\]
WantedBy=multi-user.target
```
* * *

- `systemctl daemon-reload`
    
- `systemctl enable tomcat`
    
- `systemctl start tomcat`
    
- `firewall-cmd --permanent --zone=public --add-port=8080/tcp`
    
- `firewall-cmd --reload`
    

* * *

## Installing SQL

[Link to resource that was use for SQL Install](https://linuxize.com/post/how-to-install-mysql-on-centos-8/)

- `dnf install @mysql:8.0`
- `systemctl enable --now mysqld`
- `systemctl status mysqld`

* * *

## Reset MySQL user Password
There will be several prompts when running the command, you can accept the default values or configure how you wish.

**Reset sql login, with the following cmd:**
- `mysql\_secure\_installation`

**In my case I set them to:**
- root
- Cyberark1

**Test SQL Login**

- `mysql -u root -p`
- `quit`
* * *

## Move and Extract SailPoint Files
After moving the IdentityIQ.zip to your Machine. Create a folder to unzip the idenityiq.zip file to, in my case, I created "IDIQ_Home" in my users(root) home directory.

- `mkdir IDIQ_HOME`
- `mkdir /usr/local/tomcat/webapps/identityiq/`
- `mv identityiq.war /usr/local/tomcat/webapps/identityiq/`
- `cd /usr/local/tomcat/webapps/identityiq/`
- `jar -xvf identityiq.war`
- `chmod +x WEB-INF/bin/iiq`
 cd WEB-INF/bin/
 
**Generates DB Scipts**
Now that "WEB-INF/bin/iiq" is executable
 - `cd WEB-INF/bin/`
- `./iiq schema`

After running the above commands the scritps can be found at:
- `/usr/local/tomcat/webapps/identityiq/WEB-INF/database`
- `ls`

The script I used was 8.1 mysql, if you installed a different version of SQL at the beginging please adjust accordingly 

**Login into mysql and Run script**
This will do all the heavy lifting for you

- `mysql -u root -p`
- `source create_identityiq_tables-8.1.mysql;`
- `quit`

* * *

## Update iiq.properties
- `vim /usr/local/tomcat/webapps/identityiq/WEB-INF/classes/iiq.properties`

Here we have to change several lines In iiq.properties
In vim you can enable line numbers by **:set number**
* * *
- `line 68 dataSource.username=username`
- `line 69 dataSource.password=randompassword`

- `Line 150 pluginsDataSource.username=username`
- `Line 151 pluginsDataSource.password=randompassword`
* * *
### Lines Updated
- `line 68 dataSource.username=root`
- `line 69 dataSource.password=Cyberark1`
* * *
- `150 pluginsDataSource.username=root`
- `151 pluginsDataSource.password=Cyberark1`
* * *

### While still inside iiq.properties change follwing lines:
- `73 dataSource.url=jdbc:mysql://localhost/identityiq?useServerPrepStmts=true&tinyInt1isBit=true&useUnicode=true&characterEncoding=utf8&useSSL=false`

- `155 pluginsDataSource.url=jdbc:mysql://localhost/identityiqPlugin?useServerPrepStmts=true&tinyInt1isBit=true&useUnicode=true&characterEncoding=utf8&useSSL=false`

* * *
**Adding "&serverTimezone=UTC" to the end of the line** 
- `73 dataSource.url=jdbc:mysql://localhost/identityiq?useServerPrepStmts=true&tinyInt1isBit=true&useUnicode=true&characterEncoding=utf8&useSSL=false&serverTimezone=UTC`

- `155 pluginsDataSource.url=jdbc:mysql://localhost/identityiqPlugin?useServerPrepStmts=true&tinyInt1isBit=true&useUnicode=true&characterEncoding=utf8&useSSL=false&serverTimezone=UTC`

**Exit VIM and save the file**
- `:wq!`
* * *

## Import init.xml
 **Location /usr/local/tomcat/webapps/identityiq/WEB-INF/bin**
 
- `./iiq console`
- `import init.xml`
- `quit`

* * *

## Restart Tomcat
- `systemctl restart tomcat`

## Open Login Page
- `http://yourVMIpaddress:8080/identityiq/login.`
- USERNAME = spadmin
- PASSWORD = admin
