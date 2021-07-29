---
title: 排查 Azure 角色分配条件的问题（预览版）
description: 排查 Azure 角色分配条件的问题（预览版）
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/06/2021
ms.author: rolyon
ms.openlocfilehash: eddebb6742a50ce729d436fc19ce4ff32491f75f
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2021
ms.locfileid: "109489541"
---
# <a name="troubleshoot-azure-role-assignment-conditions-preview"></a>排查 Azure 角色分配条件的问题（预览版）

> [!IMPORTANT]
> Azure ABAC 和 Azure 角色分配条件目前为预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="symptom---condition-is-not-enforced"></a>症状 - 条件未强制实施

**原因 1**

安全主体在相同或更高的范围具有一个或多个角色分配。

**解决方案 1**

确保安全主体不存在多个对同一数据操作授予访问权限、导致不强制实施条件的角色分配（带有或不带有条件）。 有关评估逻辑的信息，请参阅 [Azure RBAC 如何确定用户是否有权访问某个资源](overview.md#how-azure-rbac-determines-if-a-user-has-access-to-a-resource)。

**原因 2**

你的角色分配包含多个授予权限的操作，并且你的条件不是以所有操作为目标。 例如，如果你有 `/blobs/write` 或 `/blobs/add/action` 数据操作，则可以创建 Blob。 如果你的角色分配包含这两个数据操作，并且你只在条件中以其中一个操作为目标，则该角色分配将授权创建 Blob，并绕过该条件。

**解决方案 2**

如果你的角色分配包含多个授予权限的操作，请确保以所有相关操作为目标。

**原因 3**

在角色分配中添加条件时，最多可能需要 5 分钟时间，该条件才会强制实施。 添加条件时，会向资源提供程序（例如 Microsoft.Storage）通知发生了更新。 资源提供程序将立即对其本地缓存进行更新，以确保使用最新的角色分配。 此过程将在 1 到 2 分钟内完成，但最长可能需要 5 分钟。

**解决方法 3**

等待 5 分钟，然后再次测试条件。

## <a name="symptom---condition-is-not-valid-error-when-adding-a-condition"></a>症状 - 添加条件时出现“条件无效”错误

尝试添加带有条件的角色分配时，出现如下所示的错误：

`The given role assignment condition is invalid.`

**原因**

条件的格式不正确。 

**解决方案**

解决任何[条件格式或语法](conditions-format.md)问题。 或者，使用 [Azure 门户中的视觉编辑器](conditions-role-assignments-portal.md)添加条件。

## <a name="symptom---resource-attribute-is-not-valid-error-when-adding-a-condition-using-azure-powershell"></a>症状 - 使用 Azure PowerShell 添加条件时出现“资源特性无效”错误

尝试使用 Azure PowerShell 添加带有条件的角色分配时，出现如下所示的错误：

```
New-AzRoleAssignment : Resource attribute
Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$> is not valid.
```

**原因**

如果条件包含美元符号 ($)，则必须使用反撇号 (\`) 作为其前缀。

**解决方案**

在每个美元符号前面添加一个反撇号 (\`)。 下面显示了一个示例。 有关 PowerShell 中的引号规则的详细信息，请参阅[关于引号规则](/powershell/module/microsoft.powershell.core/about/about_quoting_rules)。

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Cascade'))"
```

## <a name="symptom---resource-attribute-is-not-valid-error-when-adding-a-condition-using-azure-cli"></a>症状 - 使用 Azure CLI 添加条件时出现“资源特性无效”错误

尝试使用 Azure CLI 添加带有条件的角色分配时，出现如下所示的错误：

```
Resource attribute Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$> is not valid.
```

**原因**

如果条件包含美元符号 ($)，则必须使用反斜杠 (\\) 作为其前缀。

**解决方案**

在每个美元符号前面添加一个反斜杠 (\\)。 下面显示了一个示例。 有关 Bash 中的引号规则的详细信息，请参阅[双引号](https://www.gnu.org/software/bash/manual/html_node/Double-Quotes.html)。

```azurecli
condition="((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<\$key_case_sensitive\$>] StringEquals 'Cascade'))"
```

## <a name="symptom---error-when-assigning-a-condition-string-to-a-variable-in-bash"></a>症状 - 在 Bash 中将条件字符串分配到变量时出错

尝试在 Bash 中将条件字符串分配到变量时出现 `bash: !: event not found` 消息。

**原因**

在 Bash 中，如果启用了历史记录扩展，则可能会由于感叹号 (!) 的原因而出现消息 `bash: !: event not found`。

**解决方案**

使用命令 `set +H` 禁用历史记录扩展。 若要重新启用历史记录扩展，请使用 `set -H`。

## <a name="symptom---unrecognized-arguments-error-when-adding-a-condition-using-azure-cli"></a>症状 - 使用 Azure CLI 添加条件时出现“无法识别参数”错误

尝试使用 Azure CLI 添加带有条件的角色分配时，出现如下所示的错误：

`az: error: unrecognized arguments: --description {description} --condition {condition} --condition-version 2.0`

**原因**

你可能正在使用早期版本的 Azure CLI，该版本不支持角色分配条件参数。

**解决方案**

更新到最新的 Azure CLI 版本（2.18 或更高版本）。 有关详细信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="symptom---condition-not-recognized-in-visual-editor"></a>症状 - 视觉编辑器中无法识别条件

使用代码编辑器后，切换到视觉编辑器时出现如下所示的消息：

`The current expression cannot be recognized. Switch to the code editor to edit the expression or delete the expression and add a new one.`

**原因**

更新了该条件，使得视觉编辑器无法对其进行分析。

**解决方案**

解决任何[条件格式或语法](conditions-format.md)问题。 或者，可以删除该条件，然后重试。

## <a name="symptom---error-when-copying-and-pasting-a-condition"></a>症状 - 复制并粘贴条件时出错

**原因**

如果从某个文档复制条件，复制的内容可能会包含特殊字符，因而导致出错。 某些编辑器（例如 Microsoft Word）在设置文本格式时，会添加一些隐藏的控制字符。

**解决方案**

如果你确定条件正确，请删除所有空格和回车符，然后重新添加相关的空格。

## <a name="next-steps"></a>后续步骤

- [Azure 角色分配条件格式和语法（预览版）](conditions-format.md)
- [有关 Azure 角色分配条件的常见问题解答（预览版）](conditions-faq.md)
- [示例 Azure 角色分配条件（预览版）](../storage/common/storage-auth-abac-examples.md)
