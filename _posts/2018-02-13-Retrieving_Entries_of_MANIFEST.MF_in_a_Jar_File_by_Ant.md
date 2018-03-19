---
layout: post
title: Retrieving Entries of MANIFEST.MF in a Jar File by Ant
---

If we have `myapp.jar` file that has following `MANIFEST.MF` inside,

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


then we can read its properties with following Ant script snippet:

    <!--
      Source: https://goo.gl/4xT7Fy

      Loads entries from a manifest file.
      @jar     The jar from where to read
      @prefix  A prefix to prepend
    -->
    <macrodef name="loadmf">
        <attribute name="jar"/>
        <attribute name="prefix" default=""/>
        <sequential>
            <loadproperties>
                <!-- Load the manifest entries -->
                <zipentry zipfile="@{jar}" name="META-INF/MANIFEST.MF"/>
                <!-- Add the prefix -->
                <filterchain>
                    <prefixlines prefix="@{prefix}"/>
                </filterchain>
            </loadproperties>
        </sequential>
    </macrodef>
    
    <!-- 
      Source: https://stackoverflow.com/a/37896164/5903564 

      Returns substring of given string.
    -->
    <macrodef name="getsubstring">
      <attribute name="src"/>
      <attribute name="start"/>
      <attribute name="length"/>
      <attribute name="result"/>
      <sequential>
        <loadresource property="@{result}">
          <string value="@{src}}" />
            <filterchain>
              <tokenfilter>
                <replaceregex pattern="^.{@{start}}(.{@{length}}).*" replace="\1" />
              </tokenfilter>
            </filterchain>
        </loadresource>
      </sequential>
    </macrodef>
    
    
    <target name="create-version">    
        <!-- Read manifest entries -->
        <loadmf jar="target/myapp.jar" prefix="MYAPP-" />

        <!-- Get the first 6 characters of git SHA -->
        <getsubstring src="${MYAPP-Git-Commit}" start="0" length="6" result="short-git-commit"/>

        <property name="version" value="${MYAPP-Specification-Version}.${short-git-commit}.${MYAPP-Jenkins-Build-Number}" />
        <echo>Version: ${version}</echo>
    </target>