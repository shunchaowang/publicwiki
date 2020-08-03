### Set Up Development Environment
All components are listed:
1. dbmi-annotator: `https://github.com/dbmi-pitt/dbmi-annotator` should use my repo instead
2. Elasticsearch 1.7 : `https://www.elastic.co/downloads/past-releases/elasticsearch-1-7-5` direct dl link: `https://download.elastic.co/elasticsearch/elasticsearch/elasticsearch-1.7.5.tar.gz`
3. Annotator Store: `https://github.com/ningyifan/annotator-store`
4. annotator.js: `https://github.com/dbmi-pitt/annotator.js` should use my repo instead
#### Postgres 9.3 Database
1. create role dbmiannotator 
   - after installation of postgresql, run command: `psql postgres` # postgres has a builtin user
   - `create role dbmiannotator WITH LOGIN PASSWORD 'dbmiannotator';` to create the role. ( `\du` show all users )
2. create database dbmiannotator
   - `create database dbmiannotator;` to create the database. `\list` shows all dbs, `\connect dbmiannotator` to connect to the db, `\dt` show all tables.
3. Grant privileges
> `\connect dbmiannotator`
> `GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO dbmiannotator;`
> `GRANT USAGE, SELECT ON ALL SEQUENCES IN SCHEMA public TO dbmiannotator;`
5. create table schema
   - command line: `psql -f rdb-postgres-schema.sql` or psql prompt: `\i rdb-postgres-schema.sql`
6. initialize plugins information
   - command line: `psql -f rdb-postgres-initial.sql` or psql prompt: `\i rdb-postgres-initial.sql`
#### Elastic Search 1.7
1. download software andd untar it to whatever path, for me it's ~/apps/elasticsearch-1.7.5
2. es env variable: `export ES_HOME=~/apps/elasticsearch-1.7.5`
3. add es to path: `export PATH=$ES_HOME/bin:$PATH`
4. start elasticsearch `elasticsearch `, should listen on 9200, verify by access `http://localhost:9200/`
#### Annotator Store 
> annotator store is based on Python (Python 2 >=2.6 or Python 3 >=3.3), and depends on elastic search 1.7.x
1. install pip and virtualenv: `easy_install pip`, `easy_install virtualenv` 
2. under annotator-store path, enable virtualenv
- `sudo virtualenv pyenv`
- `source pyenv/bin/activate`
- `sudo pip install -e .[flask]` # make sure bash is used, otherwise will fail
3. set up config
- `cp annotator.cfg.example annotator.cfg`
4. run application
- `python run.py` # app should be accessed `http://localhost:5000` 
#### Mac installs Apache2 through homebrew
> Mac ships with an apache2.4 and is ready to use, but configs get overriden every time Mac udpated, we use homebrew
> Apache2 instead.
1. Install Xcode if haven't, open Xcode once to accept Terms and Conditions.
2. Install Xcode Command Line Tools `xcode-select --intall`
3. Disable Mac Apache2
- Stop apache2 `sudo apachectl stop`
- Unlaunch daemon `sudo launchctl unload -w /System/Library/LaunchDaemons/org.apache.httpd.plist 2>/dev/null`
4. Homebrew install Apache
- Install homebrew `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`
- Add apache tap `brew tap homebrew/apache`
- Install apache2 `brew install httpd24 --with-privileged-ports --with-http2`
5. Apache2 settings
#### Apache Rewrite
1. apache needs these modules enabled: `mod_rewrite, mod_proxy, mod_proxy_http, mod_headers`
2. Make sure apache2 main config (`httpd.conf` mostly, mac is `/usr/local/etc/httpd/httpd.conf`) contains directory settings for document root as below:
    ```
    <Directory />
        AllowOverride none
        Require all granted
    </Directory>
    
    <Directory "/usr/local/var/www">
        Options FollowSymLinks Indexes
        AllowOverride None
        Require all granted
    </Directory>
    
    Include /usr/local/etc/httpd/extra/httpd-vhosts.conf
    ```
    
