---
title: Azure NetApp 文件上 Kerberos 的性能影响 NFSv 4.1 卷 |Microsoft Docs
description: 介绍了可用的安全选项、测试的性能向量以及 Azure NetApp 文件 NFSv 4.1 卷上 kerberos 的预期性能影响。
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
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744571"
---
# <a name="performance-impact-of-kerberos-on-azure-netapp-files-nfsv41-volumes"></a>Azure NetApp 文件上 Kerberos 的性能影响 NFSv 4.1 卷

Azure NetApp 文件在 Kerberos 模式下支持 [NFS 客户端加密](configure-kerberos-encryption.md) (krb5.conf、krb5i 和 krb5p) 与 AES-256 加密。 本文介绍了 NFSv 4.1 卷上 Kerberos 的性能影响。 

## <a name="available-security-options"></a>可用的安全选项 

当前可用于 NFSv 4.1 卷的安全选项如下所示： 

* **sec = sys** 使用本地 UNIX Uid 和 gid，方法是使用 AUTH_SYS 对 NFS 操作进行身份验证。
* **sec = krb5.conf** 使用 Kerberos V5 而不是本地 UNIX Uid 和 gid 来对用户进行身份验证。
* **sec = krb5i** 使用 Kerberos V5 进行用户身份验证，并使用安全校验和对 NFS 操作执行完整性检查，以防止数据篡改。
* **sec = krb5p** 使用 Kerberos V5 进行用户身份验证和完整性检查。 它会加密 NFS 流量，以防止流量被探查。 此选项是最安全的设置，但它还涉及到最大的性能开销。

## <a name="performance-vectors-tested"></a>已测试性能向量

本部分介绍各种选项的单个客户端性能影响 `sec=*` 。

* 对性能的影响在两个级别进行了测试：低并发性 (低负载) 和高并发性 (i/o 和吞吐量) 的上限。  
* 测试了三种类型的工作负荷：  
    * 小型操作随机读取/写入 (使用 FIO) 
    * 使用 FIO) 的大型操作顺序读取/写入 (
    * 由应用程序（例如 git）生成的元数据繁重工作负荷

## <a name="expected-performance-impact"></a>预期的性能影响 

主要有两个方面：光源负载和上限。 以下列表描述了安全设置和方案按方案列出的性能影响。 对安全参数进行所有比较 `sec=sys` 。 测试是使用单个客户端在单个卷上完成的。 

Krb5.conf 对性能的影响：

* 低并发性 (r/w) ：
    * 顺序延迟增加了0.3 毫秒。
    * 随机 i/o 延迟增加了0.2 毫秒。
    * 元数据 i/o 延迟增加了0.2 毫秒。
* 高并发性 (r/w) ： 
    * 最大顺序吞吐量为不受影响 by krb5.conf。
    * 当工作负荷转移到纯写入时，将总体影响降到零时，最大随机 i/o 数将减少30%，并将总体影响降到零。 
    * 最大元数据工作负荷减少30%。

Krb5i 对性能的影响： 

* 低并发性 (r/w) ：
    * 顺序延迟增加了0.5 毫秒。
    * 随机 i/o 延迟增加了0.2 毫秒。
    * 元数据 i/o 延迟增加了0.2 毫秒。
* 高并发性 (r/w) ： 
    * 最大顺序吞吐率降低了70%，而不考虑工作负荷组合。
    * 当工作负荷转移到纯写入时，整体影响降低到25% 时，最大随机 i/o 数降低了50%。 
    * 最大元数据工作负荷减少30%。

Krb5p 对性能的影响：

* 低并发性 (r/w) ：
    * 顺序延迟增加了0.8 毫秒。
    * 随机 i/o 延迟增加了0.2 毫秒。
    * 元数据 i/o 延迟增加了0.2 毫秒。
* 高并发性 (r/w) ： 
    * 最大顺序吞吐率降低了85%，而不考虑工作负荷组合。 
    * 当工作负荷转移到纯写入时，整体影响降低到43% 时，最大随机 i/o 数降低了65%。 
    * 最大元数据工作负荷减少30%。

## <a name="next-steps"></a>后续步骤  

* [为 Azure NetApp 文件配置 NFSv4.1 Kerberos 加密](configure-kerberos-encryption.md) 
