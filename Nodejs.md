### NPM

* NPM is using http proxy from bash if running behind proxy on Linux, it will pick up http_proxy, https_proxy from shell
  env. Set up proxy for current user by putting content below in ~/.bash_profile:
  ```
  # proxy
export http_proxy=http://proxy.osumc.edu:8080
export HTTP_PROXY=http://proxy.osumc.edu:8080
export https_proxy=http://proxy.osumc.edu:8080
export HTTPS_PROXY=http://proxy.osumc.edu:8080
```

