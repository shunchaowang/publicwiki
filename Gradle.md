#### Set up Gradle proxy glabally
> Create a file gradle.properties in GRADLE_HOME if not existed, gradle home usually is ~/.gradle.
> Put proxy setting in gradle.properties:
```
systemProp.https.proxyHost=proxy.osumc.edu
systemProp.https.proxyPort=8080
systemProp.http.proxyHost=proxy.osumc.edu
systemProp.http.proxyPort=8080
systemProp.https.nonProxyHosts=*.osumc.edu|localhost
```
