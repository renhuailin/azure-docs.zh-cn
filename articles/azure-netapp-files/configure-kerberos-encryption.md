---
title: 为 Azure NetApp 文件配置 NFSv 4.1 Kerberos 加密 |Microsoft Docs
description: 介绍如何为 Azure NetApp 文件配置 NFSv 4.1 Kerberos 加密以及对性能的影响。
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
ms.date: 02/18/2021
ms.author: b-juche
ms.openlocfilehash: 6ff87d046c60f588e133010895ec3e7ce08cb71f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740556"
---
# <a name="configure-nfsv41-kerberos-encryption-for-azure-netapp-files"></a>为 Azure NetApp 文件配置 NFSv4.1 Kerberos 加密

Azure NetApp 文件在 Kerberos 模式下支持 NFS 客户端加密 (krb5.conf、krb5i 和 krb5p) 与 AES-256 加密。 本文介绍将 NFSv 4.1 卷与 Kerberos 加密配合使用所需的配置。

## <a name="requirements"></a>要求

以下要求适用于 NFSv 4.1 客户端加密： 

* Active Directory 域服务 (AD DS) 连接以便于 Kerberos 票证 
* 为客户端和 Azure NetApp 文件创建 DNS A/PTR 记录 NFS 服务器 IP 地址
* Linux 客户端  
    本文为 RHEL 和 Ubuntu 客户端提供指导。  其他客户端将使用类似的配置步骤。 
* NTP 服务器访问  
    你可以使用 (AD DC) 域控制器的常用 Active Directory 域控制器之一。

## <a name="create-an-nfs-kerberos-volume"></a>创建 NFS Kerberos 卷

1.  按照为 [Azure NetApp 文件创建 NFS 卷](azure-netapp-files-create-volumes.md) 中的步骤创建 nfsv 4.1 卷。   

    在 "创建卷" 页上，将 NFS 版本设置为 **nfsv 4.1**，并将 Kerberos 设置为 " **已启用**"。

    > [!IMPORTANT] 
    > 创建卷后，无法修改 Kerberos 启用选项。

    ![创建 NFSv 4.1 Kerberos 卷](../media/azure-netapp-files/create-kerberos-volume.png)  

2. 选择 " **导出策略** "，以匹配所需的访问级别和安全选项 (kerberos 5、kerberos 5I 或 kerberos 5p) 。   

    对于 Kerberos 的性能影响，请参阅 [nfsv 4.1 上 Kerberos 的性能影响](#kerberos_performance)。  

    你还可以通过在 Azure NetApp 文件导航窗格中单击 "导出策略" 来修改卷的 Kerberos 安全方法。

3.  单击 " **查看 + 创建** " 创建 nfsv 4.1 卷。

## <a name="configure-the-azure-portal"></a>配置 Azure 门户 

1.  按照 [创建 Active Directory 连接](create-active-directory-connections.md)中的说明进行操作。  

    Kerberos 要求你至少在 Active Directory 中创建一个计算机帐户。 提供的帐户信息用于创建 SMB *和* Nfsv 4.1 Kerberos 卷的帐户。 创建卷时，将自动创建此计算机。

2.  在 " **Kerberos 领域**" 下，输入 **AD 服务器名称** 和 **KDC IP** 地址。

    AD 服务器和 KDC IP 可以是同一服务器。 此信息用于创建由 Azure NetApp 文件使用的 SPN 计算机帐户。 创建计算机帐户后，Azure NetApp 文件将使用 DNS 服务器记录根据需要定位其他 KDC 服务器。 

    ![Kerberos 领域](../media/azure-netapp-files/kerberos-realm.png)
 
3.  单击 " **联接** " 以保存配置。

## <a name="configure-active-directory-connection"></a>配置 Active Directory 连接 

配置 NFSv 4.1 Kerberos 会在 Active Directory 中创建两个计算机帐户：
* SMB 共享的计算机帐户
* NFSv 4.1 的计算机帐户-可以通过前缀来识别此帐户 `NFS-` 。 

创建第一个 NFSv 4.1 Kerberos 卷后，请使用以下 PowerShell 命令设置计算机帐户的加密类型：

`Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256`

## <a name="configure-the-nfs-client"></a>配置 NFS 客户端 

按照为 [Azure NetApp 文件配置 nfs 客户端](configure-nfs-clients.md) 中的说明配置 nfs 客户端。  

## <a name="mount-the-nfs-kerberos-volume"></a><a name="kerberos_mount"></a>装载 NFS Kerberos 卷

1. 从 " **卷** " 页中，选择要装载的 NFS 卷。

2. 从卷中选择 " **装入说明** " 以显示说明。

    例如： 

    ![Kerberos 卷的装载说明](../media/azure-netapp-files/mount-instructions-kerberos-volume.png)  

3. 为新卷 (装入点) 创建目录。  

4. 为计算机帐户将默认加密类型设置为 AES 256：  
    `Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256 -Credential $ANFSERVICEACCOUNT`

    * 对于每个计算机帐户，只需运行一次此命令。
    * 可以从域控制器或安装了 [RSAT](https://support.microsoft.com/help/2693643/remote-server-administration-tools-rsat-for-windows-operating-systems) 的 PC 上运行此命令。 
    * `$NFSCOMPUTERACCOUNT`变量是在部署 Kerberos 卷 Active Directory 中创建的计算机帐户。 这是前缀为的帐户 `NFS-` 。 
    * `$ANFSERVICEACCOUNT`变量是在创建计算机帐户的组织单位上具有委派控制权限的非特权 Active Directory 用户帐户。 

5. 在主机上装载卷： 

    `sudo mount -t nfs -o sec=krb5p,rw,hard,rsize=1048576,wsize=1048576,vers=4.1,tcp $ANFEXPORT $ANFMOUNTPOINT`

    * `$ANFEXPORT`变量是 `host:/export` 在装载说明中找到的路径。
    * `$ANFMOUNTPOINT`变量是 Linux 主机上用户创建的文件夹。

## <a name="performance-impact-of-kerberos-on-nfsv41"></a><a name="kerberos_performance"></a>NFSv 4.1 上的 Kerberos 对性能的影响 

你应了解适用于 NFSv 4.1 卷的安全选项、经过测试的性能向量以及 kerberos 所预期的性能影响。 有关详细信息，请参阅 [nfsv 4.1 卷上的 Kerberos 对性能的影响](performance-impact-kerberos.md) 。  

## <a name="next-steps"></a>后续步骤  

* [NFSv 4.1 卷上 Kerberos 的性能影响](performance-impact-kerberos.md)
* [排查 NFSv 4.1 Kerberos 卷问题](troubleshoot-nfsv41-kerberos-volumes.md)
* [有关 Azure NetApp 文件的常见问题解答](azure-netapp-files-faqs.md)
* [创建用于 Azure NetApp 文件的 NFS 卷](azure-netapp-files-create-volumes.md)
* [创建 Active Directory 连接器](create-active-directory-connections.md)
* [为 Azure NetApp 文件配置 NFS 客户端](configure-nfs-clients.md) 
