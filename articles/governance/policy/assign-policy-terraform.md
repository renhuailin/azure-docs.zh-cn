---
title: 快速入门：通过 Terraform 进行新策略分配
description: 本快速入门介绍如何使用 Terraform 和 HCL 语法创建策略分配以识别不合规的资源。
ms.date: 08/17/2021
ms.topic: quickstart
ms.openlocfilehash: 09e36e8b73fd224d7ab9c94dceaa7e02b34c9b8b
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323445"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-terraform"></a>快速入门：使用 Terraform 创建策略分配以识别不合规的资源

若要了解 Azure 中的符合性，第一步是确定资源的状态。
本快速入门逐步讲解如何创建策略分配，以识别未使用托管磁盘的虚拟机。

此过程结束时，你可以成功识别哪些虚拟机未使用托管磁盘。 这些虚拟机不符合策略分配要求。

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。
- 在你的环境中配置 [Terraform](https://www.terraform.io/) 版本 0.12.0 或更高版本。
  有关说明，请参阅[使用 Azure Cloud Shell 配置 Terraform](/azure/developer/terraform/get-started-cloud-shell)。
- 本快速入门需要运行 Azure CLI 2.13.0 或更高版本。 若要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-the-terraform-configuration-variable-and-output-file"></a>创建 Terraform 配置、变量和输出文件

本快速入门将创建一个策略分配，并分配“审核未使用托管磁盘的 VM”(`06a78e20-9358-41c9-923c-fb736d382a4d`) 定义。 此策略定义可识别不符合策略定义中设置的条件的资源。

首先，配置 Terraform 配置、变量和输出文件。 Azure Policy 的 Terraform 资源使用 [Azure 提供程序](https://www.terraform.io/docs/providers/azurerm/index.html)。

1. 创建名为 `policy-assignment` 的新文件夹，并将目录更改到其中。

1. 使用以下代码创建 `main.tf`：

   ```hcl
   provider "azurerm" {
       version = "~>2.0"
       features {}
   }

   resource "azurerm_policy_assignment" "auditvms" {
       name = "audit-vm-manageddisks"
       scope = var.cust_scope
       policy_definition_id = "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d"
       description = "Shows all virtual machines not using managed disks"
       display_name = "Audit VMs without managed disks Assignment"
   }
   ```

1. 使用以下代码创建 `variables.tf`：

   ```hcl
   variable "cust_scope" {
       default = "{scope}"
   }
   ```

   范围用于确定对其强制执行策略分配的资源或资源组。 它的范围可以从管理组到单个资源。 请确保将 `{scope}` 替换为以下某个模式：

   - 订阅：`/subscriptions/{subscriptionId}`
   - 资源组：`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
   - 资源：`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

1. 使用以下代码创建 `output.tf`：

   ```hcl
   output "assignment_id" {
       value = azurerm_policy_assignment.auditvms.id
   }
   ```

## <a name="initialize-terraform-and-create-plan"></a>初始化 Terraform 并创建计划

接下来，初始化 Terraform 以下载所需的提供程序，然后创建计划。

1. 运行 [terraform init](https://www.terraform.io/docs/commands/init.html) 命令。 此命令将下载在 Terraform 配置中创建 Azure 资源所需的 Azure 模块。

   ```bash
   terraform init
   ```

   :::image type="content" source="./media/assign-policy-terraform/terraform-initialize.png" alt-text="运行 terraform init 命令的屏幕截图，其中显示了下载 azurerm 模块的过程和一条成功消息。":::

1. 使用 [Azure CLI](/cli/azure/) 为 Terraform 进行身份验证。 有关详细信息，请参阅[Azure 提供程序：使用 Azure CLI 进行身份验证](https://www.terraform.io/docs/providers/azurerm/guides/azure_cli.html)。

   ```azurecli
   az login
   ```

1. 使用 [terraform plan](https://www.terraform.io/docs/commands/plan.html) 命令和 out 参数创建执行计划。

   ```bash
   terraform plan -out assignment.tfplan
   ```

   :::image type="content" source="./media/assign-policy-terraform/terraform-plan-out.png" alt-text="运行 terraform plan 命令和 out 参数（用于显示将创建的 Azure 资源）的屏幕截图。":::

   > [!NOTE]
   > 有关使执行计划和安全性持久化的信息，请参阅 [Terraform 计划：安全警告](https://www.terraform.io/docs/commands/plan.html#security-warning)。

## <a name="apply-the-terraform-execution-plan"></a>应用 Terraform 执行计划

最后，应用执行计划。

运行 [terraform apply](https://www.terraform.io/docs/commands/apply.html) 命令并指定已创建的 `assignment.tfplan`。

```bash
terraform apply assignment.tfplan
```

:::image type="content" source="./media/assign-policy-terraform/terraform-apply.png" alt-text="运行 terraform apply 命令以及随后创建的资源的屏幕截图。":::

显示“应用完成! 资源：1 个已添加，0 个已更改，0 个已销毁。” 消息，表示策略分配现已创建。 由于我们已定义 `outputs.tf` 文件，因此还会返回 assignment\_id。

## <a name="identify-non-compliant-resources"></a>识别不合规的资源

若要查看此新分配下不合规的资源，请使用由 `terraform apply` 返回的 assignment\_id。 使用它，运行以下命令，获取输出到 JSON 文件中的不合规资源的资源 ID：

```console
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

结果应如以下示例所示：

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

这些结果与 Azure 门户视图中“不合规资源”下通常所列的结果类似。 

## <a name="clean-up-resources"></a>清理资源

若要删除创建的分配，请使用 Azure CLI，或使用 `terraform destroy` 撤销 Terraform 执行计划。

- Azure CLI

  ```azurecli-interactive
  az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
  ```

- Terraform

  ```bash
  terraform destroy assignment.tfplan
  ```

## <a name="next-steps"></a>后续步骤

本快速入门已分配一个策略定义用于识别 Azure 环境中的不合规资源。

要了解有关分配策略以验证新资源是否符合要求的详细信息，请继续以下教程：

> [!div class="nextstepaction"]
> [创建和管理策略](./tutorials/create-and-manage.md)
