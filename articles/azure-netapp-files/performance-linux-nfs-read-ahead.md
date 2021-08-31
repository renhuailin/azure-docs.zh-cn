---
title: Azure NetApp 文件的 Linux NFS 预读最佳做法 - 会话槽和槽表条目 | Microsoft Docs
description: 介绍 Azure NetApp 文件的文件系统缓存和 Linux NFS 预读最佳做法。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: b-juche
ms.openlocfilehash: f87d2742ab34cdf5b6509e88b403240d388fa373
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113233456"
---
# <a name="linux-nfs-read-ahead-best-practices-for-azure-netapp-files"></a>Azure NetApp 文件的 Linux NFS 预读最佳做法

本文将帮助你了解 Azure NetApp 文件的文件系统缓存最佳做法。  

在应用程序发出 I/O 请求之前，NFS 预读预测性地请求来自文件的块。 它旨在提高客户端顺序读取吞吐量。  到目前为止，所有新式 Linux 分发版将预读值设置为等同于装载文件系统 `rsize` 的 15 倍。  

下表显示了每个给定 `rsize` 装载选项的默认预读值。

| 装载的文件系统 `rsize` | 块预读 |
|-|-|
| 64 KiB | 960 KiB |
| 256 KiB | 3,840 KiB |
| 1024 KiB | 15,360 KiB |

RHEL 8.3 和 Ubuntu 18.04 引入了可能会对客户端顺序读取性能产生负面影响的更改。  与早期版本不同，无论是否使用 `rsize` 装载选项，这些分发版都会将预读设置为默认值 128 KiB。 从具有较大预读值版本升级到默认值为 128 KiB 的版本会导致顺序读取性能下降。 但是，可以动态和持久地向上优化预读值。  例如，使用 SAS GRID 进行测试时发现，与 3,840 KiB、960 KiB 和 128 KiB 相比，15,360-KiB 的读取值最佳。  未运行超过 15,360 KiB 的足够测试，无法确定正面影响还是负面影响。

下表显示了每个当前可用分发版的默认预读值。

|     分发    |     发布    |     块预读    |
|-|-|-|
|     RHEL    |     8.3    |     128 KiB    |
|     RHEL    |     7.X、8.0、8.1、8.2    |     15 X `rsize`    |
|     SLES    |     12.X – 至少 15SP2    |     15 X `rsize`    |
|     Ubuntu    |     18.04 – 至少 20.04    |     128 KiB    |
|     Ubuntu    |     16.04    |     15 X `rsize`    |
|     Debian    |     至少 10    |     15 x `rsize`    |


## <a name="how-to-work-with-per-nfs-filesystem-read-ahead"></a>如何处理每个 NFS 文件系统的预读   

NFS 预读是在 NFS 文件系统的装入点定义的。 可以动态和持久查看和设置默认设置。  为方便起见，我们提供了 Red Hat 编写的以下 bash 脚本，用于查看或动态设置已装载的 NFS 文件系统的预读。

可以使用以下脚本动态定义每个 NFS 装载的预读，也可以永久使用此部分中所示的 `udev` 规则。  若要显示或设置已装载 NFS 文件系统的预读，可以将以下脚本保存为 bash 文件，修改文件的权限，使其成为可执行文件 (`chmod 544 readahead.sh`)，并按如下所示运行。 

## <a name="how-to-show-or-set-read-ahead-values"></a>如何显示或设置预读值   

若要显示当前预读值（返回的值以 KiB 为单位），请运行以下命令：  

`$ ./readahead.sh  show <mount-point>`   

若要为预读设置新值，请运行以下命令：   

`$ ./readahead.sh  show <mount-point> [read-ahead-kb]`
 
### <a name="example"></a>示例   

```
#!/bin/bash
# set | show readahead for a specific mount point
# Useful for things like NFS and if you do not know / care about the backing device
#
# To the extent possible under law, Red Hat, Inc. has dedicated all copyright
# to this software to the public domain worldwide, pursuant to the
# CC0 Public Domain Dedication. This software is distributed without any warranty.
# See <http://creativecommons.org/publicdomain/zero/1.0/>.
#

E_BADARGS=22
function myusage() {
echo "Usage: `basename $0` set|show <mount-point> [read-ahead-kb]"
}

if [ $# -gt 3 -o $# -lt 2 ]; then
   myusage
   exit $E_BADARGS
fi

MNT=${2%/}
BDEV=$(grep $MNT /proc/self/mountinfo | awk '{ print $3 }')

if [ $# -eq 3 -a $1 == "set" ]; then
   echo $3 > /sys/class/bdi/$BDEV/read_ahead_kb
elif [ $# -eq 2 -a $1 == "show" ]; then
   echo "$MNT $BDEV /sys/class/bdi/$BDEV/read_ahead_kb = "$(cat /sys/class/bdi/$BDEV/read_ahead_kb)
else
   myusage
   exit $E_BADARGS
fi
```

## <a name="how-to-persistently-set-read-ahead-for-nfs-mounts"></a>如何为 NFS 装载永久设置预读

若要为 NFS 装载永久设置预读设置，可以按如下方式编写 `udev` 规则：    

1. 创建并测试 `/etc/udev/rules.d/99-nfs.rules`：

    `SUBSYSTEM=="bdi", ACTION=="add", PROGRAM="/bin/awk -v bdi=$kernel 'BEGIN{ret=1} {if ($4 == bdi) {ret=0}} END{exit ret}' /proc/fs/nfsfs/volumes", ATTR{read_ahead_kb}="15380"`

2. 应用 `udev` 规则：   

    `$udevadm control --reload`

## <a name="next-steps"></a>后续步骤  

* [适用于 Azure NetApp 文件的 Linux 直接 I/O 最佳做法](performance-linux-direct-io.md)
* [适用于 Azure NetApp 文件的 Linux 文件系统缓存最佳做法](performance-linux-filesystem-cache.md)
* [适用于 Azure NetApp 文件的 Linux NFS 装载选项最佳做法](performance-linux-mount-options.md)
* [Linux 并发最佳做法](performance-linux-concurrency-session-slots.md)
* [Azure 虚拟机 SKU 最佳做法](performance-virtual-machine-sku.md) 
* [Linux 性能基准](performance-benchmarks-linux.md) 
