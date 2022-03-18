## Maven build error
if this error occurs when run `mvn clean package`
> Failed to execute goal org.apache.maven.plugins:maven-compiler-plugin:3.3:compile (default-compile) on project
there are 2 possible causes:
1. missing maven-compiler-plugin source and target settings, add these to the effective `pom.xml`, adjusting the
   version.
```
<properties>
		<maven.compiler.source>1.8</maven.compiler.source>
		<maven.compiler.target>1.8</maven.compiler.target>
	</properties>
```
2. if a custom maven `settings.xml` is being used, try to make sure the `java.<version>.home` is correctly configured
   based on the build environment.

