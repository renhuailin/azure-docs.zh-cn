---
title: 在 Windows 上装载 SMB Azure 文件共享 | Microsoft Docs
description: 了解如何在 Windows 和 Windows Server 中使用 Azure 文件共享。 在本地或 Azure VM 上运行的 Windows 安装上配合使用 Azure 文件共享与 SMB 3.x。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fecf2ea565343ad2f91471ba1be98df513b55478
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112115705"
---
# <a name="mount-smb-azure-file-share-on-windows"></a>在 Windows 上装载 SMB Azure 文件共享
[Azure 文件](storage-files-introduction.md)是 Microsoft 推出的易用云文件系统。 Azure 文件共享可以在 Windows 和 Windows Server 中无缝使用。 本文介绍在 Windows 和 Windows Server 中使用 Azure 文件共享时的注意事项。

若要在托管某个 Azure 文件共享的 Azure 区域（例如本地或其他 Azure 区域）外部通过公共终结点使用该文件共享，OS 必须支持 SMB 3.x。 仅支持 SMB 2.1 的旧版 Windows 无法通过公共终结点装载 Azure 文件共享。

| Windows 版本 | SMB 版本 | 最大 SMB 通道加密 |
|-|-|-|-|
| Windows 10，版本 21H1 | SMB 3.1.1 | AES-256-GCM |
| Windows Server 半年频道，版本 21H1 | SMB 3.1.1 | AES-256-GCM |
| Windows Server 2019 | SMB 3.1.1 | AES-128-GCM |
| Windows 10<br />版本：1607、1809、1909、2004 和 20H2 | SMB 3.1.1 | AES-128-GCM |
| Windows Server 半年频道<br />版本：2004 和 20H2 | SMB 3.1.1 | AES-128-GCM |
| Windows Server 2016 | SMB 3.1.1 | AES-128-GCM |
| Windows 10 版本 1507 | SMB 3.0 | AES-128-GCM |
| Windows 8.1 | SMB 3.0 | AES-128-CCM |
| Windows Server 2012 R2 | SMB 3.0 | AES-128-CCM |
| Windows Server 2012 | SMB 3.0 | AES-128-CCM |
| Windows 7<sup>1</sup> | SMB 2.1 | 不支持 |
| Windows Server 2008 R2<sup>1</sup> | SMB 2.1 | 不支持 |

