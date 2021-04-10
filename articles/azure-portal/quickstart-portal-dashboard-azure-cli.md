---
title: 使用 Azure CLI 创建 Azure 门户仪表板
description: 快速入门：了解如何使用 Azure CLI 在 Azure 门户中创建仪表板。 仪表板可以集中且有组织地呈现你的云资源。
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.date: 12/4/2020
ms.openlocfilehash: ddfee1932c6887c6ca7593ca7a28c03e68e09899
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104613173"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-azure-cli"></a>快速入门：使用 Azure CLI 创建 Azure 门户仪表板

Azure 门户中的仪表板可以集中且有组织地呈现你的云资源。 本文重点介绍使用 Azure CLI 创建仪表板的过程。
仪表板会显示虚拟机 (VM) 的性能以及一些静态信息和链接。


[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 如果有多个 Azure 订阅，请选择在其中对资源进行计费的相应订阅。
使用 [az account set](/cli/azure/account#az_account_set) 命令选择订阅：

  ```azurecli
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```

- 使用 [az group create](/cli/azure/group#az_group_create) 命令或使用现有资源组创建 [Azure 资源组](../azure-resource-manager/management/overview.md)：

  ```azurecli
  az group create --name myResourceGroup --location centralus
  ```

   资源组是在其中以组的形式部署和管理 Azure 资源的逻辑容器。

## <a name="create-a-virtual-machine"></a>创建虚拟机

使用 [az vm create](/cli/azure/vm#az_vm_create) 命令创建虚拟机：

```azurecli
az vm create --resource-group myResourceGroup --name SimpleWinVM --image win2016datacenter \
   --admin-username azureuser --admin-password 1StrongPassword$
```

> [!Note]
> 密码必须是复杂密码。
> 这是新的用户名和密码。
> 它不是用于登录 Azure 的帐户。
> 有关详细信息，请参阅[用户名要求](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm)和[密码要求](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)。

部署开始进行，通常需要几分钟才能完成。
部署完成后，请转到下一部分。

## <a name="download-the-dashboard-template"></a>下载仪表板模板

由于 Azure 仪表板是资源，所以它们可以表示为 JSON。
有关详细信息，请参阅 [Azure 仪表板结构](./azure-portal-dashboards-structure.md)。

下载以下文件：[portal-dashboard-template-testvm.json](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json)。

通过将以下值更改为你的值来自定义下载的模板：

* `<subscriptionID>`：订阅
* `<rgName>`：资源组，例如 `myResourceGroup`
* `<vmName>`：虚拟机名称，例如 `SimpleWinVM`
* `<dashboardTitle>`：仪表板标题，例如 `Simple VM Dashboard`
* `<location>`：Azure 区域，例如 `centralus`

有关详细信息，请参阅 [Microsoft 门户仪表板模板参考](/azure/templates/microsoft.portal/dashboards)。

## <a name="deploy-the-dashboard-template"></a>部署仪表板模板

现在可以从 Azure CLI 中部署模板。

1. 运行 [az portal dashboard create](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_create) 命令来部署模板：

   ```azurecli
   az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
      --input-path portal-dashboard-template-testvm.json --location centralus
   ```

1. 通过运行 [az portal dashboard show](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_show) 命令检查是否已成功创建仪表板：

   ```azurecli
   az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
   ```

若要查看当前订阅的所有仪表板，请使用 [az portal dashboard list](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_list)：

```azurecli
az portal dashboard list
```

还可以查看资源组的所有仪表板：

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

可以使用 [az portal dashboard update](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_update) 命令更新仪表板：

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

[!INCLUDE [azure-portal-review-deployed-resources](../../includes/azure-portal-review-deployed-resources.md)]

## <a name="clean-up-resources"></a>清理资源

若要删除虚拟机和关联仪表板，请删除包含它们的资源组。

> [!CAUTION]
> 以下示例删除指定的资源组及其包含的所有资源。
> 如果指定的资源组中存在本文范围外的资源，这些资源也会被删除。

```azurecli
az group delete --name myResourceGroup
```

如需仅删除仪表板，请使用 [az portal dashboard delete](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_delete) 命令：

```azurecli
az portal dashboard delete --resource-group myResourceGroup --name "Simple VM Dashboard"
```

## <a name="next-steps"></a>后续步骤

有关对仪表板的 Azure CLI 支持的详细信息，请参阅 [az portal dashboard](/cli/azure/ext/portal/portal/dashboard)。
