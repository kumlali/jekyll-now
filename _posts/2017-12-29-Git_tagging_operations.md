---
layout: post
title: Git tagging operations
---

List tags:
```bash
git tag
```
Create a tag:
```bash
git tag -a 1.0 -m "The first stable release."
```

Create a tag and take message from the given file:
```bash
git tag -F tag.md 1.0
```

Push tags:
```bash
git push --tags
```

Delete a tag(local and remote):
```bash
git tag -d 1.0
git push origin :1.0
```