<sup>1</sup>Microsoft 对 Windows 7 和 Windows Server 2008 R2 的常规支持已结束。 只有通过[扩展安全更新 (ESU) 程序](https://support.microsoft.com/help/4497181/lifecycle-faq-extended-security-updates)才能购买对安全更新的附加支持。 我们强烈建议从这些操作系统中迁移。

> [!Note]  
> 我们始终建议使用相对于 Windows 版本来说最新的 KB。

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |

## <a name="prerequisites"></a>先决条件 
确保端口 445 处于打开状态：SMB 协议要求 TCP 端口 445 处于打开状态；如果端口 445 已被阻止，连接将会失败。 可以使用 `Test-NetConnection` cmdlet 检查防火墙是否正在阻止端口 445。 若要了解如何解决 445 端口被阻止的问题，请参阅 Windows 故障排除指南的[原因 1：端口 445 被阻止](storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked)部分。

## <a name="using-an-azure-file-share-with-windows"></a>在 Windows 中使用 Azure 文件共享
若要在 Windows 中使用某个 Azure 文件共享，必须装载该文件共享（为其分配驱动器号或装载点路径），或通过其 [UNC 路径](/windows/win32/fileio/naming-a-file)来访问它。 

本文使用存储帐户密钥来访问文件共享。 存储帐户密钥是用于存储帐户的管理员密钥，包括对你要访问的文件共享中所有文件和文件夹的管理员权限，以及对所有文件共享和其他包含在存储帐户中的存储资源（Blob、队列、表等）的权限。 如果这对你的工作负载来说还不够，可使用 [Azure 文件同步](../file-sync/file-sync-planning.md)，或者可使用[通过 SMB 的基于标识的身份验证](storage-files-active-directory-overview.md)。

若要将预期使用 SMB 文件共享的业务线 (LOB) 应用程序直接迁移到 Azure，通常的模式是使用 Azure 文件共享，而不是在 Azure VM 中运行专用的 Windows 文件服务器。 成功迁移业务线应用程序以使用 Azure 文件共享的一个重要注意事项是，许多业务线应用程序在具有有限系统权限的专用服务帐户的上下文中运行，而不是在 VM 的管理帐户下运行。 因此，必须确保装载/保存服务帐户上下文（而不是管理帐户）中 Azure 文件共享的凭据。

### <a name="mount-the-azure-file-share"></a>装载 Azure 文件共享

Azure 门户为你提供了一个脚本，你可以使用该脚本将文件共享直接装载到主机。 我们建议使用这个提供的脚本。

若要获取此脚本，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 导航到包含要装载的文件共享的存储帐户。
1. 选择“文件共享”。
1. 选择要装载的文件共享。

    :::image type="content" source="media/storage-how-to-use-files-windows/select-file-shares.png" alt-text="文件共享边栏选项卡的屏幕截图，其中突出显示了文件共享。":::

1. 选择“连接” 。

    :::image type="content" source="media/storage-how-to-use-files-windows/file-share-connect-icon.png" alt-text="文件共享的“连接”图标的屏幕截图。":::

1. 选择要将共享装载到的驱动器号。
1. 复制所提供的脚本。

    :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="连接边栏选项卡的屏幕截图，其中突出显示了脚本上的复制按钮。":::

1. 将脚本粘贴到你要将文件共享装载到的主机上的 shell 中，然后运行该脚本。

现已装载 Azure 文件共享。

### <a name="mount-the-azure-file-share-with-file-explorer"></a>使用文件资源管理器装载 Azure 文件共享
> [!Note]  
> 请注意，以下说明是在 Windows 10 上显示的，在较旧的版本上可能稍有不同。 

1. 打开文件资源管理器。 可以从“开始”菜单打开，也可以按 Win+E 快捷键打开文件资源管理器。

1. 导航到窗口左侧的“此电脑”。 这样会更改功能区中的可用菜单。 在“计算机”菜单中，选择“映射网络驱动器”。
    
    ![“映射网络驱动器”下拉菜单的屏幕截图](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

1. 选择驱动器号并输入 UNC 路径，UNC 路径格式为 `\\<storageAccountName>.file.core.windows.net\<fileShareName>`。 例如：`\\anexampleaccountname.file.core.windows.net\example-share-name`。
    
    ![“映射网络驱动器”对话框的屏幕截图](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

1. 使用带 `AZURE\` 前缀的存储帐户名称作为用户名，使用存储帐户密钥作为密码。
    
    ![网络凭据对话框的屏幕快照](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

1. 根据需要使用 Azure 文件共享。
    
    ![Azure 文件共享现已装载](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

1. 做好卸载 Azure 文件共享的准备后，可在文件资源管理器中右键单击“网络位置”下对应于共享的条目，并选择“断开连接”。 

### <a name="accessing-share-snapshots-from-windows"></a>从 Windows 访问共享快照
如果已手动或通过脚本或 Azure 备份等服务自动获取共享快照，则可以从 Windows 上的文件共享查看以前版本的共享、目录或特定文件。 可以使用 [Azure PowerShell](storage-how-to-use-files-powershell.md)、[Azure CLI](storage-how-to-use-files-cli.md) 或 [Azure 门户](storage-how-to-use-files-portal.md)创建共享快照。

#### <a name="list-previous-versions"></a>列出以前版本
浏览到需要还原的项或父项。 通过双击转到所需的目录。 右键单击，然后从菜单中选择“属性”。

![所选目录的右键单击菜单](./media/storage-how-to-use-files-windows/snapshot-windows-previous-versions.png)

选择"以前版本”，以查看此目录的共享快照列表。 列表可能需要几秒钟才能加载，具体要取决于网速和目录中共享快照的数量。

![“以前版本”选项卡](./media/storage-how-to-use-files-windows/snapshot-windows-list.png)

可以选择“打开”以打开特定快照。 

![打开的快照](./media/storage-how-to-use-files-windows/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>从以前版本还原
选择“还原”，以递归方式将整个目录在共享快照创建时包含的内容复制到原始位置。

 ![警告消息中的“还原”按钮](./media/storage-how-to-use-files-windows/snapshot-windows-restore.png) 

## <a name="next-steps"></a>后续步骤
请参阅以下链接，获取有关 Azure 文件的更多信息：
- [规划 Azure 文件部署](storage-files-planning.md)
- [常见问题](./storage-files-faq.md)
- [在 Windows 上进行故障排除](storage-troubleshoot-windows-file-connection-problems.md)