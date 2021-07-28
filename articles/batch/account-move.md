---
title: 将 Azure Batch 帐户移到另一个区域
description: 了解如何将 Azure Batch 帐户移到另一个区域。
ms.topic: how-to
ms.date: 05/05/2021
ms.custom: subject-moving-resources
ms.openlocfilehash: 9c0e45b62f6dd6152ab80beaa751925702b1fa37
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108776484"
---
# <a name="move-an-azure-batch-account-to-another-region"></a>将 Azure Batch 帐户移到另一个区域

在某些情况下，将现有 [Batch 帐户](accounts.md)从一个区域移到另一个区域可能会很有帮助。 例如在灾难恢复计划中，你可能想将帐户移到另一个区域。

Azure Batch 帐户无法直接从一个区域移到另一个区域。 但是，可以使用 Azure 资源管理器模板来导出 Batch 帐户的现有配置。 然后，可将资源暂存在另一区域，方法是：将 Batch 帐户导出到模板，根据目标区域的情况修改参数，然后将模板部署到新区域。 接下来，可以在帐户中重新创建作业和其他功能。

 有关资源管理器和模板的详细信息，请参阅[快速入门：使用 Azure 门户创建和部署 Azure 资源管理器模板](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)。

本主题介绍如何使用 Azure 门户在不同的区域之间移动 Batch 帐户。

## <a name="prerequisites"></a>先决条件

- 按照[将 Azure 存储帐户移到另一区域](../storage/common/storage-account-move.md)中的步骤，将与 Batch 帐户关联的存储帐户移到新的目标区域。 如果需要，可将存储帐户留在原始区域；但我们建议移动它，因为将存储帐户与 Batch 帐户放在同一区域通常可以提高性能。 以下说明假设你已迁移自己的存储帐户。
- 确保你的 Batch 帐户使用的服务和功能在目标区域中受支持。

## <a name="prepare"></a>准备

若要开始，需要导出并修改一个资源管理器模板。

### <a name="export-a-template"></a>导出模板

首先，导出包含 Batch 帐户设置和信息的模板。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 选择“所有资源”，然后选择你的 Batch 帐户。

3. 选择“自动化” > “导出模板” 。

4. 选择“导出模板”边栏选项卡中的“下载”。 

5. 找到从门户下载的 .zip 文件，并将该文件解压缩到所选的文件夹。

   此 zip 文件包含构成模板的 .json 文件以及用于部署模板的脚本。

### <a name="modify-the-template"></a>修改模板

接下来，加载并修改该模板，以便可以在目标区域创建新的 Batch 帐户。

1. 在 Azure 门户中，选择“创建资源”。

1. 在“搜索市场”中键入“模板部署”，然后按 **ENTER**。 

1. 选择“模板部署(使用自定义模板进行部署)”。

1. 选择“创建” 。

1. 选择“在编辑器中生成自己的模板”。

1. 选择“加载文件”，然后选择在上一部分下载的“template.json”文件 。

1. 在上传的 template.json 文件中，通过输入 Batch 帐户名称的新 defaultValue 来为目标 Batch 帐户命名 。 此示例将 Batch 帐户名称的 defaultValue 设置为 `mytargetaccount`。 将 defaultValue 中的字符串替换为 `mytargetstorageaccount` 的资源 ID。

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "batchAccounts_mysourceaccount_name": {
                "defaultValue": "mytargetaccount",
                "type": "String"
            }
        },
   ```

1. 接下来，使用迁移的存储帐户的资源 ID 更新存储帐户的 defaultValue。 若要获取此值，请在 Azure 门户中导航到存储帐户，选择靠近屏幕顶部的“JSON 视图”，然后复制“资源 ID”下显示的值 。 此示例使用资源组 `mytargetresourcegroup` 中名为 `mytargetstorageaccount` 的存储帐户的资源 ID。

   ```json
           "storageAccounts_mysourcestorageaccount_externalid": {
            "defaultValue": "/subscriptions/{subscriptionID}/resourceGroups/mytargetresourcegroup/providers/Microsoft.Storage/storageAccounts/mytargetstorageaccount",
            "type": "String"
        }
    },
   ```

1. 最后，编辑 location 属性以使用你的目标区域。 此示例将目标区域设置为 `centralus`。

```json
    {
        "resources": [
            {
                "type": "Microsoft.Batch/batchAccounts",
                "apiVersion": "2021-01-01",
                "name": "[parameters('batchAccounts_mysourceaccount_name')]",
                "location": "centralus",  
```

若要获取区域位置代码，请参阅 [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。  区域代码是不带空格的区域名称。 例如，美国中部 = **centralus** 。

## <a name="move"></a>移动

部署该模板以在目标区域中创建新的 Batch 帐户。

1. 进行修改后，选择“template.json”文件下面的“保存” 。

1. 输入或选择属性值：
   - **订阅**：选择 Azure 订阅。
   - 资源组：选择你在移动关联的存储帐户时创建的资源组。
   - 区域：选择要将帐户移到的 Azure 区域。

1. 依次选择“查看并创建”、“创建” 。

### <a name="configure-the-new-batch-account"></a>配置新的 Batch 帐户

某些功能不会导出到模板中，因此必须在新的 Batch 帐户中重新创建这些功能。 其中包括：

- 作业
- 作业计划
- 证书
- 应用程序包

请务必在新帐户中按需配置这些功能。 可以参考源 Batch 帐户来了解如何配置这些功能。

## <a name="discard-or-clean-up"></a>丢弃或清理

确认新 Batch 帐户在新区域中正常工作并且已还原所需的功能后，可以删除源 Batch 帐户。

若要使用 Azure 门户删除 Batch 帐户，请执行以下操作：

1. 在 Azure 门户中，展开左侧的菜单以打开服务菜单，然后选择“Batch 帐户”。

2. 找到要删除的 Batch 帐户，然后右键单击列表右侧的“更多”按钮 (...) 。 请确保要删除的帐户是原始源 Batch 帐户，而不是你新建的帐户。

3. 选择“删除”并确认。

## <a name="next-steps"></a>后续步骤

- 详细了解如何[将资源移到新资源组或订阅](../azure-resource-manager/management/move-resource-group-and-subscription.md)。
- 了解如何[将 Azure VM 移到另一区域](../site-recovery/azure-to-azure-tutorial-migrate.md)。
