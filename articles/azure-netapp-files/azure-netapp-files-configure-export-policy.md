---
title: 为 Azure NetApp 文件 NFS 或双重协议卷配置导出策略 - Azure NetApp 文件
description: 介绍如何使用 Azure NetApp 文件配置导出策略来控制对 NFS 卷的访问
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 05/07/2021
ms.openlocfilehash: 35bcff2615ab9b31f077a13c1cae7fbe38518575
ms.sourcegitcommit: c1b0d0b61ef7635d008954a0d247a2c94c1a876f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2021
ms.locfileid: "109628241"
---
# <a name="configure-export-policy-for-nfs-or-dual-protocol-volumes"></a>为 NFS 或双重协议卷配置导出策略

可以配置导出策略来控制对某个使用 NFS 协议（NFSv3 和 NFSv4.1）或双协重议（NFSv3 和 SMB）的 Azure NetApp 文件卷的访问。 

最多可创建 5 个导出策略规则。

## <a name="configure-the-policy"></a>配置策略 

1.  在“卷”页面上，选择要为其配置导出策略的卷，然后选择“导出策略” 。 还可在卷的创建过程中配置导出策略。

2.  若要创建导出策略规则，请指定以下信息：   
    * **索引**：指定规则的索引号。  
      
      导出策略最多可以包含五个规则。 规则将按照它们在索引号列表中的顺序进行评估。 首先将评估索引号较小的规则。 例如，索引号为 1 的规则将在索引号为 2 的规则前进行评估。 

    * **允许的客户端**：采用以下格式之一指定值：  
      * IPv4 地址旁边列出的你的 IP 地址。 示例： `10.1.12.24`
      * 以位数表示子网掩码的 IPv4 地址。 示例： `10.1.12.10/4`
      * 逗号分隔的 IP 地址。 可通过逗号分隔方式在单个规则中输入多个主机 IP。 长度限制为 4096 个字符。 示例： `10.1.12.25,10.1.12.28,10.1.12.29`

    * **访问权限**：选择以下访问权限类型之一：  
      * 无访问权限 
      * 读取和写入
      * 只读

    * 只读和读/写：如果你将 Kerberos 加密与 NFSv4.1 一起使用，请按照[配置 NFSv4.1 Kerberos 加密](configure-kerberos-encryption.md)中的说明进行操作 。  对于 Kerberos 的性能影响，请参阅 [NFSv4.1 卷上 Kerberos 的性能影响](performance-impact-kerberos.md)。 

      ![Kerberos 安全选项](../media/azure-netapp-files/kerberos-security-options.png) 

    * **根访问权限**：指定 `root` 帐户是否可以访问该卷。  默认情况下，根访问权限设置为“开”，并且 `root` 帐户有权访问卷。

      ![导出策略](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 

## <a name="next-steps"></a>后续步骤 
* [装载或卸载卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [管理快照](azure-netapp-files-manage-snapshots.md)
