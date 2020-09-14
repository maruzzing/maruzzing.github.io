---
layout: post
title: Amazone Linux의 TimeZone 변경하기
date: 2019-09-20
comments: true
categories: [Study, etc]
tags: [AWS, Amazone Linux, TimeZone]
excerpt: 아마존 EC2 인스턴스는 기본적으로 UTC으로 표시된다. 이것을 KST로 변경하는 방법은 아래와 같다.
---

아마존 EC2 인스턴스는 기본적으로 UTC으로 표시된다. 이것을 KST로 변경하는 방법은 아래와 같다.

```bash
sudo cat /etc/localtime
sudo rm /etc/localtime
sudo ln -s /usr/share/zoneinfo/Asia/Seoul /etc/localtime
```

끝!
