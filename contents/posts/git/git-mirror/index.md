---
title: "AWS EC2 Pinpoint 설치 with docker"
description:
date: 2022.07.29
tags:
- git
- github
- repository
--- 

# Git 저장소 미러링하기

Date: 2022.07.29  
Tags: #git #github #repository

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
