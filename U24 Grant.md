### Elasticsearch
To install Elasticsearch using yum, we have to set up proxy if the server is hehind the firewall
1. Import PGP key
`sudo rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch --httpproxy proxy.osumc.edu --httpport 8080`
2. Add repo metadata for elasticsearch
create a elasticsearch.repo under /etc/yum.repos.d/ with the content:
```
[elasticsearch-6.x]
name=Elasticsearch repository for 6.x packages
baseurl=https://artifacts.elastic.co/packages/6.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
```
3. Install elasticsearch
Add proxy to /etc/yum.conf if the server is behind the firewall
* `proxy=http://proxy.osumc.edu:8080`
* `sudo yum install elasticsearch -y`
4. Start elasticsearch
`sudo systemctl daemon-reload `
`sudo systemctl enable elasticsearch`
`sudo systemctl start elasticsearch`
5. Test if elasticsearch is successfully installed
`curl -v --noproxy localhost, http://localhost:9200` # rest call listens on 9200, http listens on 9300, --noproxy
localhost, makes curl doesn't use proxy to access localhost
It should present below if successful
```
* About to connect() to localhost port 9200 (#0)
*   Trying ::1...
* Connected to localhost (::1) port 9200 (#0)
> GET / HTTP/1.1
> User-Agent: curl/7.29.0
> Host: localhost:9200
> Accept: */*
>
< HTTP/1.1 200 OK
< content-type: application/json; charset=UTF-8
< content-length: 435
<
{
  "name" : "mOwvzWE",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "J8xtrBF4Rumq1DxshcKVAQ",
  "version" : {
    "number" : "6.2.2",
    "build_hash" : "10b1edd",
    "build_date" : "2018-02-16T19:01:30.685723Z",
    "build_snapshot" : false,
    "lucene_version" : "7.2.1",
    "minimum_wire_compatibility_version" : "5.6.0",
    "minimum_index_compatibility_version" : "5.0.0"
  },
  "tagline" : "You Know, for Search"
}
* Connection #0 to host localhost left intact
```
#### Change binding host
Add configurations below to /etc/elasticsearch/elasticsearch.yml file:
```
network.bind_host: 0.0.0.0
node.master: true
node.data: true
transport.host: localhost
transport.tcp.port: 9300
```
### Kibana
1. Create a kibana.repo under /etc/yum.repos.d/
```
[kibana-6.x]
name=Kibana repository for 6.x packages
baseurl=https://artifacts.elastic.co/packages/6.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
```
2. Install
sudo yum install kibana -y
3. Enable service and start
```
sudo systemctl daemon-reload
sudo systemctl enable kibana
sudo systemctl start kibana
```
5. Kibana runs on 5601 and connect to elasticsearch running on localhost:9200 by default
### X-Pack
Download x-pack zip file to install locally
`curl -o x-pack-6.2.2.zip https://artifacts.elastic.co/downloads/packs/x-pack/x-pack-6.2.2.zip`
#### Install X-Pack for elasticsearch
`sudo /usr/share/elasticsearch/bin/elasticsearch-plugin install file:///home/OSUMC.EDU/wan119/x-pack-6.2.2.zip`
#### Install X-Pack for Kibana
`sudo /usr/share/kibana/bin/kibana-plugin install file:///home/OSUMC.EDU/wan119/x-pack-6.2.2.zip`
#### Updating & Removing x-pack Plugins, others are similar
`sudo /usr/share/kibana/bin/kibana-plugin remove x-pack`
#### Disable security on elasticsearch after removing x-pack
By default elasticsearch has security enabled for xpack, add this line to /etc/elasticsearch/elasticsearch.yml to
disable it, otherwise you will see 'Login is currently disabled. Administrators should consult the Kibana logs for more
details.' when accessing the server.
`xpack.security.enabled: false`
#### Install & Configure Nginx on RHEL 7
1. Add nginx repo
Create a file /etc/yum.repos/nginx.repo, put content as
```
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/mainline/centos/7/$basearch/
gpgcheck=0
enabled=1
```
2. Install nginx
`sudo yum update && sudo yum install nginx`
3. Configure nginx
4. Configure firewall to open port 80 and 443 locally using firewall-cmd
5. Test it
`sudo ss -tulpn` to check the list of ports listening
#### Install Python 3.7 on RHEL 7
1. Python installation required GCC compiler, install these prerequisites
`sudo yum install gcc openssl-devel bzip2-devel libffi-devel`
2. Download Python 3.7.0 source
`wget https://www.python.org/ftp/python/3.7.0/Python-3.7.0.tgz`
3. Untar the package
`tar xvf Pythong-3.7.0.tgz`
5. Install
```
cd Python-3.7.0
sudo ./configure --enable-optimizations
sudo make altinstall
```
7. Test
`python3.7 -V`
### cBioPortal
#### Deployment & Installation

