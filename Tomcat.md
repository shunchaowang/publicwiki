## Set up tomcat 8.5 behind ssl reverse proxy (NetScaler)
In most enterprise network infrastructure there is a ssl reverse proxy to handle ssl and cname, the request will be
forwarded by the proxy to backend tomcat on bare http 8080. When deploying web apps to the tomcat, if there are
authorization/authentication redirections on https, tomcat will fall back to http of original hostname by default,
instead of https on cname. To accomplish https redirection on cname, more configurations should be added to Connector on
port 8080 in server.xml file.
```
proxyPort="443"
scheme="https"
secure="true"
proxyName="<cname>"
URIEncoding="UTF-8"
```
The final Connector should be similar like this:
```
<Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               proxyPort="443" scheme="https" secure="true" proxyName="<cname>"
               URIEncoding="UTF-8"
               redirectPort="8443" />
```
## Install tomcat 8.5 on RHEL 7
* Prerequisites
1. make sure jdk 1.8+ is installed `sudo yum install java-1.8.0-openjdk-devel`
* Install tomcat 8.5
1. create user from tomcat `sudo useradd -m -U -d /opt/tomcat -s /bin/false tomcat`
2. download tomcat 8.5 and put it under tomcat user's home
```
cd /tmp
wget http://www-us.apache.org/dist/tomcat/tomcat-8/v8.5.37/bin/apache-tomcat-8.5.37.zip
unzip apache-tomcat-*.zip
sudo mkdir -p /opt/tomcat
sudo mv apache-tomcat-8.5.37 /opt/tomcat/
```
3. create a soft link as a general entry for tomcat `sudo ln -s /opt/tomcat/apache-tomcat-8.5.37 /opt/tomcat/latest`
4. change permission and make tomcat runnable
```
sudo chown -R tomcat: /opt/tomcat
sudo sh -c 'chmod +x /opt/tomcat/latest/bin/*.sh'
```
* Create service
* Disable default tomcat 7
* Enable tomcat 8.5

