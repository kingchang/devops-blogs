---
title: "Linux 使用 parted 製作硬碟分割"
date: 2019-06-27T08:11:08+08:00
draft: true
---

因 AWS EC2 在虛擬主機的硬體提供支援中有分 EBS 跟 instance store，使用 instance store 會比較便宜，缺點就是每次shutdown 後，存放在instance store 的資料會被清空，故每次重新啟動後都需要實作硬碟分割，相關的方式如下

1. 登入 EC2 的 Linux 主機

    使用 putty 或 mobaxterm 用 key 認證的方式連到 AWS 的 Server 中，如下圖

2. 編輯 /etc/fstab

   ```linux
      vim /etc/fstab
   ```
    
![fstab content](/fstab_sample.png)
     
    
3. 使用 parted 切 swap 跟 data 的分割區

```bash

    # parted
GNU Parted 3.2
Using /dev/nvme0n1
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) mklabel
New disk label type? gpt
(parted) p
Model: Amazon EC2 NVMe Instance Storage (nvme)
Disk /dev/nvme0n1: 200GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt

(parted) free
Model: Amazon EC2 NVMe Instance Storage (nvme)
Disk /dev/nvme0n1: 200GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:
Number  Start   End    Size   File system  Name  Flags
        17.4kB  200GB  200GB  Free Space

(parted) mkpart primary

File system type?  [ext2]? linux-swap
Start? 1GB
End? 30GB

(parted) mkpart primary

File system type?  [ext2]? xfs
Start? 30GB
End? 200GB
(parted) p
Model: Amazon EC2 NVMe Instance Storage (nvme)
Disk /dev/nvme0n1: 200GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system     Name     Flags
 1      1000MB  30.0GB  29.0GB  linux-swap(v1)  primary
 2      30.0GB  200GB   170GB   xfs             primary

(parted) exit
 
(parted) quit
Information: You may need to update /etc/fstab.

```

4. 使用 lvm 建立相關的記錄
    
```linux
   mkswap /dev/nvme0n1p1
   swapon -a /dev/nvme0n1p1
   vim /etc/fstab

LABEL=cloudimg-rootfs	/	 ext4	defaults,discard	0 0
#cdf3fcb0-11d9-42c1-b588-73a4ee2258f0
/dev/nvme0n1p1 none swap defaults,discard 0 0

pvcreate /dev/nvme0n1p2
vgcreate vg_wildfly /dev/nvme0n1p2
lvcreate -n var -L 50G vg_wildfly
lvcreate -n opt -L 50G vg_wildfly
mkfs.xfs /dev/mapper/vg_wildfly-opt
lsblk
blkid
systemctl daemon-reload
vim /etc/fstab
mount -a

LABEL=cloudimg-rootfs	/	 ext4	defaults,discard	0 0
#cdf3fcb0-11d9-42c1-b588-73a4ee2258f0
/dev/nvme0n1p1 none swap defaults,discard 0 0
UUID=a26ea291-0f31-4756-8ca3-f783438c2ef1 /opt xfs defaults,nofail 0 2


```