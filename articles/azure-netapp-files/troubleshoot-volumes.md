---
title: 排除 Azure NetApp 文件的卷错误 | Microsoft Docs
description: 介绍有助于对 Azure NetApp 文件卷进行故障排除的错误消息和解决方法。
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
ms.topic: troubleshooting
ms.date: 10/04/2021
ms.author: b-juche
ms.openlocfilehash: a9df7577e1426fb355995cd4486338003a753c86
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129539007"
---
# <a name="troubleshoot-volume-errors-for-azure-netapp-files"></a>排除 Azure NetApp 文件的卷错误

本文介绍了有助于对 Azure NetApp 文件卷进行故障排除的错误消息和解决方法。 

## <a name="errors-for-volume-allocation"></a>卷分配错误 

在 Azure NetApp 文件中创建新卷或调整现有卷的大小时，Microsoft Azure 会为订阅分配存储和网络资源。 你偶尔可能因特定区域中前所未有的 Azure 服务需求增长而遇到资源分配失败的情况。

本部分说明一些常见分配失败的原因，并建议可能的补救方法。

|     错误条件    |     解决方法    |
|-|-|
|创建新卷或调整现有卷大小时出错。 <br> 错误消息：`There was a problem locating [or extending] storage  for the volume. Please retry the operation. If the problem persists, contact Support.` | 此错误指示服务在尝试为此请求分配资源时遇到了错误。 <br> 稍后重试操作。 如果问题仍然存在，请与支持人员联系。|
|区域中常规卷的存储或网络容量不足。 <br> 错误消息：`There are currently insufficient resources available to create [or extend] a volume in this region. Please retry the operation. If the problem persists, contact Support.` | 此错误表示该区域中没有足够的资源可用于创建卷或调整卷的大小。 <br> 尝试下列解决方法之一： <ul><li>在新的 VNet 下创建卷。 这样做可以避免达到与网络相关的资源限制。</li> <li>请稍后重试。 在过渡期间，可能在群集或区域中释放了资源。</li></ul> |
|创建网络功能设置为 `Standard` 的卷时，存储容量不足。 <br> 错误消息：`No storage available with Standard network features, for the provided VNet.` | 此错误表示该区域中没有足够的资源可用于创建具有 `Standard` 网络功能的卷。 <br> 尝试下列解决方法之一： <ul><li>如果不需要 `Standard` 网络功能，请创建具有 `Basic` 网络功能的卷。</li> <li>尝试在新的 VNet 下创建卷。 这样做可以避免达到与网络相关的资源限制</li><li>请稍后重试。  在过渡期间，可能在群集或区域中释放了资源。</li></ul> |

## <a name="next-steps"></a>后续步骤  

* [创建用于 Azure NetApp 文件的 NFS 卷](azure-netapp-files-create-volumes.md)
* [创建用于 Azure NetApp 文件的 SMB 卷](azure-netapp-files-create-volumes-smb.md) 
* [为 Azure NetApp 文件创建双重协议卷](create-volumes-dual-protocol.md) 
* [为卷配置网络功能](configure-network-features.md)
