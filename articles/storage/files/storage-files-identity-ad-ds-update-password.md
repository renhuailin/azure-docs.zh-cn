---
title: 更新 AD DS 存储帐户密码
description: 了解如何更新代表存储帐户的 Active Directory 域服务帐户的密码。 这可以防止在密码过期后存储帐户被清除，从而防止身份验证失败。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: f54a2e7471917adb604da29e3b7fcc260ac521e0
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2021
ms.locfileid: "114729475"
---
# <a name="update-the-password-of-your-storage-account-identity-in-ad-ds"></a>更新 AD DS 中的存储帐户标识密码

如果你在强制实施密码过期时间的组织单位或域中注册了表示你的存储帐户的 Active Directory 域服务 (AD DS) 标识/帐户，则必须在最长密码使用期限之前更改密码。 你的组织可能会运行自动清理脚本，删除密码过期的帐户。 因此，如果你在密码过期之前未更改密码，帐户将会被删除，从而导致你无法访问 Azure 文件共享。

若要触发密码轮换，可以在 [AzFilesHybrid 模块](https://github.com/Azure-Samples/azure-files-samples/releases)中运行 `Update-AzStorageAccountADObjectPassword` 命令。 此命令必须使用混合用户在本地 AD DS 联接环境中运行，混合用户既具有存储帐户的所有者权限，也具有更改表示存储帐户的标识密码的 AD DS 权限。 该命令将执行类似于存储帐户密钥轮换的操作。 具体来说，它会获取存储帐户的第二个 Kerberos 密钥，并使用该密钥更新 AD DS 中已注册帐户的密码。 然后，它会重新生成存储帐户的目标 Kerberos 密钥，并更新 AD DS 中已注册帐户的密码。 你必须在本地 AD DS 联接环境中运行此命令。

为了防止在域中加入 Azure 存储帐户时进行密码轮换，请确保将 Azure 存储帐户置于 AD DS 中单独的组织单位中。 在此组织单位上禁用组策略继承，以避免应用默认域策略或特定密码策略。

```PowerShell
# Update the password of the AD DS account registered for the storage account
# You may use either kerb1 or kerb2
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |