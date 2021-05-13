---
title: 从自动化 State Configuration 中删除 DSC 和节点
description: 本文介绍如何删除已分配的 Azure 自动化 State Configuration (DSC) 配置文档以及如何注销托管节点。
titleSuffix: Azure Automation
services: automation
ms.subservice: dsc
ms.date: 04/16/2021
ms.topic: how-to
ms.openlocfilehash: 2db345705933f89a2ce119e65182f88a52f13dc8
ms.sourcegitcommit: 5da0bf89a039290326033f2aff26249bcac1fe17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2021
ms.locfileid: "109716318"
---
# <a name="how-to-remove-a-configuration-and-node-from-automation-state-configuration"></a>如何从自动化 State Configuration 中删除配置和节点

本文介绍如何注销由自动化 State Configuration 托管的节点，以及如何从托管节点安全删除 PowerShell Desired State Configuration (DSC) 配置。 如果是 Windows 和 Linux 节点，你需要[注销节点](#unregister-a-node)并[删除配置](#delete-a-configuration-from-the-azure-portal)。 如果只是 Linux 节点，还可以选择从节点中删除 DSC 包。 请参阅[从 Linux 节点中删除 DSC 包](#remove-the-dsc-package-from-a-linux-node)。

## <a name="unregister-a-node"></a>注销节点

如果不再需要通过 State Configuration (DSC) 来托管节点，可以使用以下步骤在 Azure 门户或通过 Azure PowerShell 将其注销。

要从服务中注销节点，只需设置本地 Configuration Manager 设置，以便节点不再连接到服务。 这不会影响当前应用于节点的配置，并且会将相关文件保留在节点上。 可以选择清理这些文件。 请参阅[删除配置](#delete-a-configuration)。

### <a name="unregister-in-the-azure-portal"></a>在 Azure 门户中注销

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 搜索并选择“自动化帐户”。
1. 在“自动化帐户”页上，从列表中选择你的自动化帐户。
1. 在“自动化帐户”中的“配置管理”下，选择“State Configuration (DSC)”。 
1. 在“State Configuration (DSC)”页上，单击“节点”选项卡。 
1. 在“节点”选项卡上，选择要注销的节点的名称。
1. 在该节点的窗格中，单击“注销”。

   :::image type="content" source="./media/remove-node-and-configuration-package/unregister-node.png" alt-text="“节点详细信息”页的屏幕截图，其中突出显示了“注销”按钮。" lightbox="./media/remove-node-and-configuration-package/unregister-node.png":::

### <a name="unregister-using-powershell"></a>使用 PowerShell 注销

还可使用 PowerShell cmdlet [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode)注销节点。

>[!NOTE]
>如果你的组织仍使用弃用的 AzureRM 模块，则可以使用 [Unregister-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/unregister-azurermautomationdscnode)。

## <a name="delete-a-configuration"></a>删除配置

如果已准备好删除分配给一个或多个节点的导入的 DSC 配置文档（托管对象格式 (MOF)），请按照以下步骤进行操作。

### <a name="delete-a-configuration-from-the-azure-portal"></a>在 Azure 门户中删除配置

可以在 Azure 门户中删除 Windows 和 Linux 节点的配置。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 搜索并选择“自动化帐户”。
1. 在“自动化帐户”页上，从列表中选择你的自动化帐户。
1. 在“自动化帐户”中的“配置管理”下，选择“State Configuration (DSC)”。 
1. 在“State Configuration (DSC)”页上，单击“配置”选项卡，然后选择要删除的配置的名称 。

   :::image type="content" source="./media/remove-node-and-configuration-package/configurations-tab.png" alt-text="“配置”选项卡的屏幕截图。" lightbox="./media/remove-node-and-configuration-package/configurations-tab.png":::

1. 在“配置详细信息”页上，单击“删除”来删除配置。

   :::image type="content" source="./media/remove-node-and-configuration-package/delete-extension.png" alt-text="删除扩展的屏幕截图。" lightbox="./media/remove-node-and-configuration-package/delete-extension.png":::

## <a name="manually-delete-a-configuration-file-from-a-node"></a>从节点手动删除配置文件

如果不想使用 Azure 门户，可以按如下所示手动删除 .mof 配置文件。

### <a name="delete-a-windows-configuration-using-powershell"></a>使用 PowerShell 删除 Windows 配置

若要删除导入的 DSC 配置文档 (.mof)，请使用 [Remove-DscConfigurationDocument](/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument) cmdlet。

### <a name="delete-a-linux-configuration"></a>删除 Linux 配置

配置文件存储在 /etc/opt/omi/conf/dsc/configuration/ 中。 删除此目录中的 .mof 文件以删除节点的配置。

## <a name="remove-the-dsc-package-from-a-linux-node"></a>从 Linux 节点删除 DSC 包

此步骤是可选的。 从 State Configuration (DSC) 中注销 Linux 节点不会从计算机中删除 DSC 和 OMI 包。 使用下面的命令删除包以及所有日志和相关数据。

若要查找包名称和其他相关详细信息，请参阅[适用于 Linux GitHub 存储库的 PowerShell Desired State Configuration](https://github.com/Microsoft/PowerShell-DSC-for-Linux)。

### <a name="rpm-based-systems"></a>基于 RPM 的系统

```bash
RPM -e <package name>
``` 

### <a name="dpkg-based-systems"></a>基于 dpkg 的系统

```bash
dpkg -P <package name>
```

 ## <a name="next-steps"></a>后续步骤

- 如果要重新注册节点或注册新节点，请参阅[注册由 State Configuration 托管的 VM](../tutorial-configure-servers-desired-state.md#register-a-vm-to-be-managed-by-state-configuration)。

- 如果要重新添加配置和重新编译，请参阅[在 Azure 自动化 State Configuration 中编译 DSC 配置](../automation-dsc-compile.md)。