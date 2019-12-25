---
layout: post
title: IDEA 远程debug
category: idea
tags: [java,idea]
excerpt: 通过IDEA远程调试服务器代码
---

## 远程debug

## 1. tomcat 的catalina.sh中添加配置 
```bash
CATALINA_OPTS="-Xdebug  -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=63403"
```
## 2. IDEA中 添加Remote Tomcat
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191111190214177.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2VxcXRr,size_16,color_FFFFFF,t_70)
### 3. 端口检查
```
telnet ip port 端口测试
``` 