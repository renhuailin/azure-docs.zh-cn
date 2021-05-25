---
title: Kerberos 对 Azure NetApp 文件 NFSv4.1 卷的性能影响 | Microsoft Docs
description: 介绍可用的安全选项、已测试的性能向量以及 Kerberos 对 Azure NetApp 文件 NFSv4.1 卷的预期性能影响。
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
ms.date: 02/18/2021
ms.author: b-juche
ms.openlocfilehash: e9054f11c8f55e9fe00266840a9f6e257f14e659
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101744571"
---
# <a name="performance-impact-of-kerberos-on-azure-netapp-files-nfsv41-volumes"></a>Kerberos 对 Azure NetApp 文件 NFSv4.1 卷的性能影响

Azure NetApp 文件支持使用 AES-256 加密[在 Kerberos 模式（krb5、krb5i 和 krb5p）下加密 NFS 客户端](configure-kerberos-encryption.md)。 本文介绍 Kerberos 对 NFSv4.1 卷的性能影响。 

## <a name="available-security-options"></a>可用的安全选项 

当前可用于 NFSv4.1 卷的安全选项如下所示： 

* **sec=sys** 通过 AUTH_SYS 使用本地 UNIX UID 和 GID 来对 NFS 操作进行身份验证。
* **sec=krb5** 使用 Kerberos V5 代替本地 UNIX UID 和 GID 来对用户进行身份验证。
* **sec=krb5i** 使用 Kerberos V5 进行用户身份验证，并使用安全校验和执行 NFS 操作的完整性检查，以防止数据篡改。
* **sec=krb5p** 使用 Kerberos V5 进行用户身份验证和完整性检查。 它加密 NFS 流量以防止流量嗅探。 此选项是最安全的设置，但也涉及最大的性能开销。

## <a name="performance-vectors-tested"></a>已测试的性能向量

本部分介绍各种 `sec=*` 选项对单个客户端性能的影响。

* 在两个级别上测试了性能影响：低并发（低负载）和高并发（I/O 和吞吐量的上限）。  
* 测试了三种类型的工作负载：  
    * 小型操作随机读取/写入（使用 FIO）
    * 大型操作顺序读取/写入（使用 FIO）
    * 由 git 等应用程序生成的元数据繁重工作负载

## <a name="expected-performance-impact"></a>预期性能影响 

有两个重要方面：轻型负载和上限。 以下列表按安全设置和方案介绍了性能影响安全设置。 所有比较都是针对 `sec=sys` 安全参数进行的。 该测试是使用单个客户端在单个卷上完成的。 

krb5 的性能影响：

* 低并发 (r/w)：
    * 顺序延迟增加了 0.3 毫秒。
    * 随机 I/O 延迟增加了 0.2 毫秒。
    * 元数据 I/O 延迟增加了 0.2 毫秒。
* 高并发 (r/w)： 
    * 最大顺序吞吐量不受 krb5 影响。
    * 对于纯读取工作负载，最大随机 I/O 降低了 30%，随着工作负载转移为纯写入，总体影响降至零。 
    * 最大元数据工作负载降低了 30%。

krb5i 的性能影响： 

* 低并发 (r/w)：
    * 顺序延迟增加了 0.5 毫秒。
    * 随机 I/O 延迟增加了 0.2 毫秒。
    * 元数据 I/O 延迟增加了 0.2 毫秒。
* 高并发 (r/w)： 
    * 无论工作负载如何混合，最大顺序吞吐量总体上都降低了 70%。
    * 对于纯读取工作负载，最大随机 I/O 降低了 50%，随着工作负载转移为纯写入，总体影响降至 25%。 
    * 最大元数据工作负载降低了 30%。

krb5p 的性能影响：

* 低并发 (r/w)：
    * 顺序延迟增加了 0.8 毫秒。
    * 随机 I/O 延迟增加了 0.2 毫秒。
    * 元数据 I/O 延迟增加了 0.2 毫秒。
* 高并发 (r/w)： 
    * 无论工作负载如何混合，最大顺序吞吐量总体上都降低了 85%。 
    * 对于纯读取工作负载，最大随机 I/O 降低了 65%，随着工作负载转移为纯写入，总体影响降至 43%。 
    * 最大元数据工作负载降低了 30%。

## <a name="next-steps"></a>后续步骤  

* [为 Azure NetApp 文件配置 NFSv4.1 Kerberos 加密](configure-kerberos-encryption.md) 