3. Add a VirtualHost config to apache config like below, (mac should `/usr/local/etc/httpd/extra/httpd-vhost.conf`):
   
   ```
   <VirtualHost *:80>

    ServerAdmin shunchao.wang@osumc.edu
    ServerName localhost
    DocumentRoot /usr/local/var/www

    RewriteEngine on

    # For most configuration files from conf-available/, which are
    # enabled or disabled at a global level, it is possible to
    # include a line for only one particular virtual host. For example the
    # following line enables the CGI configuration for this host only
    # after it has been globally disabled with "a2disconf".
    #Include conf-available/serve-cgi-bin.conf

    <Directory /usr/local/var/www >
        Options All
        Require all granted
        AllowOverride All
        Order allow,deny
        Allow from all
    </Directory>


    ## dbmi-annotator config ##########################################

    # stylesheet folder relocate at Document root(dailymed, PMC, wiley)
    RewriteRule /dbmiannotator/dailymed(.*) http://localhost/DDI-labels/dailymed$1 [P]
    RewriteRule /dbmiannotator/PMC(.*)_files/(.*) http://localhost/PMC/PMC$1_files/$2 [P]

    RewriteRule /dbmiannotator$ http://localhost:3000/dbmiannotator [P]
    RewriteRule /dbmiannotator/(.*) http://localhost:3000/dbmiannotator/$1 [P]

    ProxyPass /annotatorstore   http://localhost:5000/
    RewriteRule /annotatorstore(.*) http://localhost:5000$1 [P]

    ## Domeo config ###################################################

    #Load function to unescape strings and name it unencode
    RewriteMap unencode int:unescape
    RewriteRule ^/proxy/(htt.*)$ ${unencode:$1} [P]
    
    # RewriteCond ${unencode:${unencode:%{HTTP_REFERER}}} ^http:\/\/.*\/proxy/(http:\/\/(([a-zA-\Z0-9-]+\.?)*[a-zA-Z0-9-]+\.?)(:\d+)?)($|\/(\(([^\\/]+\/)*))([^\/]*)$
    # RewriteRule ^/proxy/(.*)$ %1/%6$1 [L]

    # RewriteCond ${unencode:${unencode:%{HTTP_REFERER}}} ^http:\/\/.*\/proxy/(http:\/\/(([a-zA-\Z0-9-]+\.?)*[a-zA-Z0-9-]+\.?)(:\d+)?).*?$
    # RewriteRule ^/(.*) %1/$1 [L]

    ## Logs ###################################################
    LogLevel alert rewrite:trace2

    </VirtualHost>
    ```

3. restart apache: `sudo apachectl restart`
#### annotator.js
1. `npm install` to build the project
2. build the package: `make` or `make pkg/annotator.min.js` to just build the main annotator bundle
3. once the build is successful, copy the package to dbminannotator: 
    > `cp <annotator.js.path>/pkg/annotator.min.js <dbminannotator.path>/public/dbmiannotator/js/`
#### DBMI Annotator
1. `npm install` to build the project
2. install browserify `npm install -g browserify`
3. create config.js `cp config/config.smaple.js config/config.js`
    > make sure config.postgres connection is meeting your environment
4. compile js packages (run every time updating config.js) `browserify app.js -o public/dbmiannotator/js/app.bundle.js`
5. run app `nodemon server.js` then access url: localhost/dbmiannotator (`npm install -g nodemon` if necessary)
    > if app throws database authentication error, check pg_hba.conf to replace all peer/ident with md5 or trust and
    restart postgres service;
    > if app throws database permission error, rerun grant statements of postgres steps;
### Set Up Production Environment
    > to run the app background on production, `nohup node server.js > output.log &`
### Restart Server after an OS Patch
All commands should run under the path `~/annotation-press`
Elastic Search
```
cd elasticsearch-1.7.5
bin/elasticsearch &
```
Annotator Store
```
cd annotator-store
sudo virtualenv pyenv
source pyenv/bin/activate
sudo pip install -e .[flask]
python run.py &
```
DBMI Annotator
```
cd dbmi-annotator
nohup node server.js > output.log &
```

