# Install Docker
# Configure Docker
# Commandline tools
* docker images commands
    + list all images
    > docker images
    + commit a new image from container
    > docker commit <container_name> <image_name>
    + delete an image
    > docker rmi <image_name>
* docker containers commands
    + create and launch container
    > docker run -tid --restart always -v <host_volumn:container_volumn> --hostname=<host_name can be the same with
    container_name> --cap-add SYS_ADMIN -p <host_port>:<container_port> <multiple port mappings> --name <container_name>
    <image_name>
    examle 
    > docker run -tid --restart always -v /sys/fs/cgroup:/sys/fs/cgroup:ro --hostname=wan119-8 --cap-add SYS_ADMIN -p
    2222:22 -p 9090:8080 --name wan119-8  tomcat8-oauth2-service
    + operate container
       - get into a running container
       > docker exec -it <container_name> /bin/bash
       - launch a stopped container
       > docker start <container_name>
       - stop a launched container
       > docker stop <container_name>
       - delete a container
       > docker rm <container_name> -f
* port mapping between docker container and host
When `docker run` a container, the ports can be published to the host by specifying `-p <host_port>:<container_port>`.
Eg, ssh is running on port 22 on the container, when `-p 2222:22` is passed on `docker run`, to ssh into the container
`ssh -p 2222 <username on the container>@<hostname of the host>`;
to scp file onto the container `scp -P 2222 <username on the container>@<hostname of the host>`.

