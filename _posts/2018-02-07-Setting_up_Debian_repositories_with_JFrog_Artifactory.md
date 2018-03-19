---
layout: post
title: Setting up Debian repositories with JFrog Artifactory
---

While I was searching for how I use Artifactory as Debian repository, I came across official Artifactory documentation: [How do I cache artifacts from a remote Debian repository?](https://jfrog.com/knowledge-base/how-do-i-cache-artifacts-from-a-remote-debian-repository/). But, it did not work because there is no `apt-add-repository` command as documentation mentioned. I had to figure out the correct way by myself and following steps worked for me.

## Resources
* [How do I cache artifacts from a remote Debian repository?](https://jfrog.com/knowledge-base/how-do-i-cache-artifacts-from-a-remote-debian-repository/)
* [Setting up a Debian repository with JFrog Artifactory in less than one minute](https://jfrog.com/screencast/setting-up-a-debian-repository-with-jfrog-artifactory-in-less-than-one-minute/)
* [Debian stable updates](https://wiki.debian.org/StableUpdates/)
* [Debian security updates](https://www.debian.org/security/)

## Current Setup
Operating system:
```bash
root@debian:~# cat /etc/os-release
PRETTY_NAME="Debian GNU/Linux 9 (stretch)"
NAME="Debian GNU/Linux"
VERSION_ID="9"
VERSION="9 (stretch)"
ID=debian
HOME_URL="https://www.debian.org/"
SUPPORT_URL="https://www.debian.org/support"
BUG_REPORT_URL="https://bugs.debian.org/"
```

Default `/etc/apt/sources.list`:
```bash
root@debian:~# cat /etc/apt/sources.list
deb http://deb.debian.org/debian stretch main
deb http://deb.debian.org/debian stretch-updates main
deb http://security.debian.org stretch/updates main
```

## Let's Do It!
Create Debian remote repositories in Artifactory(http://repo.mycompany.com):
* Repository Key: `deb.debian.org`, URL: http://deb.debian.org
* Repository Key: `security.debian.org`, URL: http://security.debian.org/debian-security

Remove default `/etc/apt/sources.list` and create our own:
```bash
# I do know I could use '>' instead of adding extra line to remove the file ;)
root@debian:~# rm /etc/apt/sources.list \
  && echo 'deb http://repo.mycompany.com/artifactory/deb.debian.org stretch main' >> /etc/apt/sources.list \
  && echo 'deb http://repo.mycompany.com/artifactory/deb.debian.org stretch-updates main' >> /etc/apt/sources.list \
  && echo 'deb http://repo.mycompany.com/artifactory/security.debian.org stretch/updates main' >> /etc/apt/sources.list
```

Test it:
```bash
root@debian:~# apt-get update
Ign:1 http://repo.mycompany.com/artifactory/deb.debian.org stretch InRelease
Get:2 http://repo.mycompany.com/artifactory/deb.debian.org stretch-updates InRelease [91.0 kB]
Get:3 http://repo.mycompany.com/artifactory/security.debian.org stretch/updates InRelease [63.0 kB]
Get:4 http://repo.mycompany.com/artifactory/deb.debian.org stretch Release [118 kB]
Get:5 http://repo.mycompany.com/artifactory/deb.debian.org stretch-updates/main amd64 Packages [8384 B]
Get:6 http://repo.mycompany.com/artifactory/security.debian.org stretch/updates/main amd64 Packages [339 kB]
Get:7 http://repo.mycompany.com/artifactory/security.debian.org stretch/updates/non-free amd64 Packages [1208 B]
Get:8 http://repo.mycompany.com/artifactory/security.debian.org stretch/updates/contrib amd64 Packages [1305 B]
Get:9 http://repo.mycompany.com/artifactory/deb.debian.org stretch Release.gpg [2434 B]
Get:10 http://repo.mycompany.com/artifactory/deb.debian.org stretch/main amd64 Packages [9531 kB]
Fetched 10.2 MB in 9s (1063 kB/s)
Reading package lists... Done
```

Use in Dockerfile:
```Dockerfile
# Artifactory can be used as private Docker registry, as well.
FROM repo.mycompany.com/debian:stretch

RUN rm /etc/apt/sources.list \
  && echo 'deb http://repo.mycompany.com/artifactory/deb.debian.org stretch main' >> /etc/apt/sources.list \
  && echo 'deb http://repo.mycompany.com/artifactory/deb.debian.org stretch-updates main' >> /etc/apt/sources.list \
  && echo 'deb http://repo.mycompany.com/artifactory/security.debian.org stretch/updates main' >> /etc/apt/sources.list \
  && apt-get update && apt-get install -y git curl && rm -rf /var/lib/apt/lists/*
```

```bash
docker build \
  --build-arg HTTP_PROXY="http://proxy.mycompany.com:8080" \
  --build-arg NO_PROXY=".mycompany.com,.sock,localhost,127.0.0.1,::1" \
  -t debianrepotest .
```