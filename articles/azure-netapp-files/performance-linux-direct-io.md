---
title: 适用于 Azure NetApp 文件的 Linux 直接 I/O 最佳做法 | Microsoft Docs
description: 介绍适用于 Azure NetApp 文件的 Linux 直接 I/O 以及相关的最佳做法。
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
ms.openlocfilehash: 6497d91169c7530251a473e8e06c26ce411e0e6a
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113233457"
---
# <a name="linux-direct-io-best-practices-for-azure-netapp-files"></a>适用于 Azure NetApp 文件的 Linux 直接 I/O 最佳做法

本文帮助你了解适用于 Azure NetApp 文件的直接 I/O 最佳做法。  

## <a name="direct-io"></a>直接 I/O

 存储性能基准测试中最常使用的参数是直接 I/O。 FIO 和 Vdbench 支持该参数。 DISKSPD 为内存映射 I/O 的类似构造提供支持。 使用直接 I/O 时，将会绕过文件系统缓存并避免直接内存访问复制操作，使存储测试变得快速简单。  

使用直接 I/O 参数可使存储测试变得轻松。 不会从客户端上的文件系统缓存读取任何数据。 因此，测试会真正对存储协议和服务本身施加压力，而不考虑内存访问速度。 此外，由于不执行 DMA 内存复制操作，从处理的角度看，读写操作非常高效。 

使用 Linux `dd` 命令作为示例工作负载。 在没有可选 `odirect` 标志的情况下，将从 Linux 缓冲区缓存为 `dd` 生成的所有 I/O 提供服务。 不会从存储中检索对已在内存中的块执行的读取。 导致缓冲区缓存未命中的读取内容最终会使用 NFS 预读从存储中读取，根据装载点 `rsize` 和客户端预读可调参数等因素，这种读取效果各不相同。 当写入内容通过缓冲区缓存发送时，它们将使用后写机制，该机制未经优化，会使用大量的并行度将数据发送到存储设备。 你可能会尝试运行两个独立的 I/O 流，一个 `dd` 用于读取，一个 `dd` 用于写入。 但事实上，未经优化的操作系统更有利于写入而不是读取，并使用更多的写入并行度。

除数据库以外，少数应用程序也使用直接 I/O。 不过，这些应用程序选择利用大内存缓存的优势进行重复读取，并使用后写缓存进行异步写入。 简而言之，如果要合成的应用程序使用文件系统缓存，则使用直接 I/O 会将测试转化为微基准测试。  

下面是支持直接 I/O 的一些数据库： 

* Oracle 
* SAP HANA
* MySQL（InnoDB 存储引擎）
* RocksDB
* PostgreSQL
* Teradata

## <a name="best-practices"></a>最佳实践 

使用 `directio` 进行测试是了解存储服务和客户端限制的极佳方式。 若要更好地了解应用程序本身的行为方式（如果应用程序不使用 `directio`），也应该通过文件系统缓存来运行测试。

## <a name="next-steps"></a>后续步骤  

* [适用于 Azure NetApp 文件的 Linux 文件系统缓存最佳做法](performance-linux-filesystem-cache.md)
* [适用于 Azure NetApp 文件的 Linux NFS 装载选项最佳做法](performance-linux-mount-options.md)
* [适用于 Azure NetApp 文件的 Linux 并发最佳做法](performance-linux-concurrency-session-slots.md)
* [Linux NFS 文件预读最佳做法](performance-linux-nfs-read-ahead.md)
* [Azure 虚拟机 SKU 最佳做法](performance-virtual-machine-sku.md) 
* [Linux 性能基准](performance-benchmarks-linux.md) 
