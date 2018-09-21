---
title: Ubuntu服务器启用swap虚拟内存
date: 2018-09-21 22:54:38
tags: 笔记
---

![](/images/kernel.jpg)

1. 查看当前虚拟内存： `cat /proc/swaps`
2. 创建swap文件：`sudo dd if=/dev/zero of=swaps bs=512 count=8388616`；其中swaps大小为：`bs*count=4G`
3. 通过`mkswap`命令把上一步创建的文件做成swap分区：`mkswap /path/to/swaps`
4. 查看内核参数：`cat /proc/sys/vm/swappiness`
5. 修改内核参数：`sudo vim /etc/sysctl.conf` 将 `vm.swappiness`修改为`60`（表示`100% -60% = 40%` 物理内存时启用虚拟内存）
6. 启用分区：`sudo swapon /path/to/swaps`
7. 修改以使重启启用分区： `sudo vim /etc/fstab` 添加`/path/to/swaps  swap     swap  defaults 0   0`
8. done.
