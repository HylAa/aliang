---
title: MinIO - MinIO上传文件错误解决办法（Linux）
tags:
  - Linux
categories: []
date: 2021-01-22 15:15:00
updated: 2021-01-22 15:15:00
top_img: https://res.cloudinary.com/dt3vcmqdt/image/upload/v1615251663/pm_images_f130_7ae19e4d941edc26f34fad9830d3f130_o0snbb.jpg
cover: https://res.cloudinary.com/dt3vcmqdt/image/upload/v1615251663/pm_images_f130_7ae19e4d941edc26f34fad9830d3f130_o0snbb.jpg
---

# minio上传下载文件失败：

错误消息：

```rust
The difference between the request time and the server's time is too large.
```

原因：linux服务器时区的问题。

# 解决方案：

一、查看系统时间、硬件时间

```cpp
1.# date // 查看系统时间
2.# hwclock // 查看硬件时间
```

二、时间服务器上的时间同步的方法
 安装ntpdate工具

```bash
1.# yum -y install ntp ntpdate
```

设置系统时间与网络时间同步

```css
2.# ntpdate cn.pool.ntp.org
```

将系统时间写入硬件时间

```bash
3.# hwclock --systohc
```