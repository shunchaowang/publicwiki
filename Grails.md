#### Set up Grails proxy glabally
> Create a file ProxySettings.groovy in GRAILS_HOME, grails home usually is ~/.grails.
> Put proxy setting in ProxySettings.groovy:
`client=['http.proxyHost':'myproxy', 'http.proxyPort':'myport', 'http.proxyUser':'myusername', 'http.proxyPassword':'mypass', 'http.nonProxyHosts':'localhost']
currentProxy='client'`

