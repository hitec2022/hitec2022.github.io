---
layout: default
title: virtualbox 리눅스 디스크 늘이기
nav_order: 1
parent: 기술팁
date: 2023-01-31 12:00:00
lastmod: 2023-01-31 12:00:00
---

# virtualbox 리눅스 디스크 늘이기

virtualbox 에 우분투 리눅스가 설치되어 있는데 디스크가 FULL 이 난 경우

## We need to resize the logical volume to use all the existing and free space of the volume group

```sh
$ lvm
lvm> lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
lvm> exit
```

## And then, we need to resize the file system to use the new available space in the logical volume

```sh
$ resize2fs /dev/ubuntu-vg/ubuntu-lv
```
## Finally, you can check that you now have available space:

```sh
$ df -h
```