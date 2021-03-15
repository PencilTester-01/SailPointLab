#Install Apache Tomcat
- `sudo yum install java-1.8.0-openjdk-headless`
- `java -version`
- `sudo yum install wget -y && sudo yum install tar`
- `sudo mkdir /usr/local/tomcat`

- `wget https://archive.apache.org/dist/tomcat/tomcat-9/v9.0.33/bin/apache-tomcat-9.0.33.tar.gz`

- `sudo tar xvf apache-tomcat-9.0.33.tar.gz --strip-components=1 -C /usr/local/tomcat`

- `sudo ln -s /usr/local/tomcat/apache-tomcat-9.0.33 /usr/local/tomcat/tomcat`
