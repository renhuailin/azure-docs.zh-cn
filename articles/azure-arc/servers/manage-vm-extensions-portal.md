---
title: 从 Azure 门户启用 VM 扩展
description: 本文介绍如何从 Azure 门户将虚拟机扩展部署到在混合云环境中运行的已启用 Azure Arc 的服务器。
ms.date: 04/13/2021
ms.topic: conceptual
ms.openlocfilehash: b5b4ff79d68ec9ff0cc61b9dbb7d3c5d7fe93598
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388272"
---
# <a name="enable-azure-vm-extensions-from-the-azure-portal"></a>从 Azure 门户启用 Azure VM 扩展

本文介绍如何通过 Azure 门户将已启用 Azure Arc 的服务器支持的 Azure VM 扩展部署到 Linux 或 Windows 混合计算机，以及如何将其卸载。

> [!NOTE]
> Key Vault VM 扩展（预览版）不支持从 Azure 门户进行部署，仅支持使用 Azure CLI、Azure PowerShell 或 Azure 资源管理器模板进行部署。

> [!NOTE]
> 已启用 Azure Arc 的服务器不支持管理 VM 扩展，也不支持将 VM 扩展部署到 Azure 虚拟机。 对于 Azure VM，请查看下面的 [VM 扩展概述](../../virtual-machines/extensions/overview.md)一文。

## <a name="enable-extensions-from-the-portal"></a>从门户启用扩展

可通过 Azure 门户将 VM 扩展应用到用于服务器托管计算机的 Arc。

1. 在浏览器中转到 [Azure 门户](https://portal.azure.com)。

2. 在门户中，浏览到“服务器 - Azure Arc”并从列表中选择你的混合计算机。

3. 依次选择“扩展”、“添加” 。 从可用扩展的列表中选择所需扩展，并按向导中的说明操作。 在此示例中，我们将部署 Log Analytics VM 扩展。

    ![选择所选计算机的 VM 扩展](./media/manage-vm-extensions/add-vm-extensions.png)

    以下示例演示如何从 Azure 门户安装 Log Analytics VM 扩展：

    ![安装 Log Analytics VM 扩展](./media/manage-vm-extensions/mma-extension-config.png)

    若要完成安装，需要提供工作区 ID 和主密钥。 如果你不熟悉如何查找此信息，请参阅[获取工作区 ID 和密钥](../../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)。

4. 确认提供了所需的信息后，选择“创建”。 随后将显示部署摘要，你可以查看部署状态。

>[!NOTE]
>虽然可将多个扩展一同进行批处理，但这些扩展是依次逐个安装的。 第一个扩展安装完成后，将尝试安装下一个扩展。

## <a name="list-extensions-installed"></a>列出已安装的扩展

可以从 Azure 门户获取已启用 Arc 的服务器上的 VM 扩展列表。 执行以下步骤可查看这些扩展。

1. 在浏览器中转到 [Azure 门户](https://portal.azure.com)。

2. 在门户中，浏览到“服务器 - Azure Arc”并从列表中选择你的混合计算机。

3. 选择“扩展”，然后会返回已安装的扩展列表。

    ![列出已部署到所选计算机的 VM 扩展](./media/manage-vm-extensions/list-vm-extensions.png)

## <a name="uninstall-extension"></a>卸载扩展

可以从 Azure 门户删除已启用 Arc 的服务器中的一个或多个扩展。 执行以下步骤可删除扩展。

1. 在浏览器中转到 [Azure 门户](https://portal.azure.com)。

2. 在门户中，浏览到“服务器 - Azure Arc”并从列表中选择你的混合计算机。

3. 选择“扩展”，然后从已安装的扩展列表中选择一个扩展。

4. 选择“卸载”，系统提示确认时，选择“是”以继续 。

## <a name="next-steps"></a>后续步骤

- 可以使用 [Azure CLI](manage-vm-extensions-cli.md)、[PowerShell](manage-vm-extensions-powershell.md) 或 [Azure 资源管理器模板](manage-vm-extensions-template.md)来部署、管理和删除 VM 扩展。

- 可以在 [VM 扩展故障排除指南](troubleshoot-vm-extensions.md)中找到故障排除信息。