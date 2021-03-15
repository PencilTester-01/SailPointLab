## Install Apache Tomcat
- `sudo yum install java-1.8.0-openjdk-headless`
- `java -version`
- `sudo yum install wget -y && sudo yum install tar`
- `sudo mkdir /usr/local/tomcat`

- `wget https://archive.apache.org/dist/tomcat/tomcat-9/v9.0.33/bin/apache-tomcat-9.0.33.tar.gz`

- `sudo tar xvf apache-tomcat-9.0.33.tar.gz --strip-components=1 -C /usr/local/tomcat`

- `sudo ln -s /usr/local/tomcat/apache-tomcat-9.0.33 /usr/local/tomcat/tomcat`

* * *
## Create Tomcat User
- `sudo useradd -r tomcat`
- `sudo chown -R tomcat:tomcat /usr/local/tomcat`
* * *
## Create a new systemd service file
/etc/systemd/system/tomcat.service, in the text editor of your choice with the following details:
* * *
`[Unit]
Description=Tomcat Server
After=syslog.target network.target

[Service]
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

[Install]
WantedBy=multi-user.target`
* * *
- `sudo systemctl daemon-reload`

- `sudo systemctl enable tomcat`
- `sudo systemctl start tomcat`

- `sudo firewall-cmd --permanent --zone=public --add-port=8080/tcp`
- `sudo firewall-cmd --reload`
* * *
## Test Tomcat!
http://example.com:8080/
* * *
