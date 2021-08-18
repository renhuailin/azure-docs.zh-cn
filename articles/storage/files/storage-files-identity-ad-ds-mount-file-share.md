---
title: 将 Azure 文件共享装载到加入 AD DS 的 VM
description: 了解如何将文件共享装载到已加入本地 Active Directory 域服务的计算机。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: d71526fea23e3a440428266addfc497b1a89c63c
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112117152"
---
# <a name="part-four-mount-a-file-share-from-a-domain-joined-vm"></a>第四部分：从加入域的 VM 装载文件共享

在开始学习本文之前，请确保已完成上一篇文章：[通过 SMB 配置目录和文件级别权限](storage-files-identity-ad-ds-configure-permissions.md)。

本文中所述的过程验证是否正确设置了文件共享和访问权限，你是否可以从加入域的 VM 访问 Azure 文件共享。 共享级别 Azure 角色分配可能需要一些时间才能生效。 

使用你向其授予权限的凭据登录到客户端，如下图所示。

![显示用户身份验证的 Azure AD 登录屏幕的屏幕截图](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |

## <a name="mounting-prerequisites"></a>装载先决条件

在装载文件共享之前，确保满足以下先决条件：

- 如果要从以前使用存储帐户密钥装载了文件共享的客户端装载文件共享，请确保已断开与共享的连接，删除存储帐户密钥的永久凭据，并且当前正在使用 AD DS 凭据进行身份验证。 有关使用存储帐户密钥清除已装载共享的说明，请参阅[常见问题解答页](./storage-files-faq.md#ad-ds--azure-ad-ds-authentication)。
- 客户端必须可访问 AD DS。 如果你的计算机或 VM 不在 AD DS 管理的网络中，则需要启用 VPN 来访问 AD DS 以进行身份验证。

将占位符值替换为你自己的值，然后使用以下命令装载 Azure 文件共享。 始终需要使用如下所示的路径进行装载。 对于基于标识的身份验证（AD DS 或 Azure AD DS），不支持使用 CNAME 进行文件装载。

```PSH
# Always mount your share using.file.core.windows.net, even if you setup a private endpoint for your share.
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
  net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
  Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

如果在使用 AD DS 凭据装载时遇到问题，请参阅[无法使用 AD 凭据装载 Azure 文件存储](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials)以获得指导。

如果成功装载文件共享，则已成功为 Azure 文件共享启用并配置本地 AD DS 身份验证。

## <a name="next-steps"></a>后续步骤

如果在 AD DS 中创建的用于表示存储帐户的标识位于强制密码轮换的域或 OU 中，请继续浏览下一篇文章，了解有关更新密码的说明：

[更新 AD DS 中的存储帐户标识密码](storage-files-identity-ad-ds-update-password.md)