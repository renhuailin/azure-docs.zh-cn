---
title: 为 Azure NetApp 文件配置 NFSv4.1 Kerberos 加密 | Microsoft Docs
description: 介绍如何为 Azure NetApp 文件配置 NFSv4.1 Kerberos 加密，以及性能影响。
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
ms.topic: how-to
ms.date: 07/15/2021
ms.author: b-juche
ms.openlocfilehash: a12df50ac6fe2e1dc2bf793316538a90ba0f901d
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114361328"
---
# <a name="configure-nfsv41-kerberos-encryption-for-azure-netapp-files"></a>为 Azure NetApp 文件配置 NFSv4.1 Kerberos 加密

Azure NetApp 文件支持使用 AES-256 加密在 Kerberos 模式（krb5、krb5i 和 krb5p）下加密 NFS 客户端。 本文介绍将 NFSv4.1 卷与 Kerberos 加密结合使用时所需的配置。

## <a name="requirements"></a>要求

NFSv4.1 客户端加密有以下要求： 

* 连接 Active Directory 域服务 (AD DS) 或 Azure Active Directory 域服务 (AADDS)，以便使用 Kerberos 票证 
* 同时为客户端和 Azure NetApp 文件 NFS 服务器 IP 地址创建 DNS A/PTR 记录
* Linux 客户端：本文针对 RHEL 和 Ubuntu 客户端提供相关指导。  其他客户端的配置步骤与此类似。 
* NTP 服务器访问：可以使用其中一个常用的 Active Directory 域控制器 (AD DC)。
* 确保用户帐户的用户主体名称不以 `$` 符号结尾（例如，user$@REALM.COM）。 <!-- Not using 'contoso.com' in this example; per Mark, A customers REALM namespace may be different from their AD domain name space. -->   
    对于[组托管服务帐户](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts) (gMSA)，需要先删除用户主体名称中的 `$` 后缀，帐户才能与 Azure NetApp 文件 Kerberos 功能一起使用。


## <a name="create-an-nfs-kerberos-volume"></a>创建 NFS Kerberos 卷

1.  按照[为 Azure NetApp 文件创建 NFS 卷](azure-netapp-files-create-volumes.md)中的步骤创建 NFSv4.1 卷。   

    在“创建卷”页上，将 NFS 版本设置为“NFSv4.1”，将 Kerberos 设置为“已启用” 。

    > [!IMPORTANT] 
    > 创建卷后无法修改 Kerberos 启用选择。

    ![创建 NFSv4.1 Kerberos 卷](../media/azure-netapp-files/create-kerberos-volume.png)  

2. 选择“导出策略”，使其与卷所需的访问和安全级别选项匹配（Kerberos 5、Kerberos 5i 或 Kerberos 5p）。   

    有关 Kerberos 的性能影响，请参阅 [Kerberos 对 NFSv4.1 卷的性能影响](#kerberos_performance)。  

    还可以单击 Azure NetApp 文件导航窗格中的“导出策略”，修改卷的 Kerberos 安全性方法。

3.  单击“查看 + 创建”以创建 NFSv4.1 卷。

## <a name="configure-the-azure-portal"></a>配置 Azure 门户 

1.  按照[创建 Active Directory 连接](create-active-directory-connections.md)中的说明操作。  

    Kerberos 要求在 Active Directory 中创建至少一个计算机帐户。 提供的帐户信息同时用于创建 SMB 和 NFSv4.1 Kerberos 卷的帐户。 创建卷时会自动创建此计算机帐户。

2.  在“Kerberos 领域”下，输入“AD 服务器名称”和“KDC IP”地址。

    AD 服务器和 KDC IP 可以是同一服务器。 此信息用于创建供 Azure NetApp 文件使用的 SPN 计算机帐户。 创建该计算机帐户后，Azure NetApp 文件将使用 DNS 服务器记录根据需要定位更多 KDC 服务器。 

    ![Kerberos 领域](../media/azure-netapp-files/kerberos-realm.png)
 
3.  单击“联结”以保存配置。

## <a name="configure-active-directory-connection"></a>配置 Active Directory 连接 

配置 NFSv4.1 Kerberos 时会在 Active Directory 中创建两个计算机帐户：
* 用于 SMB 共享的计算机帐户
* 用于 NFSv4.1 的计算机帐户 - 可通过前缀 `NFS-` 来识别此帐户。 

创建第一个 NFSv4.1 Kerberos 卷后，使用以下 PowerShell 命令设置计算机帐户的加密类型：

`Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256`

## <a name="configure-the-nfs-client"></a>配置 NFS 客户端访问 

按照[为 Azure NetApp 文件配置 NFS 客户端](configure-nfs-clients.md)中的说明配置 NFS 客户端。  

## <a name="mount-the-nfs-kerberos-volume"></a><a name="kerberos_mount"></a>装载 NFS Kerberos 卷

1. 从“卷”页中，选择要装载的 NFS 卷。

2. 从卷中选择“装入说明”以显示说明。

    例如： 

    ![Kerberos 卷的装载说明](../media/azure-netapp-files/mount-instructions-kerberos-volume.png)  

3. 为新卷创建目录（装载点）。  

4. 将计算机帐户的默认加密类型设置为 AES 256：  
    `Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256 -Credential $ANFSERVICEACCOUNT`

    * 只需为每个计算机帐户运行一次此命令。
    * 可以从域控制器或安装了 [RSAT](https://support.microsoft.com/help/2693643/remote-server-administration-tools-rsat-for-windows-operating-systems) 的 PC 上运行此命令。 
    * `$NFSCOMPUTERACCOUNT` 变量是在部署 Kerberos 卷时在 Active Directory 中创建的计算机帐户。 这是前缀为 `NFS-` 的帐户。 
    * `$ANFSERVICEACCOUNT` 变量是非特权 Active Directory 用户帐户，对计算机帐户创建时所在的组织单位具有委派的控制。 

5. 在主机上装载卷： 

    `sudo mount -t nfs -o sec=krb5p,rw,hard,rsize=1048576,wsize=1048576,vers=4.1,tcp $ANFEXPORT $ANFMOUNTPOINT`

    * 变量 `$ANFEXPORT` 是装载说明中所述的路径 `host:/export`。
    * 变量 `$ANFMOUNTPOINT` 是 Linux 主机上用户创建的文件夹。

## <a name="performance-impact-of-kerberos-on-nfsv41"></a><a name="kerberos_performance"></a>NFSv 4.1 上的 Kerberos 对性能的影响 

应了解适用于 NFSv4.1 卷的安全选项、经过测试的性能向量以及 kerberos 的预期性能影响。 有关详细信息，请参阅 [Kerberos 对 NFSv4.1 卷的性能影响](performance-impact-kerberos.md)。  

## <a name="next-steps"></a>后续步骤  

* [Kerberos 对 NFSv4.1 卷的性能影响](performance-impact-kerberos.md)
* [排查 NFSv4.1 Kerberos 卷的问题](troubleshoot-nfsv41-kerberos-volumes.md)
* [有关 Azure NetApp 文件的常见问题解答](azure-netapp-files-faqs.md)
* [创建用于 Azure NetApp 文件的 NFS 卷](azure-netapp-files-create-volumes.md)
* [创建 Active Directory 连接器](create-active-directory-connections.md)
* [为 Azure NetApp 文件配置 NFS 客户端](configure-nfs-clients.md) 
