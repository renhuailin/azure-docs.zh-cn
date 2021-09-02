---
title: 控制用户可以在文件级别执行的操作 - Azure 文件共享
description: 了解如何配置 Windows ACL 权限以向 Azure 文件共享进行本地 AD DS 身份验证。 允许利用精细访问控制。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/16/2020
ms.author: rogarana
ms.openlocfilehash: efa2ec8873374604e252677e436e6883867f982a
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255485"
---
# <a name="part-three-configure-directory-and-file-level-permissions-over-smb"></a>第三部分：通过 SMB 配置目录和文件级别权限 

在开始本文之前，请确保已完成上一篇文章：[向标识分配共享级别权限](storage-files-identity-ad-ds-assign-permissions.md)，以确保共享级别权限已就位。

使用 Azure RBAC 分配共享级别权限后，必须在根、目录或文件级别配置适当的 Windows ACL 才能利用精细访问控制。 将 Azure RBAC 共享级别权限视为确定用户是否可以访问共享的高级守卫。 而 Windows ACL 则在粒度更细的级别运行，确定用户可以在目录或文件级别执行哪些操作。 当用户尝试访问文件/目录时，会强制执行共享级别和文件/目录级别权限，因此，如果两者之间存在差异，则只会应用限制最严格的权限。 例如，如果用户在文件级别具有读取/写入访问权限，但在共享级别只具有读取权限，则该用户只能读取该文件。 反过来也是这样，如果用户在共享级别具有读取/写入访问权限，但在文件级别只具有读取权限，则该用户仍然只能读取文件。

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |

## <a name="azure-rbac-permissions"></a>Azure RBAC 权限

下表包含与此配置相关的 Azure RBAC 权限：


| 内置角色  | NTFS 权限  | 得到的访问权限  |
|---------|---------|---------|
|存储文件数据 SMB 共享读取者 | 完全控制、修改、读取、写入、执行 | 读取并执行  |
|     |   读取 |     读取  |
|存储文件数据 SMB 共享参与者  |  完全控制    |  修改、读取、写入、执行 |
|     |  修改         |  修改    |
|     |  读取并执行 |  读取并执行 |
|     |  读取           |  读取    |
|     |  写入          |  写入   |
|存储文件数据 SMB 共享特权参与者 | 完全控制  |  修改、读取、写入、编辑（更改权限）、执行 |
|     |  修改          |  修改 |
|     |  读取并执行  |  读取并执行 |
|     |  读取            |  读取   |
|     |  写入           |  写入  |



## <a name="supported-permissions"></a>支持的权限

Azure 文件存储支持全套基本和高级 Windows ACL。 可以通过装载共享，然后使用 Windows 文件资源管理器、运行 Windows [icacls](/windows-server/administration/windows-commands/icacls) 命令或 [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) 命令，查看并配置 Azure 文件共享中目录和文件的 Windows ACL。 

若要使用超级用户权限配置 ACL，必须使用存储帐户密钥从已加入域的 VM 装载共享。 请按照下一节中的说明操作，从命令提示符装载 Azure 文件共享，并配置 Windows ACL。

文件共享的根目录包含以下权限：

- BUILTIN\Administrators:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

|用户|定义|
|---|---|
|BUILTIN\Administrators|作为本地 AD DS 环境的域管理员的所有用户。
|BUILTIN\Users|AD 中的内置安全组。 默认情况下，它包括 NT AUTHORITY\Authenticated Users。 对于传统的文件服务器，可以为每个服务器配置成员身份定义。 对于 Azure 文件存储，没有托管服务器，因此 BUILTIN\Users 包含与 NT AUTHORITY\Authenticated Users 相同的一组用户。|
|NT AUTHORITY\SYSTEM|文件服务器操作系统的服务帐户。 此类服务帐户在 Azure 文件存储上下文中不适用。 它包含在根目录中，以便在混合方案中与 Windows 文件服务器体验保持一致。|
|NT AUTHORITY\Authenticated Users|AD 中可获取有效 Kerberos 令牌的所有用户。|
|CREATOR OWNER|每个对象（目录或文件）都具有对应的所有者。 如果在该对象上有分配给“CREATOR OWNER”的 ACL，则作为此对象所有者的用户具有对 ACL 定义的对象的权限。|



## <a name="mount-a-file-share-from-the-command-prompt"></a>从命令提示符装载文件共享

使用 Windows `net use` 命令装载 Azure 文件共享。 请务必将下面示例中的占位符值替换为你自己的值。 有关装载文件共享的详细信息，请参阅 [将 Azure 文件共享与 Windows 配合使用](storage-how-to-use-files-windows.md)。 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
  net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
} 
else 
{
  Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN,   Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

如果连接到 Azure 文件存储时遇到问题，请查看[我们在 Windows 上发布的 Azure 文件存储装载错误排查工具](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/)。 我们还提供了[指南](./storage-files-faq.md#on-premises-access)来解决端口 445 被阻止时的情况。 

## <a name="configure-windows-acls"></a>配置 Windows ACL

使用存储帐户密钥装载文件共享后，必须配置 Windows ACL（也称为 NTFS 权限）。 可以使用 Windows 文件资源管理器或 icacls 配置 Windows ACL。

如果在针对 AD DS 标识配置了 Windows DACL 的本地文件服务器中有目录或文件，则可以使用传统文件复制工具（如 Robocopy 或 [Azure AzCopy v 10.4+](https://github.com/Azure/azure-storage-azcopy/releases)）将其复制到 Azure 文件存储以保留 ACL。 如果通过 Azure 文件同步将目录和文件分层到 Azure 文件存储，则会以本机格式移动并保留 ACL。

### <a name="configure-windows-acls-with-icacls"></a>使用 icacls 配置 Windows ACL

使用以下 Windows 命令为文件共享（包括根目录）下的所有目录和文件授予完全权限。 请务必将示例中的占位符值替换为你自己的值。

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

若要详细了解如何使用 icacls 设置 Windows ACL，以及各种受支持的权限，请参阅 [icacls 的命令行参考](/windows-server/administration/windows-commands/icacls)。

### <a name="configure-windows-acls-with-windows-file-explorer"></a>使用 Windows 文件资源管理器配置 Windows ACL

使用 Windows 文件资源管理器为文件共享（包括根目录）下的所有目录和文件授予完全权限。 如果无法在 Windows 文件资源管理器中正确加载 AD 域信息，这可能是由于本地 AD 环境中的信任配置引起的。 客户端计算机无法访问为 Azure 文件存储身份验证注册的 AD 域控制器。 在这种情况下，请使用 icacls 配置 Windows ACL。

1. 打开 Windows 文件资源管理器，右键单击文件/目录，然后选择“属性”。
1. 选择“安全”选项卡。
1. 选择“编辑…” 更改权限。
1. 可以更改现有用户的权限，也可以选择“添加...”向新用户授予权限。
1. 在添加新用户的提示窗口中，在“输入要选择的对象名称”框中输入要向其授予权限的目标用户名，然后选择“检查名称”以查找目标用户的完整 UPN 名称 。
1.    选择“确定”。
1.    在“安全性”选项卡中，选择要授予新用户的所有权限。
1.    选择“应用”。


## <a name="next-steps"></a>后续步骤

启用并配置此功能后，请继续阅读下一篇文章，从已加入域的 VM 装载 Azure 文件共享。

[第四部分：从加入域的 VM 装载文件共享](storage-files-identity-ad-ds-mount-file-share.md)
