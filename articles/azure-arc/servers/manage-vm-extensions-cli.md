---
title: 使用 Azure CLI 启用 VM 扩展
description: 本文介绍如何使用 Azure CLI 将虚拟机扩展部署到在混合云环境中运行的已启用 Azure Arc 的服务器。
ms.date: 08/05/2021
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: e493e035caf3a201d3670bb352c19455b3cfee72
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750241"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>使用 Azure CLI 启用 Azure VM 扩展

本文介绍如何使用 Azure CLI 将已启用 Azure Arc 的服务器支持的 VM 扩展部署到 Linux 或 Windows 混合计算机并将其卸载。

> [!NOTE]
> 已启用 Azure Arc 的服务器不支持管理 VM 扩展，也不支持将 VM 扩展部署到 Azure 虚拟机。 对于 Azure VM，请查看下面的 [VM 扩展概述](../../virtual-machines/extensions/overview.md)一文。

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="install-the-azure-cli-extension"></a>安装 Azure CLI 扩展

ConnectedMachine 命令不会作为 Azure CLI 的一部分交付。 使用 Azure CLI 在已启用 Arc 的服务器管理的混合服务器上管理 VM 扩展之前，需要加载 ConnectedMachine 扩展。 运行以下命令可获取该扩展：

```azurecli
az extension add --name connectedmachine
```

## <a name="enable-extension"></a>启用扩展

若要在已启用 Arc 的服务器上启用 VM 扩展，请结合使用 [az connectedmachine extension create](/cli/azure/connectedmachine/extension#az_connectedmachine_extension_create) 命令以及 `--machine-name`、`--extension-name`、`--location`、`--type`、`settings` 和 `--publisher` 参数。

以下示例将在已启用 Arc 的服务器上启用 Log Analytics VM 扩展：

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "OmsAgentForLinux or MicrosoftMonitoringAgent" --location "eastus" --settings '{\"workspaceId\":\"myWorkspaceId\"}' --protected-settings '{\"workspaceKey\":\"myWorkspaceKey\"}' --resource-group "myResourceGroup" --type-handler-version "1.13" --type "OmsAgentForLinux or MicrosoftMonitoringAgent" --publisher "Microsoft.EnterpriseCloud.Monitoring" 
```

以下示例将在已启用 Arc 的服务器上启用自定义脚本扩展：

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

以下示例将在已启用 Arc 的服务器上启用 Key Vault VM 扩展：

```azurecli
az connectedmachine extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.KeyVault" --type "KeyVaultForLinux or KeyVaultForWindows" --name "KeyVaultForLinux or KeyVaultForWindows" --settings '{"secretsManagementSettings": { "pollingIntervalInS": "60", "observedCertificates": ["observedCert1"] }, "authenticationSettings": { "msiEndpoint": "http://localhost:40342/metadata/identity" }}'
```

## <a name="list-extensions-installed"></a>列出已安装的扩展

要获取已启用 Arc 的服务器上的 VM 扩展列表，请结合使用 [az connectedmachine extension list](/cli/azure/connectedmachine/extension#az_connectedmachine_extension_list) 命令以及 `--machine-name` 和 `--resource-group` 参数。

示例：

```azurecli
az connectedmachine extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

默认情况下，Azure CLI 命令的输出采用 JSON（JavaScript 对象表示法）格式。 例如，若要更改列表或表的默认输出，请使用 [az config set core.output=table](/cli/azure/reference-index)。 还可以向任何命令添加 `--output` 来对输出格式进行一次性更改。

以下示例显示了 `az connectedmachine extension -list` 命令的部分 JSON 输出：

```json
[
  {
    "autoUpgradingMinorVersion": "false",
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.HybridCompute/machines/SVR01/extensions/DependencyAgentWindows",
    "location": "eastus",
    "name": "DependencyAgentWindows",
    "namePropertiesInstanceViewName": "DependencyAgentWindows",
```

## <a name="remove-an-installed-extension"></a>删除已安装的扩展

要删除已启用 Arc 的服务器上安装的 VM 扩展，请结合使用 [az connectedmachine extension delete](/cli/azure/connectedmachine/extension#az_connectedmachine_extension_delete) 命令以及 `--extension-name`、`--machine-name` 和 `--resource-group` 参数。

例如，要删除适用于 Linux 的日志分析 VM 扩展，请运行以下命令：

```azurecli
az connectedmachine extension delete --machine-name "myMachineName" --name "OmsAgentForLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>后续步骤

- 可以使用 [Azure PowerShell](manage-vm-extensions-powershell.md) 在 [Azure 门户](manage-vm-extensions-portal.md)或 [Azure 资源管理器模板](manage-vm-extensions-template.md)中部署、管理和删除 VM 扩展。

- 有关故障排除信息，请参阅《[VM 扩展故障排除指南](troubleshoot-vm-extensions.md)》。

- 有关命令的详细信息，请参阅 Azure CLI VM 扩展“[概述](/cli/azure/connectedmachine/extension)”一文。
