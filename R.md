## RHEL 7 Install R
1. Install EPEL (Extra Packages for Enterprise Linux)
```
sudo yum install -y epel-release
sudo yum update -y
```
> EPEL contains R package but is missing 3 dependencies, which have to be installed manually.
2. Set up proxy for yum
> yum can be set proxy in its conf file `/etc/yum.conf`
> add this line to /etc/yum.conf `proxy=http://proxy.osumc.edu:8080`
3. Set up proxy for login user
```
export http_proxy=http://proxy.osumc.edu:8080
export https_proxy=http://proxy.osumc.edu:8080
```
4. Install R dependency
```
wget http://mirror.centos.org/centos/7/os/x86_64/Packages/texinfo-tex-5.1-5.el7.x86_64.rpm # download texinfo-tex
wget http://mirror.centos.org/centos/7/os/x86_64/Packages/texlive-epsf-doc-svn21461.2.7.4-38.el7.noarch.rpm # dependency
of texinfo-tex
wget http://mirror.centos.org/centos/7/os/x86_64/Packages/texlive-epsf-svn21461.2.7.4-38.el7.noarch.rpm # dependency of
texinfo-tex
sudo yum localinstall texlive-epsf-doc-svn21461.2.7.4-38.el7.noarch.rpm
sudo yum localinstall texlive-epsf-svn21461.2.7.4-38.el7.noarch.rpm
sudo yum localinstall texinfo-tex-5.1-5.el7.x86_64.rpm # has to be the last, it depends on previous two
```
5. Install R
`sudo yum install R -y`
6. Run R
`sudo R`

## RHEL 7 Install R Package
### When installing R package behind a proxy, shell proxy has to be set up if run R console as normal user
* to set up http proxy
> export http_proxy='http://proxy.osumc.edu:8080'
* to set up https proxy
> export https_proxy='http://proxy.osumc.edu:8080'
* install the package
run command `install.packages('[name]')` in R console to install package [name]
### When installing R package, will get the ERROR: 'configure' exists but is not executable, TMPDIR should be changed.
1. Create a tmp folder `/opt/r_tmp` with execution permission `sudo chmod 777 /opt/r_tmp`
2. Enter R console with sudo `sudo R`; 
3. In R console set TMPDIR: `Sys.setenv(TMPDIR="/opt/r_tmp")`
4. Set tempdir (it's not needed now, noted for future reference)
```
tempdir <- function() "/opt/r_tmp"
unlockBinding("tempdir", baseenv())
utils::assignInNamespace("tempdir", tempdir, ns="base", envir=baseenv())
assign("tempdir", tempdir, baseenv())
lockBinding("tempdir", baseenv())
```
> Sys.getenv() will print all environment variables, Sys.getenv('PWD') will print current working directory.
> tempdir() will print temp dir used by R.
### If `sudo R` starts a root R console, the system proxy can be set up using command
* in R console, set up proxy: `Sys.setenv(http_proxy="http://proxy.osumc.edu:8080")`
## Install Shiny Package
run this command to install the latest shiny server
```
sudo su - -c "R -e \"install.packages('shiny', repos='https://cran.rstudio.com/')\""
```
## Install Shiny Server
these commands to install shiny server
```
wget https://download3.rstudio.org/centos6.3/x86_64/shiny-server-1.5.7.907-rh6-x86_64.rpm
sudo yum install --nogpgcheck shiny-server-1.5.7.907-rh6-x86_64.rpm
```
## Configure Shiny Server
