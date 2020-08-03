### Request university issued certificate
- Generate signing request
    `openssl req -new -newkey rsa:2048 -nodes -keyout <sever-name>-key.pem -out <server-name>.csr.pem`
    - Country: US
    - State or Province Name: OH
    - Locality Name: Columbus
    - Organization Name: The Ohio State University Medical Center
    - Organizational Unit Name: Biomedical Informatics
    - Common Name: code.bmi.osumc.edu
- Submit request throught https://go.osu.edu/new-certificate
### gitlab - code.bmi.osumc.edu
#### start/stop gitlab service
- stop gitlab: sudo gitlab-ctl stop
- stop apache: sudo systemctl stop httpd
- stop shibd: sudo systemctl stop shibd
---
- start shibd: sudo systemctl start shibd
- start apache: sudo systemctl start httpd
- start gitlab: sudo gitlab-ctl start
---
#### https clone with shibboleth authentication
1. Make sure nginx['enable'] = false in /etc/gitlab/gitlab.rb
2. Set the username of the non-bundled web-server user, should have a web_server['external_users'] = ['apache'] in
   /etc/gitlab/gitlab.rb
3. Make sure workhorse is using tcp instead of socket for apache in /etc/gitlab/gitlab.rb.
    | gitlab_workhorse['listen_network'] = "tcp"
    | gitlab_workhorse['listen_addr'] = "127.0.0.1:8181"
4. sudo gitlab-ctl reconfigure to make gitlab configured
#### Install gitlab ci runner on CentOS
If the git server is running using self-signed certificate, the ca must be trusted to register the runner:
> `sudo bash -c "cat <ca file> >> /etc/ssl/certs/ca-certificates.crt"`
#### CentOS yum Update Package with enablerepo options
- Update specific gitlab RPMs
`yum --enablerepo=gitlab_gitlab-ce update <package1> [package2] [package3]...`
- Update everything including gitlab RPMs, this will include every part of the OS
`yum --enablerepo=gitlab_gitlab-ce update`
> multiple --enablerepo statements can be used separated by space
### Gitlab Set Up Auto Deployment using crontab
#### create script to deploy the war file in /opt/deploy-artifact.sh
```
#!/bin/sh

src=/home/gitlab-runner/dev.war
dest=/usr/share/tomcat/webapps/dev.war
destExploded=/usr/share/tomcat/webapps/dev
owner=tomcat
DATE=$(date)

# exits if src not existing
if [ ! -f ${src} ]; then
    echo "${DATE}: ${src} does not exist, skip."
    exit 1
fi
# if existing
if [ -f ${dest} ]; then
    echo "${DATE}: ${dest} exists, compare ${src} with ${dest}..."
    # compare md5 between src and desk, exit if equal
    srcMd5=`md5sum ${src} | awk '{ print $1 }'`
    destMd5=`md5sum ${dest} | awk '{ print $1 }'`
    if [ "${srcMd5}" == "${destMd5}" ]; then
        echo "${DATE}: ${desk} is identical with ${src}, skip the deployment."
        exit 1
    else
        echo "${DATE}: ${desk} is not identical with ${src}, start the deployment."
        # stop tomcat
        systemctl stop tomcat
        # remove exploded artifact if existing
        if [ -e ${destExploded} ]; then
            rm -rf ${destExploded}
        fi
        # copy new war file
        cp ${src} ${dest}
        # change owner and group
        chown tomcat:tomcat ${dest}
        # wait for 30 seconds
        # sleep 30s
        # start tomcat
        systemctl start tomcat
    fi
else # desk doesn't exist yet
    echo "${DATE}: ${desk} does not exist yet, start the deployment."
    # stop tomcat
    systemctl stop tomcat
    # copy new war file
    cp ${src} ${dest}
    # change owner and group
    chown tomcat:tomcat ${dest}
    # wait for 30 seconds
    # sleep 30s
    # start tomcat
    systemctl start tomcat
fi
```
#### set up cron task
- Add an entry in cron list
`sudo crontab -e`
add the line like this `*/5 * * * * /opt/deploy-artifact.sh >> /var/log/deploy-artifact.log`
to run the script every 5 minutes. The schedule means minute(0-59), hour(0-23), day of month(1-31), month(1-12), day
of the week(0-7)

