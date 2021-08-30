---
title: 从 Azure 门户启用 VM 扩展
description: 本文介绍如何从 Azure 门户将虚拟机扩展部署到在混合云环境中运行的已启用 Azure Arc 的服务器。
ms.date: 08/11/2021
ms.topic: conceptual
ms.openlocfilehash: 740ee7954340da0a34b581356d51135033a3829d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727362"
---
# <a name="enable-azure-vm-extensions-from-the-azure-portal"></a>从 Azure 门户启用 Azure VM 扩展

本文介绍如何使用 Azure 门户将已启用 Azure Arc 的服务器支持的 Azure VM 扩展部署到 Linux 或 Windows 混合计算机，以及如何更新并将其卸载。

> [!NOTE]
> Key Vault VM 扩展不支持通过 Azure 门户进行部署，仅支持使用 Azure CLI、Azure PowerShell 或 Azure 资源管理器模板进行部署。

> [!NOTE]
> 已启用 Azure Arc 的服务器不支持将 VM 扩展部署到 Azure 虚拟机，也不支持管理 VM 扩展。 对于 Azure VM，请查看下面的 [VM 扩展概述](../../virtual-machines/extensions/overview.md)一文。

## <a name="enable-extensions"></a>启用扩展

可使用 Azure 门户将 VM 扩展应用到已启用 Arc 的服务器托管计算机。

1. 在浏览器中转到 [Azure 门户](https://portal.azure.com)。

2. 在门户中，浏览到“服务器 - Azure Arc”并从列表中选择你的混合计算机。

3. 依次选择“扩展”、“添加” 。 从可用扩展的列表中选择所需扩展，并按向导中的说明操作。 在此示例中，我们将部署 Log Analytics VM 扩展。

    ![选择所选计算机的 VM 扩展](./media/manage-vm-extensions/add-vm-extensions.png)

    以下示例演示如何从 Azure 门户安装 Log Analytics VM 扩展：

    ![安装 Log Analytics VM 扩展](./media/manage-vm-extensions/mma-extension-config.png)

    若要完成安装，需要提供工作区 ID 和主密钥。 如果你不熟悉如何查找此信息，请参阅[获取工作区 ID 和密钥](../../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)。

4. 确认提供了所需的信息后，选择“查看 + 创建”。 随后将显示部署摘要，你可以查看部署状态。

>[!NOTE]
>虽然可将多个扩展一同进行批处理，但这些扩展是依次逐个安装的。 第一个扩展安装完成后，将尝试安装下一个扩展。

## <a name="list-extensions-installed"></a>列出已安装的扩展

可以通过 Azure 门户获取已启用 Arc 的服务器上的 VM 扩展列表。 执行以下步骤可查看这些扩展。

1. 在浏览器中转到 [Azure 门户](https://portal.azure.com)。

2. 在门户中，浏览到“服务器 - Azure Arc”并从列表中选择你的混合计算机。

3. 选择“扩展”，然后会返回已安装的扩展列表。

    :::image type="content" source="media/manage-vm-extensions/list-vm-extensions.png" alt-text="列出已部署到所选计算机的 VM 扩展。" border="true":::

## <a name="update-extensions"></a>更新扩展

支持的扩展的新版本发布后，可将该扩展更新到这一最新版本。 导航到已启用 Arc 的服务器时，它们将在 Azure 门户显示横幅，告知你计算机上安装的一个或多个扩展有可用更新。 为选定的已启用 Arc 的服务器查看已安装的扩展列表时，会发现一个标记为“可用更新”的列。 如果发布了较新版本的扩展，该扩展的“可用更新”值将显示“是” 。 

将扩展更新到最新版本不会影响该扩展的配置。 你无需为更新的任何扩展重新指定配置信息。

:::image type="content" source="media/manage-vm-extensions-portal/vm-extensions-update-status.png" alt-text="列出 VM 扩展更新状态。" border="true":::

可执行以下步骤，通过 Azure 门户更新某个扩展或选择多个符合更新条件的扩展。

> [!NOTE]
> 目前只能通过 Azure 门户更新扩展。 当前不支持通过 Azure CLI、Azure PowerShell 或使用 Azure 资源管理器模板执行此操作。

1. 在浏览器中转到 [Azure 门户](https://portal.azure.com)。

2. 在门户中，浏览到“服务器 - Azure Arc”并从列表中选择你的混合计算机。

3. 选择“扩展”，然后查看“可用更新”列下的扩展状态 。 

可通过以下三种方式之一更新扩展：

* 从已安装的扩展列表中选择扩展，在扩展的属性下，选择“更新”选项。

    :::image type="content" source="media/manage-vm-extensions-portal/vm-extensions-update-from-extension.png" alt-text="从所选扩展中更新扩展。" border="true":::

* 从已安装的扩展的列表中选择扩展，然后从页面顶部选择“更新”选项。

* 从已安装的扩展的列表中选择一个或多个符合更新条件的扩展，然后选择“更新”选项。

    :::image type="content" source="media/manage-vm-extensions-portal/vm-extensions-update-selected.png" alt-text="更新所选扩展。" border="true":::

## <a name="uninstall-extensions"></a>卸载扩展

可以通过 Azure 门户删除已启用 Arc 的服务器中的一个或多个扩展。 执行以下步骤可删除扩展。

1. 在浏览器中转到 [Azure 门户](https://portal.azure.com)。

2. 在门户中，浏览到“服务器 - Azure Arc”并从列表中选择你的混合计算机。

3. 选择“扩展”，然后从已安装的扩展的列表中选择一个扩展。

4. 选择“卸载”，系统提示确认时，选择“是”以继续 。

## <a name="next-steps"></a>后续步骤

- 可以使用 [Azure CLI](manage-vm-extensions-cli.md)、[PowerShell](manage-vm-extensions-powershell.md) 或 [Azure 资源管理器模板](manage-vm-extensions-template.md)来部署、管理和删除 VM 扩展。

- 可以在 [VM 扩展故障排除指南](troubleshoot-vm-extensions.md)中找到故障排除信息。