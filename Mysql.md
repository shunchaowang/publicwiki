## apt install mysql-server 5.7
`sudo apt update && sudo apt install mysql-server`
after the installation, root password needs to be set by the procedure below:
1. run `mysql_secure_installation` to remove testing database;
1. log into to mysql as super user running `sudo mysql -uroot` # must use sudo, or will get 'access deined';
2. run `update mysql.user set authentication_string=password(''), plugin='mysql_native_password' where user='root';` to remove
 root password, or the password preferred.
3. `flush privileges;` and `exit`
## disable validate_password plugin for test purpuse
* login to server as root `mysql -h localhost -u root -p`
* run `uninstall plugin validate_password` to uninstall the plugin
* command `install plugin validate_password` can be used to install the plugin
