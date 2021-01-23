---
title: Azure 门户启用 VM 扩展
description: 本文介绍如何在 Azure 门户的混合云环境中，将虚拟机扩展部署到已启用 Azure Arc 的服务器。
ms.date: 01/22/2020
ms.topic: conceptual
ms.openlocfilehash: 43bbcef28b77e7c7112880fdac1bbd4809791cef
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98728937"
---
# <a name="enable-azure-vm-extensions-from-the-azure-portal"></a>启用 Azure 门户的 Azure VM 扩展

本文介绍如何通过 Azure 门户部署和卸载启用了 Azure Arc 的服务器支持的 Azure VM 扩展到 Linux 或 Windows 混合计算机。

> [!NOTE]
> Key Vault VM 扩展 (预览版) 不支持从 Azure 门户进行部署，只使用 Azure CLI、Azure PowerShell 或使用 Azure 资源管理器模板。

## <a name="enable-extensions-from-the-portal"></a>从门户启用扩展

VM 扩展可通过 Azure 门户应用服务器托管计算机的 Arc。

1. 在浏览器中转到 [Azure 门户](https://portal.azure.com)。

2. 在门户中，浏览到 " **服务器-Azure Arc** "，然后从列表中选择你的混合计算机。

3. 选择 " **扩展**"，然后选择 " **添加**"。 从可用扩展的列表中选择所需扩展，并按向导中的说明操作。 在此示例中，我们将部署 Log Analytics VM 扩展。

    ![选择所选计算机的 VM 扩展](./media/manage-vm-extensions/add-vm-extensions.png)

    下面的示例演示如何从 Azure 门户安装 Log Analytics VM 扩展：

    ![安装 Log Analytics VM 扩展](./media/manage-vm-extensions/mma-extension-config.png)

    若要完成安装，你需要提供工作区 ID 和主密钥。 如果你不熟悉如何查找此信息，请参阅 [获取工作区 ID 和密钥](../../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key)。

4. 确认提供所需信息后，选择 " **创建**"。 将显示部署的摘要，你可以查看部署的状态。

>[!NOTE]
>虽然可以将多个扩展组合在一起并进行处理，但它们是以串行方式安装的。 第一次扩展安装完成后，将尝试安装下一扩展。

## <a name="list-extensions-installed"></a>列出已安装的扩展

可以从 Azure 门户获取已启用 Arc 的服务器上的 VM 扩展列表。 执行以下步骤以查看它们。

1. 在浏览器中转到 [Azure 门户](https://portal.azure.com)。

2. 在门户中，浏览到 " **服务器-Azure Arc** "，然后从列表中选择你的混合计算机。

3. 选择 " **扩展**"，并返回已安装的扩展的列表。

    ![列出部署到所选计算机的 VM 扩展](./media/manage-vm-extensions/list-vm-extensions.png)

## <a name="uninstall-extension"></a>卸载扩展

可以从 Azure 门户中删除已启用 Arc 的服务器中的一个或多个扩展。 执行以下步骤以删除扩展。

1. 在浏览器中转到 [Azure 门户](https://portal.azure.com)。

2. 在门户中，浏览到 " **服务器-Azure Arc** "，然后从列表中选择你的混合计算机。

3. 选择 " **扩展**"，然后从已安装的扩展列表中选择一个扩展。

4. 选择 " **卸载** "，并在出现提示时选择 **"是"** 以继续。

## <a name="next-steps"></a>后续步骤

- 你可以使用 [Azure CLI](manage-vm-extensions-cli.md)、 [PowerShell](manage-vm-extensions-powershell.md)或 [Azure 资源管理器模板](manage-vm-extensions-template.md)来部署、管理和删除 VM 扩展。

- 疑难解答信息可在 [VM 扩展疑难解答指南](troubleshoot-vm-extensions.md)中找到。