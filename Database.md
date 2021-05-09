# MySQL
## Set Character Set to UTF8 for MySQL 5.5+
* Use following command to check current character set
    * `show variable like 'character_set_%'` 
* Edit /etc/mysql/my.cnf on Linux and Program Files/XXX/my.ini on windows
    * Set character set for mysql client
    > Locate client section
    
    > [client]
    
    > default-character-set=utf8
    
    * Set character set for mysqld
    > Locate server section
    
    > [server]
    
    > default-storage-engine=INNODB
    
    > character-set-server=utf8
    
    > collation-server=utf8_uncode_ci
    
* Change database character set to utf8
    > `ALTER DATABASE <NAME> CHARACTER SET utf8 COLLATE utf8_unicode_ci;` 
    
* Change table character set to utf8
    > `ALATER TABLE <NAME> CONVERT TO CHARACTER SET utf8 COLLATE utf8_unicode_ci;`
## Import/Export SQL file from/into database
* By mysql command
    > `mysql -u <username> -p<passowrd> <database_name> <file>` to import <file> into <database_name>
    > `mysqldump -u <username> -p<password> <database_name> > <file>` to export <database_name> to <file>
* By sql shell
    ```
    use <database_name>;
    source <file>;
    ```
