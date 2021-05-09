[##](##) Configure Apache for React App with Router
Use VirtualHost and Directory Rewrite Rules, create a <site>.conf file like below:
```
<VirtualHost *:80>
    ServerName auth-portal-test.bmi.osumc.edu
    ServerAlias auth-portal-test.bmi.osumc.edu
    DocumentRoot /var/www/bmi-oauth2-app/public
    ErrorLog /var/www/bmi-oauth2-app/log/error.log
    CustomLog /var/www/bmi-oauth2-app/log/requests.log combined

    <Directory /var/www/bmi-oauth2-app/public>
        <IfModule mod_rewrite.c>
          RewriteEngine On
          RewriteBase /
          RewriteRule ^index\.html$ - [L]
          RewriteCond %{REQUEST_FILENAME} !-f
          RewriteCond %{REQUEST_FILENAME} !-d
          RewriteCond %{REQUEST_FILENAME} !-l
          RewriteRule . /index.html [L]
        </IfModule>
    </Directory>
</VirtualHost>
```
