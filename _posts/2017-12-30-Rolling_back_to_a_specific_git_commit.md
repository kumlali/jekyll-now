---
layout: post
title: Rolling back to a specific git commit
---

WARNING: This hint is dangerous in a collaborative environment: you're rewriting history

References:
* https://stackoverflow.com/a/14091182/5903564
* https://stackoverflow.com/a/4372501/5903564

```bash
$ git clone $URL
$ cd $PROJECT_NAME
$ git reset --hard $SHA1
$ git push -f origin $BRANCH
```

Sample
```bash
$ git clone https://github.com/kumlali/handyjenkins.git
$ cd handyjenkins
$ git reset --hard cdc581a07e5dd6a751042b5a29fcf3808c07c308
$ git push -f origin master
```