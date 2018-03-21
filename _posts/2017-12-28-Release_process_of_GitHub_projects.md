---
layout: post
title: Release process of GitHub projects
---

When the code is ready to be released:
* Add change log to `CHANGELOG.md`
* Commit and push it to GitHub
* Create release tag(e.g. 1.1) by providing changes added to `CHANGELOG.md`:
    * Create `tag.md` file and copy target release changes from `CHANGELOG.md`.
    * `git tag -F tag.md 1.1`
    * `git push --tags`
* Visit the tag's page in GitHub
* Choose "Add release notes"
* Enter the tag name into "Release title" (e.g. 1.1)
* Enter the content of `tag.md` into "Describe this release". Preview it to last check.
* Enter the content of `tag.md` into "Describe this release". Preview it to last check.