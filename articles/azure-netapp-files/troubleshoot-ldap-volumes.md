---
title: 排除 Azure NetApp 文件的 LDAP 卷故障 | Microsoft Docs
description: 本文介绍为 Azure NetApp 文件配置 LDAP 卷时可能会遇到的错误情况的解决方法。
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
ms.date: 04/05/2021
ms.author: b-juche
ms.openlocfilehash: 0008c6034eba2de58684276cb5f12c6b2398cac1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729222"
---
# <a name="troubleshoot-ldap-volume-issues"></a>排除 LDAP 卷故障

本文介绍了在配置 LDAP 卷时可能会遇到的错误情况的解决方法。

## <a name="errors-and-resolutions-for-ldap-volumes"></a>LDAP 卷的错误和解决方法

|     错误条件    |     解决方法    |
|-|-|
| 创建 ldapEnabled 为 true 的 SMB 卷时出错： <br> `Error Message: ldapEnabled option is only supported with NFS protocol volume. ` | 你无法创建启用了 LDAP 的 SMB 卷。 <br> 创建禁用了 LDAP 的 SMB 卷。 |
| 更新现有卷的 ldapEnabled 参数值时出错： <br> `Error Message: ldapEnabled parameter is not allowed to update` |  创建卷后，不能修改 LDAP 选项设置。 <br> 不要在创建的卷上更新 LDAP 选项设置。 有关详细信息，请参阅[配置添加 LDAP，其中包含用于 NFS 卷访问的扩展组](configure-ldap-extended-groups.md)。 |
| 创建启用 LDAP 的 NFS 卷时出错： <br> `Could not query DNS server` <br> `Sample error message:` <br> `"log": time="2020-10-21 05:04:04.300" level=info msg=Res method=GET url=/v2/Volumes/070d0d72-d82c-c893-8ce3-17894e56cea3  x-correlation-id=9bb9e9fe-abb6-4eb5-a1e4-9e5fbb838813 x-request-id=c8032cb4-2453-05a9-6d61-31ca4a922d85 xresp="200:  {\"created\":\"2020-10-21T05:02:55.000Z\",\"lifeCycleState\":\"error\",\"lifeCycleStateDetails\":\"Error when creating - Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available.\",\"name\":\"smb1\",\"ownerId\ \":\"8c925a51-b913-11e9-b0de-9af5941b8ed0\",\"region\":\"westus2stage\",\"volumeId\":\"070d0d72-d82c-c893-8ce3-` |  发生此错误的原因是 DNS 不可访问。 <br> <ul><li> 检查是否已为 Azure NetApp 文件配置了正确的站点（站点范围）。 </li><li> DNS 不可访问的原因可能是 DNS IP 地址不正确或网络问题。 检查在 AD 连接中输入的 DNS IP 地址，以确保其正确无误。 </li><li> 请确保 AD 和卷位于同一区域和同一 VNet 中。 如果它们在不同的 VNet 中，请确保在两个 VNet 之间建立 VNet 对等互连。</li></ul> |
| 从快照创建卷时出错： <br> `Aggregate does not exist` | Azure NetApp 文件不支持从属于已禁用 LDAP 的卷的快照预配新的、启用 LDAP 的卷。 <br> 尝试从给定的快照创建新的已禁用 LDAP 的卷。 |

## <a name="next-steps"></a>后续步骤  

* [配置添加 LDAP，其中包含用于 NFS 卷访问的扩展组](configure-ldap-extended-groups.md)
* [创建用于 Azure NetApp 文件的 NFS 卷](azure-netapp-files-create-volumes.md)
* [为 Azure NetApp 文件创建双重协议卷](create-volumes-dual-protocol.md)