---
title: 在 Azure 门户中创建帐户
description: 了解如何在 Azure 门户中创建 Azure Batch 帐户，以便在云中运行大规模并行工作负荷
ms.topic: how-to
ms.date: 01/26/2021
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 943fd145dbd4964e0d43d91f726ea5a79fda59de
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895982"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>使用 Azure 门户创建 Batch 帐户

本主题说明如何在[Azure 门户](https://portal.azure.com)中创建[Azure Batch 帐户](accounts.md)，并选择适合计算方案的帐户属性。 你还将了解在何处查找重要的帐户属性，例如访问密钥和帐户 URL。

有关 Batch 帐户和方案的背景知识，请参阅 [Batch 服务工作流和资源](batch-service-workflow-features.md)。

## <a name="create-a-batch-account"></a>创建批处理帐户

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 从主页中选择 " **创建资源**"。

1. 在搜索框中，输入 " **Batch 服务**"。 从结果中选择 " **Batch 服务** "，然后选择 " **创建**"。

1. 输入以下详细信息。

    :::image type="content" source="media/batch-account-create-portal/batch-account-portal.png" alt-text="新 Batch 帐户屏幕的屏幕截图。":::

    a. **订阅**：要在其中创建 Batch 帐户的订阅。 如果只有一个订阅，则默认选择此项。

    b. **资源组**：为新 Batch 帐户选择现有的资源组，或选择创建一个新组。

    c. **帐户名称**：所选名称必须在创建帐户的 Azure 区域中唯一（参见下面的“位置”）。 帐户名只能包含小写字符或数字，且长度必须为 3-24 个字符。

    d. **位置**：要在其中创建 Batch 帐户的 Azure 区域。 只有订阅和资源组支持的区域显示为选项。

    e. **存储帐户**：与批处理帐户关联的可选 [Azure 存储帐户](accounts.md#azure-storage-accounts) 。 你可以选择现有的存储帐户，也可以创建一个新的存储帐户。 为获得最佳性能，建议使用常规用途 v2 存储帐户。

    :::image type="content" source="media/batch-account-create-portal/storage_account.png" alt-text="创建存储帐户时选项的屏幕截图。":::

1. 如果需要，请选择 " **高级** " 以指定 **标识类型**、 **公用网络访问** 或 **池分配模式**。 在大多数情况下，默认选项是正确的。

1. 依次选择 " **查看**" 和 "创建"，然后选择 " **创建** " 以创建帐户。

## <a name="view-batch-account-properties"></a>查看 Batch 帐户属性

创建帐户后，选择该帐户即可访问其设置和属性。 可以使用左侧菜单访问所有帐户设置和属性。

> [!NOTE]
> Batch 帐户名是它的 ID，无法更改。 如果需要更改 Batch 帐户名，则需要删除帐户，然后新建一个具有所需名称的帐户。

:::image type="content" source="media/batch-account-create-portal/batch_blade.png" alt-text="Azure 门户中批处理帐户页的屏幕截图。":::

通过 [Batch API](batch-apis-tools.md#azure-accounts-for-batch-development) 开发应用程序时，需要帐户 URL 和密钥才能访问 Batch 资源。  (Batch 还支持 Azure Active Directory 身份验证。 ) 若要查看 Batch 帐户访问权限信息，请选择 " **密钥**"。

:::image type="content" source="media/batch-account-create-portal/batch-account-keys.png" alt-text="Azure 门户中批处理帐户密钥的屏幕截图。":::

若要查看与 Batch 帐户关联的存储帐户的名称和密钥，请选择“存储帐户”。

若要查看应用于 Batch 帐户的 [资源配额](batch-quota-limit.md) ，请选择 " **配额**"。

## <a name="additional-configuration-for-user-subscription-mode"></a>用户订阅模式的其他配置

如果选择在用户订阅模式下创建 Batch 帐户，请在创建帐户前执行以下附加步骤。

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>允许 Azure Batch 访问订阅（一次性操作）

在用户订阅模式下创建第一个 Batch 帐户时，需将订阅注册到 Batch 中。 （如果已执行过此操作，请跳至下一部分。）

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 选择“所有服务” > “订阅”，然后选择要用于 Batch 帐户的订阅。

1. 在“订阅”页中选择“资源提供程序”，然后搜索“Microsoft.Batch”。 查看 **Microsoft.Batch** 资源提供程序是否已在订阅中注册。 如果不是，请选择靠近屏幕顶部的 " **注册** " 链接。

    :::image type="content" source="media/batch-account-create-portal/register_provider.png" alt-text="显示 Microsoft.Batch 资源提供程序的屏幕截图。":::

1. 返回到 "**订阅**" 页，然后选择 "**访问控制 (IAM)**  >  **角色分配**"  >  **添加**  >  **添加角色分配**"。

    :::image type="content" source="media/batch-account-create-portal/subscription_iam.png" alt-text="订阅的 &quot;角色分配&quot; 页的屏幕截图。":::

1. 在“添加角色分配”页上，选择“参与者”或“所有者”角色，然后搜索“Batch API” 。 搜索 **Microsoft Azure Batch** 或 **MICROSOFTAZUREBATCH** 以查找 API。  (**ddbf3205-c6bd-46ae-8127-60eb93363864** 是批处理 API 的应用程序 ID。 ) 

1. 找到此 Batch API 后，将其选中，然后选择“保存”。

### <a name="create-a-key-vault"></a>创建密钥保管库

在用户订阅模式下，需要 [Azure 密钥保管库](../key-vault/general/overview.md)。 密钥保管库必须与要创建的 Batch 帐户位于同一订阅和区域中。

1. 在 [Azure 门户](https://portal.azure.com)的 "主页" 中，选择 " **创建资源**"。
1. 在“搜索”框中输入“Key Vault”。 从结果中选择 " **Key Vault** "，然后选择 " **创建**"。
1. 在 " **创建密钥保管库** " 页上，输入 Key Vault 的名称，并在要用于 Batch 帐户的同一区域中创建新的资源组。 让其余设置保留默认值，然后选择“创建”。

在用户订阅模式下创建 Batch 帐户时，将 **用户订阅** 指定为池分配模式，选择创建的 Key Vault，并选中复选框以授予 Azure Batch 对 Key Vault 的访问权限。

如果希望手动授予对 Key Vault 的访问权限，请访问 Key Vault 的 " **访问策略** " 部分，然后选择 " **添加访问策略**"。 选择 " **选择主体** " 旁边的链接，然后搜索 " **Microsoft Azure Batch** (应用程序 ID **ddbf3205-c6bd-46ae-8127-60eb93363864**) "。 选择该主体，然后使用下拉菜单配置 **机密权限** 。 必须至少授予 Azure Batch **Get**、**List**、**Set** 和 **Delete** 权限。

:::image type="content" source="media/batch-account-create-portal/secret-permissions.png" alt-text="Azure Batch 的机密权限选项的屏幕截图":::

选择 **"添加**"，然后确保为 "链接 **Key Vault** " 资源选中 "用于 **部署的 azure 虚拟机**" 和 "**用于模板部署的 azure 资源管理器**" 复选框。 选择 " **保存** " 以提交更改。

:::image type="content" source="media/batch-account-create-portal/key-vault-access-policy.png" alt-text="访问策略屏幕的屏幕截图。":::

### <a name="configure-subscription-quotas"></a>配置订阅配额

对于用户订阅批处理帐户，必须手动设置 [核心配额](batch-quota-limit.md) 。 标准 Batch 核心配额不适用于 "用户订阅" 模式下的帐户，并将使用和强制执行在区域计算核心、每系列计算核心和其他资源的 [订阅中的配额](../azure-resource-manager/management/azure-subscription-service-limits.md) 。

1. 在 [Azure 门户](https://portal.azure.com)中选择用户订阅模式 Batch 帐户，以便显示其设置和属性。
1. 在左侧菜单中选择“配额”，以便查看和配置与 Batch 帐户相关联的核心配额。

## <a name="other-batch-account-management-options"></a>其他 Batch 帐户管理选项

除了使用 Azure 门户外，还可使用以下工具创建和管理 Batch 帐户：

- [Batch PowerShell cmdlet](batch-powershell-cmdlets-get-started.md)
- [Azure CLI](batch-cli-get-started.md)
- [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>后续步骤

- 了解 [Batch 服务工作流和主要资源](batch-service-workflow-features.md)（如池、节点、作业和任务）。
- 了解使用[批处理 .NET 客户端库](quick-run-dotnet.md)或 [Python](quick-run-python.md) 开发支持批处理的应用程序的基本概念。 这些快速入门指南介绍了使用 Batch 服务在多个计算节点上执行工作负荷的示例应用程序，并使用 Azure 存储进行工作负荷文件暂存和检索。