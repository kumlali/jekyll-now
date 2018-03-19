---
layout: post
title: Customizing MANIFEST.MF of Maven Projects Built by Jenkins
---

While building a Maven project on Jenkins, environment variables(e.g. GIT_COMMIT, SVN_REVISION) set by Jenkins can be added to `MANIFEST.MF` file residing in JAR and WAR files.

For example, if we want to add Jenkins build number, Jenkins build url, Git commit id and custom implementation version to `MANIFEST.MF` of a JAR file, we can use:

```xml
<plugin>
  <artifactId>maven-jar-plugin</artifactId>
  <configuration>
    <archive>
      <manifest>
        <addDefaultImplementationEntries>true</addDefaultImplementationEntries>
        <addDefaultSpecificationEntries>true</addDefaultSpecificationEntries>
      </manifest>
      <manifestEntries>
        <Jenkins-Build-Number>${BUILD_NUMBER}</Jenkins-Build-Number>
        <Jenkins-Build-Url>${BUILD_URL}</Jenkins-Build-Url>
        <Git-Commit>${GIT_COMMIT}</Git-Commit>
        <Implementation-Version>${project.version}.${GIT_COMMIT}.${BUILD_NUMBER}</Implementation-Version>
      </manifestEntries>
    </archive>
  </configuration>
</plugin>
```

and the result is:
```
Manifest-Version: 1.0
Implementation-Title: My Project
Implementation-Version: 1.0.0-SNAPSHOT.18b47fff41d30758a2756c13844026d
 fee323b50.5
Jenkins-Build-Number: 8
Archiver-Version: Plexus Archiver
Built-By: akumlali
Specification-Vendor: Pivotal Software, Inc.
Specification-Title: My Project
Implementation-Vendor-Id: com.mycompany.myproject
Spring-Boot-Version: 1.3.3.RELEASE
Implementation-Vendor: Pivotal Software, Inc.
Git-Commit: c70b1edd54d17c49dcc9f858e3dba4bdcdcee74a
Main-Class: org.springframework.boot.loader.JarLauncher
Start-Class: tr.com.mkk.gms.SpringBootApplicationStarter
Jenkins-Build-Url: http://jenkins.mkk.com.tr/jenkins/job/gms-build/8/
Created-By: Apache Maven 3.5.0
Build-Jdk: 1.8.0_161
Specification-Version: 1.0.0-SNAPSHOT
```

For web application projects (WAR), we would use `maven-war-plugin` with the same configuration, instead.

## Resources
* [Using Jenkins Build Version With Maven](https://goo.gl/K878DQ)