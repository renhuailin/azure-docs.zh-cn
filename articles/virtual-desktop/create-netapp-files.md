---
title: 创建 Azure NetApp 文件 Azure 虚拟桌面 - Azure
description: 本文介绍如何在 Azure 虚拟桌面中创建 Azure NetApp 文件。
author: Heidilohr
ms.topic: how-to
ms.date: 08/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 244ff34b462d519d111aad6d6b66d7261e612595
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446584"
---
# <a name="upload-msix-images-to-azure-netapp-files-in-azure-virtual-desktop"></a>在 Azure 虚拟桌面中将 MSIX 映像上传到 Azure NetApp 文件

本文介绍如何在 Azure 虚拟桌面中将 MSIX 映像上传到 Azure NetApp 文件。

## <a name="requirements"></a>要求

在开始上传映像之前，需要先设置 Azure NetApp 文件（如果尚未设置）。

若要设置 Azure NetApp 文件，需要具备以下条件：

- 具有参与者或管理员访问权限的 Azure 帐户

- 虚拟机 (VM) 或已加入 Active Directory 域服务 (AD DS) 的物理计算机，以及访问该计算机的权限

- Azure 虚拟桌面主机池，由已加入域的会话主机创建。 每个会话主机所在的区域必须与创建 Azure NetApp 文件的区域相同。 有关详细信息，请参阅[区域可用性](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)。 如果现有会话主机不在可用区域中，则需要创建新的会话主机。

## <a name="start-using-azure-netapp-files"></a>开始使用 Azure NetApp 文件

若要开始使用 Azure NetApp 文件，请按照以下步骤操作：

1. 按照[注册 Azure NetApp 文件](../azure-netapp-files/azure-netapp-files-register.md)中的说明，提交等待列表请求，并注册 NetApp 提供程序。
2. 按照[设置 Azure NetApp 文件帐户](create-fslogix-profile-container.md#set-up-your-azure-netapp-files-account)中的说明，设置 Azure NetApp 文件帐户。
3. 按照[设置容量池](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)中的说明创建容量池。
4. 按照[加入 Active Directory 连接](create-fslogix-profile-container.md#join-an-active-directory-connection)中的说明，加入 Azure Active Directory (Azure AD) 连接。
5. 按照[创建新卷](create-fslogix-profile-container.md#create-a-new-volume)和[配置卷访问参数](create-fslogix-profile-container.md#configure-volume-access-parameters)中的说明创建一个新卷。
6. 按照[确保用户可以访问 Azure NetApp 文件共享](create-fslogix-profile-container.md#make-sure-users-can-access-the-azure-netapp-file-share)中的说明，确保与 Azure NetApp 文件共享的连接可以正常工作。

## <a name="upload-an-msix-image-to-the-azure-netapp-file-share"></a>将 MSIX 映像上传到 Azure NetApp 文件共享

设置 Azure NetApp 文件共享后，可以开始将映像上传到该共享。

若要将 MSIX 映像上传到 Azure NetApp 文件共享，请按照以下步骤操作：

1. 在每个会话主机中，安装用于对 MSIX 包进行签名的证书。 确保将证书存储在名为“受信任人员”的文件夹中。
2. 复制要添加到 Azure NetApps 文件共享中的 MSIX 映像。
3. 转到“文件资源管理器”并输入装载路径，然后将 MSIX 映像粘贴到装载路径文件夹中。

现在，当会话主机使用 Azure 门户或 PowerShell 添加 MSIX 包时，会话主机应该可以访问 MSIX 映像。

## <a name="next-steps"></a>后续步骤

现在你已创建 Azure NetApp 文件共享，以下一些资源可有助于了解其在 Azure 虚拟桌面中的用途：

- [使用 Azure NetApp 文件和 AD DS 创建配置文件容器](create-fslogix-profile-container.md)
- [Azure 虚拟桌面中 FSLogix 配置文件容器的存储选项](store-fslogix-profile.md)
- [为 Azure NetApp 文件创建复制对等互连](../azure-netapp-files/cross-region-replication-create-peering.md)
