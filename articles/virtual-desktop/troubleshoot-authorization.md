---
title: Azure 文件存储虚拟桌面故障排除 - Azure
description: 如何排除 Azure 虚拟桌面中 Azure 文件存储的问题。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 08/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 31fe9f55252c00b5475bbb96cef646d0906a05f7
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446586"
---
# <a name="troubleshoot-azure-files-authorization"></a>Azure 文件存储授权的故障排除

本文介绍与使用 Azure Active Directory (AD) 进行 Azure 文件存储身份验证相关的常见问题，以及如何解决这些问题的建议。

## <a name="my-group-membership-isnt-working"></a>组成员身份不起作用

将虚拟机 (VM) 添加到 Active Directory 域服务 (AD DS) 组时，必须重新启动该 VM，以激活其在服务中的成员身份。

## <a name="i-cant-add-my-storage-account-to-my-ad-ds"></a>无法将存储帐户添加到 AD DS

首先，请检查[无法通过 AD 凭据装载 Azure 文件存储](../storage/files/storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials)，以查看其中是否已列出此问题。

以下是用户可能遇到的问题的最常见原因：

- 忽略在 PowerShell 中创建帐户时出现的任何警告消息。 忽略可能导致新帐户配置错误的警告。 若要解决此问题，应删除代表存储帐户的域帐户，然后重试。

- 帐户使用的组织单位 (OU) 不正确。 若要解决此问题，请使用以下语法重新输入 OU 信息：
    
    ```powershell
    DC=ouname,DC=domainprefix,DC=topleveldomain
    ```

    例如：

    ```powershell
    DC=storageAccounts,DC=wvdcontoso,DC=com
    ```

- 如果该存储帐户没有立即出现在 Azure AD 中，不必担心。 新的存储帐户通常需要 30 分钟才能与 Azure AD 同步，因此请耐心等待。 如果 30 分钟后仍未同步，请参阅[下一部分](#my-ad-ds-group-wont-sync-to-azure-ad)。

## <a name="my-ad-ds-group-wont-sync-to-azure-ad"></a>AD DS 组无法同步到 Azure AD

如果存储帐户在 30 分钟后未自动与 Azure AD 同步，则需要使用[此脚本](https://github.com/stgeorgi/msixappattach/blob/master/force%20AD%20DS%20to%20Azure%20AD%20sync/force%20sync.ps1)强制同步。

## <a name="my-storage-account-says-it-needs-additional-permissions"></a>存储帐户需要额外权限

如果存储帐户需要额外的权限，则是因为你可能没有访问 MSIX 应用附加和 FSLogix 的权限。 若要解决此问题，请确保已将以下权限之一分配给帐户：

- “存储文件数据 SMB 共享参与者”RBAC 权限。

- “读取和执行”以及“列表文件夹内容”NTFS 权限。

## <a name="next-steps"></a>后续步骤

如需回顾有关 Azure 文件存储安装过程的内容，请参阅[为 Azure 文件存储授权帐户](azure-files-authorization.md)。