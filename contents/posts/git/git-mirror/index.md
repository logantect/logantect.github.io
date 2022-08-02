---
title: "Git 저장소 복제 하기"
description:
date: 2022-07-29
tags:
- git
- github
- repository
--- 

원격리포지터리를 옮기고 싶은 경우  `--mirror` 옵션을 사용하여 깃 커밋 히스토리 그대로 푸시하는 방법을 알아보자.  


### 1. 기존 프로젝트 클론
```bash
$ git clone --bare https://github.com/exampleuser/old-repository.git
```

### 2. 신규 프로젝트 `--mirror` 옵션으로 푸시
```bash
$ cd old-repository.git
$ git push --mirror https://github.com/exampleuser/new-repository.git
```

### 3. 기존 프로젝트 삭제
```bash
$ cd ..
$ rm -rf old-repository.git
```

## Reference
---
* [Duplicating a repository](https://docs.github.com/en/repositories/creating-and-managing-repositories/duplicating-a-repository)
