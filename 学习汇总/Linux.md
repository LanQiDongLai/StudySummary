# Linux

## 文件系统

###  Linux文件目录结构

```bash
ls -l /
```

![image-20230719221948715](/home/LanQiDongLai/.config/Typora/typora-user-images/image-20230719221948715.png#pic_left)

/

> 根目录，Linux所有系统的文件都挂载于此目录下

1. /home

   > 家目录，每个用户都对应一个子文件夹来存储用户数据
   >
   > 数据包括但不限于
   >
   > 	1. 用户的个人文件与文档
   > 	1. 应用程序的配置文件（这些通常以点开头来隐藏自己的可见性）
   > 	1. 桌面文件夹，下载文件夹，公共文件夹

2. /sys

   > 系统硬件信息和内核信息目录，该目录是一个虚拟文件系统，它提供了对系统内核和硬件的访问接口

   1. /sys/block
   2. /sys/bus
   3. /sys/class
   4. /sys/devices
   5. /sys/firmware

3. /proc
