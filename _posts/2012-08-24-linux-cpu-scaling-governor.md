---
layout: post
title: "Linux: CPU scaling governor"
description: ""
category: Linux
tags: [Linux, cpu]
---
{% include JB/setup %}


## CPU 정보

	cat /proc/cpuinfo

## 2번째 코어 살리기

	echo 1 > /sys/devices/system/cpu/cpu1/online 
	grep "processor" /proc/cpuinfo
