### Mac has issues when installing rgdal package in rstudio, this will fix it in homebrew
```
brew update
brew install gdal
in R console 
install.packages("rgdal")
```
### RHEL 7 will fail on installing rgdal package due to the dependency of gdal, gdal has to be installed using yum from epel repo
* `sudo yum install epel-release` to install epel repo
* `sudo yum install gdal-devel gdal proj proj_devel proj-epsg proj-nad` to install the dependencies needed
* then use commands below to install rgdal
```
sudo R
install.packages('rgdal')
```
### RHEL 7 will fail on installing ggmap package due to the dependencies issues
* `sudo yum install libjpeg-turbo-devel libpng-devel ` to install all dependencies
