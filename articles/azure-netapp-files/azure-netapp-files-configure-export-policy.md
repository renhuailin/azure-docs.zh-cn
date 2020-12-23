---
title: 配置 NFS 卷的导出策略-Azure NetApp 文件
description: 介绍如何使用 Azure NetApp 文件配置导出策略，以控制对 NFS 卷的访问
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 77630ddcd61d17f3b47e6cb5d43396c1a6f0e904
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517863"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>为 NFS 卷配置导出策略

可以配置导出策略来控制对 Azure NetApp 文件卷的访问。 Azure NetApp 文件导出策略支持使用 NFS 协议的卷 (NFSv3 和 NFSv 4.1) 以及双重协议 (NFSv3 和 SMB) 。 

最多可以创建五个导出策略规则。

## <a name="configure-the-policy"></a>配置策略 

1.  在 " **卷** " 页上，选择要为其配置导出策略的卷，然后选择 " **导出策略** "。 你还可以在卷的创建过程中配置导出策略。

2.  若要创建导出策略规则，请指定以下信息：   
    * **Index** ：指定规则的索引号。  
      
      导出策略最多可以包含五个规则。 规则将按照它们在索引号列表中的顺序进行评估。 首先将评估索引号较小的规则。 例如，索引号为 1 的规则将在索引号为 2 的规则前进行评估。 

    * **允许的客户端：使用** 以下格式之一指定值：  
      * IPv4 地址旁边列出的你的 IP 地址。 示例：`10.1.12.24`
      * 使用子网掩码表示的 IPv4 地址，表示为多个位。 示例：`10.1.12.10/4`
      * 逗号分隔的 IP 地址。 可以在单个规则中输入多个主机 Ip，方法是使用逗号分隔它们。 示例：`10.1.12.25,10.1.12.28,10.1.12.29`

    * **访问** ：选择以下访问类型之一：  
      * 无访问权限 
      * 读取和写入
      * 只读

    * **只读** 和 **读/写** ：如果你将 Kerberos 加密与 nfsv 4.1 一起使用，请按照 [配置 nfsv 4.1 Kerberos 加密](configure-kerberos-encryption.md)中的说明进行操作。  对于 Kerberos 的性能影响，请参阅 [nfsv 4.1 上 Kerberos 的性能影响](configure-kerberos-encryption.md#kerberos_performance)。 

      ![Kerberos 安全选项](../media/azure-netapp-files/kerberos-security-options.png) 

    * **根访问权限** ：指定 `root` 帐户是否可以访问该卷。  默认情况下，根访问权限设置为 **On** ，并且该 `root` 帐户有权访问该卷。

      ![导出策略](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 

## <a name="next-steps"></a>后续步骤 
* [为虚拟机装载或卸载卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [管理快照](azure-netapp-files-manage-snapshots.md)
