---
title: 在 Azure 自动化混合 Runbook 辅助角色上强制作业执行
description: 本文介绍如何使用自定义的 Azure Policy 定义在 Azure 自动化混合 Runbook 辅助角色上强制作业执行。
services: automation
ms.subservice: process-automation
ms.date: 05/24/2021
ms.topic: conceptual
ms.openlocfilehash: 36ead3f16a04055e7056c702b0600265dd8e61ce
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110482330"
---
# <a name="use-azure-policy-to-enforce-job-execution-on-hybrid-runbook-worker"></a>使用 Azure Policy 在混合 Runbook 辅助角色上强制执行作业

在混合 Runbook 辅助角色上启动 Runbook 会使用“运行位置”选项，该选项允许在从 Azure 门户启动时，通过 Azure PowerShell 或 REST API 指定混合 Runbook 辅助角色组的名称。 指定组时，该组中的其中一个辅助角色检索和运行 Runbook。 如果 Runbook 未指定此选项，Azure 自动化会在 Azure 沙盒中运行 Runbook。 

组织中属于[自动化作业操作员](automation-role-based-access-control.md#automation-job-operator)或更高角色的任何人都可以创建 Runbook 作业。 若要管理针对自动化帐户中混合 Runbook 辅助角色组的 Runbook 执行，可以使用 [Azure Policy](../governance/policy/overview.md)。 这有助于强制实施组织标准，并确保自动化作业由指定的人员控制和管理，且任何人都无法在 Azure 沙盒上执行 Runbook，而只能在混合 Runbook 辅助角色上执行。 

本文中提供的自定义 Azure Policy 定义可帮助你使用以下自动化 REST API 操作来控制这些活动。 具体而言：

* [创建作业](/rest/api/automation/job/create)
* [创建作业计划](/rest/api/automation/jobschedule/create)
* [创建 Webhook](/rest/api/automation/webhook/createorupdate)

此策略基于 `runOn` 属性。 该策略验证属性的值，该值应包含现有混合 Runbook 辅助角色组的名称。 如果值为 null，则将其解释为创建作业、作业计划或 Webhook 的请求是针对 Azure 沙盒发出的，并且会拒绝该请求。

## <a name="permissions-required"></a>所需的权限

你需要是订阅级别的[所有者](../role-based-access-control/built-in-roles.md#owner)角色的成员，对 Azure Policy 资源拥有权限。

## <a name="create-and-assign-the-policy-definition"></a>创建并分配策略定义

此处我们将编写策略规则，然后将其分配到管理组或订阅，并选择性地在订阅中指定资源组。 如果你不熟悉策略语言，请参考[策略定义结构](../governance/policy/concepts/definition-structure.md)了解如何构建策略定义。

1. 使用以下 JSON 代码片段创建名为 AuditAutomationHRWJobExecution.json 的 JSON 文件。

    ```json
    {
        "properties": {
            "displayName": "Enforce job execution on Automation Hybrid Runbook Worker",
            "description": "Enforce job execution on Hybrid Runbook Workers in your Automation account.",
            "mode": "all",
            "parameters": {
                "effectType": {
                    "type": "string",
                    "defaultValue": "Deny",
                    "allowedValues": [
                       "Deny",
                       "Disabled"
                    ],
                    "metadata": {
                        "displayName": "Effect",
                        "description": "Enable or disable execution of the policy"
                    }
                }
            },
            "policyRule": {
          "if": {
            "anyOf": [
              {
                "allOf": [
                  {
                    "field": "type",
                    "equals": "Microsoft.Automation/automationAccounts/jobs"
                  },
                  {
                    "value": "[length(field('Microsoft.Automation/automationAccounts/jobs/runOn'))]",
                    "less": 1
                  }
                ]
              },
              {
                "allOf": [
                  {
                    "field": "type",
                    "equals": "Microsoft.Automation/automationAccounts/webhooks"
                  },
                  {
                    "value": "[length(field('Microsoft.Automation/automationAccounts/webhooks/runOn'))]",
                    "less": 1
                  }
                ]
              },
              {
                "allOf": [
                  {
                    "field": "type",
                    "equals": "Microsoft.Automation/automationAccounts/jobSchedules"
                  },
                  {
                    "value": "[length(field('Microsoft.Automation/automationAccounts/jobSchedules/runOn'))]",
                    "less": 1
                  }
                ]
              }
            ]
          },
          "then": {
            "effect": "[parameters('effectType')]"
          }
        }
      }
    }
    ```

2. 运行以下 Azure PowerShell 或 Azure CLI 命令，以使用 AuditAutomationHRWJobExecution.json 文件创建策略定义。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az policy definition create --name 'audit-enforce-jobs-on-automation-hybrid-runbook-workers' --display-name 'Audit Enforce Jobs on Automation Hybrid Runbook Workers' --description 'This policy enforces job execution on Automation account user Hybrid Runbook Workers.' --rules 'AuditAutomationHRWJobExecution.json' --mode All
    ```

    该命令创建名为 Audit Enforce Jobs on Automation Hybrid Runbook Workers 的策略定义。 有关其他可用的参数的详细信息，请参阅 [az policy definition create](/cli/azure/policy/definition#az_policy_definition_create)。

    在没有位置参数的情况下调用时，`az policy definition create` 默认将策略定义保存在会话上下文的选定订阅中。 若要将定义保存到其他位置，请使用以下参数：

    * subscription - 保存到其他订阅。 订阅 ID 需要 *GUID* 值，订阅名称需要 *字符串* 值。
    * management-group - 保存到管理组。 需要 *字符串* 值。

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzPolicyDefinition -Name 'audit-enforce-jobs-on-automation-hybrid-runbook-workers' -DisplayName 'Audit Enforce Jobs on Automation Hybrid Runbook Workers' -Policy 'AuditAutomationHRWJobExecution.json'
    ```

    该命令创建名为 Audit Enforce Jobs on Automation Hybrid Runbook Workers 的策略定义。 有关可用的其他参数的详细信息，请参阅 [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition)。

    在没有位置参数的情况下调用时，`New-AzPolicyDefinition` 默认将策略定义保存在会话上下文的选定订阅中。 若要将定义保存到其他位置，请使用以下参数：

    * **SubscriptionId** - 保存到其他订阅。 需要 *GUID* 值。
    * **ManagementGroupName** - 保存到管理组。 需要 *字符串* 值。

    ---

3. 创建策略定义后，可运行以下命令创建策略分配：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

   `az policy assignment create` 的 scope 参数适用于管理组、订阅、资源组或单个资源。 该参数使用完整资源路径。 每个容器的 scope 模式如下所示。 将 `{rName}`、`{rgName}`、`{subId}` 和 `{mgName}` 分别替换为你的资源名称、资源组名称、订阅 ID 和管理组名称。 `{rType}` 将替换为资源的 **资源类型**，例如 VM 的 `Microsoft.Compute/virtualMachines`。

   - 资源 - `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - 资源组 - `/subscriptions/{subID}/resourceGroups/{rgName}`
   - 订阅 - `/subscriptions/{subID}`
   - 管理组 - `/providers/Microsoft.Management/managementGroups/{mgName}`

    可以在 PowerShell 中使用以下命令获取 Azure Policy 定义 ID：
    
    ```azurecli
    az policy definition show --name 'Audit Enforce Jobs on Automation Hybrid Runbook Workers'
    ```
    
    创建的策略定义的策略定义 ID 应如以下示例所示：
    
    ```output
    "/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Enforce Jobs on Automation Hybrid Runbook Workers"
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    $rgName = Get-AzResourceGroup -Name 'ContosoRG'
    $Policy = Get-AzPolicyDefinition -Name 'audit-enforce-jobs-on-automation-hybrid-runbook-workers'
    New-AzPolicyAssignment -Name 'audit-enforce-jobs-on-automation-hybrid-runbook-workers' -PolicyDefinition $Policy -Scope $rg.ResourceId
    ```

    将 _ContosoRG_ 替换为所需资源组的名称。

    `New-AzPolicyAssignment` 的 Scope 参数适用于管理组、订阅、资源组或单个资源。 该参数使用完整资源路径，它将返回 `Get-AzResourceGroup` 的 **ResourceId** 属性。 每个容器的 **范围** 模式如下所示。 将 `{rName}`、`{rgName}`、`{subId}` 和 `{mgName}` 分别替换为你的资源名称、资源组名称、订阅 ID 和管理组名称。
    `{rType}` 将替换为资源的 **资源类型**，例如 VM 的 `Microsoft.Compute/virtualMachines`。

    - 资源 - `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
    - 资源组 - `/subscriptions/{subId}/resourceGroups/{rgName}`
    - 订阅 - `/subscriptions/{subId}`
    - 管理组 - `/providers/Microsoft.Management/managementGroups/{mgName}`

    ---

4. 登录到 [Azure 门户](https://portal.azure.com)。
5. 在 Azure 门户中选择“所有服务”，然后搜索并选择“策略”，启动 Azure Policy 服务 。
6. 选择页面左侧的“符合性”。 然后找到创建的策略分配。

   :::image type="content" source="./media/enforce-job-execution-hybrid-worker/azure-policy-dashboard-policy-status.png" alt-text="Azure Policy 仪表板的屏幕截图。":::

在执行某个自动化 REST 操作但不在请求正文中引用混合 Runbook 辅助角色时，将返回 403 响应代码以及类似于以下示例的错误，其中指出该操作尝试在 Azure 沙盒上执行：

```rest
{
  "error": {
    "code": "RequestDisallowedByPolicy",
    "target": "Start_VMS",
    "message": "Resource 'Start_VMS' was disallowed by policy. Policy identifiers: '[{\"policyAssignment\":{\"name\":\"Enforce Jobs on Automation Hybrid Runbook Workers\",\"id\":\"/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/resourceGroups/MAIC-RG/providers/Microsoft.Authorization/policyAssignments/fd5e2cb3842d4eefbc857917\"},\"policyDefinition\":{\"name\":\"Enforce Jobs on Automation Hybrid Runbook Workers\",\"id\":\"/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/providers/Microsoft.Authorization/policyDefinitions/4fdffd35-fd9f-458e-9779-94fe33401bfc\"}}]'.",
    "additionalInfo": [
      {
        "type": "PolicyViolation",
        "info": {
          "policyDefinitionDisplayName": "Enforce Jobs on Automation Hybrid Runbook Workers",
          "evaluationDetails": {
            "evaluatedExpressions": [
              {
                "result": "True",
                "expressionKind": "Field",
                "expression": "type",
                "path": "type",
                "expressionValue": "Microsoft.Automation/automationAccounts/jobs",
                "targetValue": "Microsoft.Automation/automationAccounts/jobs",
                "operator": "Equals"
              },
              {
                "result": "True",
                "expressionKind": "Value",
                "expression": "[length(field('Microsoft.Automation/automationAccounts/jobs/runOn'))]",
                "expressionValue": 0,
                "targetValue": 1,
                "operator": "Less"
              }
            ]
          },
          "policyDefinitionId": "/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/providers/Microsoft.Authorization/policyDefinitions/4fdffd35-fd9f-458e-9779-94fe33401bfc",
          "policyDefinitionName": "4fdffd35-fd9f-458e-9779-94fe33401bfc",
          "policyDefinitionEffect": "Deny",
          "policyAssignmentId": "/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/resourceGroups/MAIC-RG/providers/Microsoft.Authorization/policyAssignments/fd5e2cb3842d4eefbc857917",
          "policyAssignmentName": "fd5e2cb3842d4eefbc857917",
          "policyAssignmentDisplayName": "Enforce Jobs on Automation Hybrid Runbook Workers",
          "policyAssignmentScope": "/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/resourceGroups/MAIC-RG",
          "policyAssignmentParameters": {}
        }
      }
    ]
  }
}
```

尝试的操作还会记录在自动化帐户的活动日志中，如以下示例所示。

:::image type="content" source="./media/enforce-job-execution-hybrid-worker/failed-job-activity-log-example.png" alt-text="失败的作业执行的活动日志示例。":::

## <a name="next-steps"></a>后续步骤

若要使用 runbook，请参阅[在 Azure 自动化中管理 runbook](manage-runbooks.md)。