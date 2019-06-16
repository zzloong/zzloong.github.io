---
title: Linux lvm 分区知识笔记
date: 2019-04-23 23:09:28
tags: [分区,lvm]
categories: Linux
keywords: lvm,partion
---

盘面上可以细分出扇区（Sector）与柱面（Cylinder)两种单位，其中扇区每个为512bytes那么大。

通常所说的”硬盘分区”就是指修改磁盘分区表，它定义了”第n个磁盘块是从第 x个柱面到第y个柱面”.因此，当系统要读取第n个磁盘块时，就是去读硬盘上第x个柱面到第y个柱面的信息.

<!-- more -->

整块磁盘的第一个扇区特别重要，因为它记录了整块磁盘的重要信息：

1. 主引导分区（Master Boot Record, MBR）：可以安装引导加载程序的地方，有446bytes.
2. 分区表（partition table）：记录整块磁盘分区的状态，有64bytes。

## 磁盘分区表（partion table）

在分区表所在的64bytes容量中，总共分为四组记录区。每组记录区记录了该区段的起始与结束的柱面号码。

- 其实所谓的分区只是针对那个64bytes的分区表进行设置而已。
- 硬盘默认的分区表仅能写入四组分区信息
- 四组分区信息我们称为主（Primary）或者扩展（Extended）分区。
- 分区最小单位为柱面（cylinder）。

分区的优点：
1. 数据安全
2. 有助于数据读取的速度和性能

扩展分区的目的是使用额外的扇区记录分区信息，扩展分区本身并不能拿来格式化。由扩展分区切出来的分区，就被称为逻辑分区（logical partition）。逻辑分区的设备名称号码由5号开始。

主分区、扩展分区和逻辑分区的定义：
- 主分区与扩展分区最多可以有4个（磁盘限制）
- 扩展分区最多只有1个（操作系统限制）
- 逻辑分区是由扩展分区持续切割出来的分区
- 能够被格式化后作为数据访问的分区为主分区与逻辑分区，扩展分区无法格式化。
- 逻辑分区的个数依操作系统而不同，SATA硬盘则有11个逻辑分区（5号到15号）。

分区是个很麻烦的东西，因为它是以柱面为单位的“连续”磁盘空间，且扩展分区又是类似独立的磁盘空间。

扩展分区是不能直接用的，他是以逻辑分区的方式来使用的，所以说扩展分区可分成若干逻辑分区。 他们的关系是包含的关系，所有的逻辑分区都是扩展分区的一部分。

## 磁盘分区

### LVM卷管理

```shell
disk=/dev/vdb
pvcreate $disk # 磁盘还没有分主分区或者扩展分区，就可以直接创建物理卷（Physical volume (PV) ）了，物理卷的名字就是磁盘的名字/分区的名字
vgcreate ci-vg $disk # 创建卷组Volume group (VG)，卷组名为 ci-vg1
lvcreate -L 100G  -n app_data ci-vg # or lvcreate -L 100G  --name app_data ci-vg，将ci-vg 卷组中的 100G 空间划分为逻辑卷Logical volume (LV)，逻辑卷名为 app_data
# lvcreate -l +100%FREE -n app_data ci-vg 将卷组百分百的空间都划分给 app_data 这个逻辑卷
lvdisplay # 查看逻辑卷路径
mkfs.ext3 /dev/ci-vg/app_data # 格式化
mount /dev/ci-vg/app_data /data # 挂载，前提是要有 /data 目录
df -Th # 查看
```

接着，为了开机自动挂载，执行：

```shell
echo "mount /dev/ci-vg/apkg /apkg" >> /etc/rc.d/rc.local
```

这个命令在有些情况不会生效，开机自启挂载磁盘，稳妥的方式推荐编辑 `/etc/fstab` 文件：
```shell
/dev/ci-vg/app_data /data ext3 defaults 0 0
```

### 分区常用命令

- lsblk：查看磁盘分区情况 ★★★
- vgdisplay: 查看卷组信息
- vgs： 查看卷组信息，简略
- fdisk -l：查看系统内分区信息

## 扩容

```
pvcreate /dev/vdc # 新增磁盘vdc，创建为物理卷
vgextend ci-vg1 /dev/vdc # 将新增的物理卷添加到已有的逻辑卷组中
lvextend -l +100%FREE /dev/ci-vg/data_app # # 用 lvextend 将 /dev/ci-vg1/apkg 所在卷组所有剩余空间都分配给了它
resize2fs /dev/ci-vg/data_app # # 磁盘格式是 ext2 ext3 ext4 使用 resize2fs， xfs使用 xfs_growfs 对扩容后的 LV 格式大小调整
```

### 扩容参考

- [手把手教你给 CentOS 7 添加硬盘及扩容(LVM)](http://aurthurxlc.github.io/Aurthur-2017/Centos-7-extend-lvm-volume.html)
- [Linux LVM分区之VG扩容、LV扩容、LV缩减、LVM快照](https://www.dwhd.org/20150521_225146.html)
- [Linux LVM 动态扩容（在线）](https://blog.lutty.me/linux/2017-04/linux-lvm-resize.html)

## FAQ

### Q1：发现开机启动时，`rc.local`没有自启动执行
尝试了`chmod +x /etc/rc.d/rc.local`之后，问题依旧，排除了权限的问题。
编辑`rc.local`文件，在`touch /var/locak/subsys/local`下一行，加上`sleep 10`，问题解决。

### Q2: `-bash: pvcreate: command not found`

通过`yum install lvm2`安装，支持命令。

## 参考
- [LVM (简体中文)](https://wiki.archlinux.org/index.php/LVM_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))
- [Linx卷管理详解](https://blog.csdn.net/wuweilong/article/details/7565530)
- [Linux逻辑卷详解总结](http://blog.chinaunix.net/uid-20696246-id-1892246.html)
- [Linux 磁盘和分区](https://gtcsq.readthedocs.io/en/latest/linux_tools/disk_note.html)
- [Linux LVM简明教程](https://linux.cn/article-3218-2.html)

开机挂载
- [CentOS 6.3开机自动挂载磁盘和文件夹](https://blog.csdn.net/DayDreamingBoy/article/details/8278841) fstab 文件格式介绍