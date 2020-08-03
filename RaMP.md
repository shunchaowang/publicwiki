#### Run locally in RStudio
```
install.packages('devtools')
library(devtools)
install_github("mathelab/RAMP-DB")
library(RaMP)
RaMP::runRaMPapp(conpass="mysql_password")
```
#### Issues
RMySQL package is not compatible with MySQL 8, but sometimes homebrew will upgrade mysql from 5.x to 8.x. To run RaMP,
mysql has to be downgrade to 5.x, the steps to perform:
```
mysql.server stop # if started now
brew uninstall mysql
brew install mysql@5.7
brew link --force mysql@5.7
brew pin mysql@5.7
```
Then the RaMP package should be able to install.

