---
title: 为 Azure NetApp 文件配置 NFSv4.1 默认域 | Microsoft Docs
description: 介绍如何配置 NFS 客户端，以将 NFSv 4.1 与 Azure NetApp 文件配合使用。
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
ms.date: 10/14/2020
ms.author: b-juche
ms.openlocfilehash: c3c853190d5f63bbe9012727d8b7b7ac91da135f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92072146"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>为 Azure NetApp 文件配置 NFSv 4.1 默认域

NFSv4 引入了身份验证域的概念。 Azure NetApp 文件目前支持从服务到 NFS 客户端的仅限根目录的用户映射。 要在 Azure NetApp 文件中使用 NFSv 4.1 功能，需要更新 NFS 客户端。

## <a name="default-behavior-of-usergroup-mapping"></a>用户/组映射的默认行为

默认情况下，根映射默认为 `nobody` 用户，因为 NFSv4 域默认设置为 `localdomain`。 将 NFSv 4.1 卷的 Azure NetApp 文件装载为根时，将看到如下所示的文件权限：  

![NFSv4.1 的用户/组映射的默认行为](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

如上面的示例所示，`file1` 的用户应为 `root`，但默认情况下它将映射到 `nobody`。  本文说明如何通过将 `idmap Domain` 设置更改为 `defaultv4iddomain.com` 来将 `file1` 用户设置为 `root`。  

## <a name="steps"></a>步骤 

1. 在 NFS 客户端上编辑 `/etc/idmapd.conf` 文件。   
    取消注释行 `#Domain`（即，从行中删除 `#`），然后将值 `localdomain` 更改为 `defaultv4iddomain.com`。 

    初始配置： 
    
    ![NFSv4.1 的初始配置](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    更新后的配置：
    
    ![NFSv4.1 的更新后配置](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. 卸载任何当前装载的 NFS 卷。
3. 更新 `/etc/idmapd.conf` 文件。
4. 重新启动主机 (`service rpcbind restart`) 上的 `rpcbind` 服务，或直接重新启动主机。
5. 根据需要装载 NFS 卷。   

    请参阅[为 Windows 或 Linux 虚拟机装载或卸载卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)。 

以下示例显示生成的用户/组更改： 

![显示生成的用户/组更改示例的屏幕截图。](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

如示例所示，用户/组现在已从 `nobody` 更改为 `root`。

## <a name="behavior-of-other-non-root-users-and-groups"></a>其他用户（非 root 用户）和组的行为

Azure NetApp 文件支持本地用户（在主机上本地创建的用户），这些用户具有与 NFSv4.1 卷中的文件或文件夹关联的权限。 但是，该服务当前不支持跨多个节点映射用户/组。 因此，在一个主机上创建的用户在默认情况下不会映射到在另一台主机上创建的用户。 

在下面的示例中，`Host1` 有三个现有的测试用户帐户（`testuser01`、`testuser02` 和 `testuser03`）： 

![显示 Host1 有三个现有测试用户帐户的屏幕截图。](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

请注意，在 `Host2` 上，尚未创建测试用户帐户，但这两个主机上装载了相同的卷：

![为 NFSv 4.1 产生的配置](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>后续步骤 

[为 Windows 或 Linux 虚拟机装载或卸载卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)

