---
title: Move a PR made in a fork into a branch in the target repository
date: 2024-00-20 08:39:00
excerpt: 'how to move the changes made in your fork into a branch in the target repo'
categories:
  - blog
tags:
  - git
header:
  image: '/images/header.jpg'
---
## Intro

Imagine this situation: you work hard on a forked repo during days, making lot of changes. Finally, you submitted the code just to realize that, for some reason (failures on target repository pipeline, etc), forks are not the way to go in order to have your precious changes on the target repo and you are requested to please, move your change into a branch inside the target repo.

If that your case, the following steps could be helpful.

## Steps

1. Clone the main repo

```bash
git clone https://github.com/owner/repo.git
cd repo
```

2. Add your fork as a remote repository

```bash
git remote add fork git@github.com:username/repo.git
```

Note that we added the ssh url here just for convenience.

3. fetch the fork

```bash
git fetch fork
```

4. create a new branch in the main repo

```bash
git checkout -b new-branch-name
```

5. merge your fork changes into the newly created branch

```bash
git merge fork/your-branch
```

6. push that branch into the remote repo

```bash
git push origin new-branch-name
```

7. from there, you can open a PR in the target repo.
