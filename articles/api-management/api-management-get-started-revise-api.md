---
title: 教程 - 在 API Management 中使用修订安全地进行非中断性 API 变更
titleSuffix: Azure API Management
description: 遵循本教程中的步骤了解如何在 API 管理中使用修订进行非重大更改。
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.custom: mvc, devx-track-azurecli
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: danlep
ms.openlocfilehash: 34ffc28e6c8e109252b68be269c1918beabfd5af
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676592"
---
# <a name="tutorial-use-revisions-to-make-non-breaking-api-changes-safely"></a>教程：使用修订安全地进行非中断性 API 变更
当 API 准备就绪并即将供开发人员使用时，你最终需要对该 API 进行更改，同时避免干扰 API 的调用方。 另一种有效的做法是让开发人员知道所做的更改。 

在 Azure API Management 中，使用修订进行非中断性 API 变更，以便可以安全地对变更进行建模和测试。 准备就绪后，可以将修订设为当前版本并替换当前的 API。 

有关背景知识，请参阅[版本和修订](https://azure.microsoft.com/blog/versions-revisions/)和[使用 Azure API Management 进行 API 版本控制](https://azure.microsoft.com/blog/api-versioning-with-azure-api-management/)。

在本教程中，你将了解：

> [!div class="checklist"]
> * 添加新的修订版
> * 对修订版进行非重大更改
> * 将修订版设为当前版本并添加更改日志项目
> * 浏览开发人员门户以查看更改与更改日志

:::image type="content" source="media/api-management-getstarted-revise-api/azure-portal.png" alt-text="Azure 门户中的 API 修订":::

## <a name="prerequisites"></a>先决条件

+ 了解 [Azure API 管理术语](api-management-terminology.md)。
+ 完成以下快速入门：[创建一个 Azure API 管理实例](get-started-create-service-instance.md)。
+ 此外，请完成以下教程：[导入和发布第一个 API](import-and-publish.md)。

## <a name="add-a-new-revision"></a>添加新的修订版

1. 登录到 [Azure 门户](https://portal.azure.com)，并导航到 API Management 实例。
1. 选择“API”。
2. 从 API 列表中选择“演示会议 API”（或想要添加修订的其他 API）。
3. 选择“修订”选项卡。
4. 选择“+ 添加修订”。

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-01-add-new-revision.png" alt-text="添加 API 修订版本":::

    > [!TIP]
    > 也可以在 API 上的上下文菜单 (…) 中选择“添加修订” 。

5. 提供新修订版的说明，帮助记住其用途。
6. 选择“创建”，
7. 现已创建新的修订版。

    > [!NOTE]
    > 原始 API 保留在“修订版 1”中。 这是在将其他修订版设为当前版本之前，用户继续调用的修订版。

## <a name="make-non-breaking-changes-to-your-revision"></a>对修订版进行非重大更改

1. 从 API 列表中选择“演示会议 API”。
1. 选择靠近屏幕顶部的“设计”选项卡。
1. 请注意，**修订版选择器**（位于“设计”选项卡正上方）显示“修订版 2”为当前所选项。

    > [!TIP]
    > 使用修订版选择器可在要使用的修订版之间切换。
1. 选择“+ 添加操作”。
1. 将新操作设置为“POST”，将操作的“名称”、“显示名称”和 URL 设置为“test”。
1. **保存** 新操作。

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-02-make-changes.png" alt-text="修改修订版本":::
1. 现在，已对“修订版 2”进行了更改。 使用靠近页面顶部的“修订版选择器”切换回到“修订版 1” 。
1. 请注意，新操作未出现在“修订版 1”中。 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>将修订版设为当前版本并添加更改日志项目

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. 在靠近页面顶部的菜单中选择“修订”选项卡。
1. 打开“修订版 2”对应的上下文菜单 (**...**)。
1. 选择“设为当前版本”。
1. 如果想要发布有关此变更的说明，请选中“发布有关此 API 的公共变更日志”复选框。 提供可让开发人员看到的更改说明，例如，“测试修订。添加了新的“测试”操作。
1. **修订版 2** 现在是当前版本。

    :::image type="content" source="media/api-management-getstarted-revise-api/revisions-menu.png" alt-text="“修订”窗口中的“修订”菜单":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

开始使用 Azure CLI：

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

使用此过程创建和更新版本。

1. 运行 [az apim api list](/cli/azure/apim/api#az_apim_api_list) 命令以查看 API ID：

   ```azurecli
   az apim api list --resource-group apim-hello-word-resource-group \
       --service-name apim-hello-world --output table
   ```

   要在下一个命令中使用的 API ID 是 `Name` 值。 API 版本位于 `ApiRevision` 列中。

1. 若要创建版本和发行说明，请运行 [az apim api release create](/cli/azure/apim/api/release#az_apim_api_release_create) 命令：

   ```azurecli
   az apim api release create --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --api-revision 2 --service-name apim-hello-world \
       --notes 'Testing revisions. Added new "test" operation.'
   ```

   你发布的修订版即为当前修订版。

1. 若要查看版本，请使用 [az apim api release list](/cli/azure/apim/api/release#az_apim_api_release_list) 命令：

   ```azurecli
   az apim api release list --resource-group apim-hello-word-resource-group \
       --api-id echo-api --service-name apim-hello-world --output table
   ```

   指定的注释显示在更改日志中。 可以在前一个命令的输出中看到这些注释。

1. 创建版本时，可选用 `--notes` 参数。 稍后可以通过使用 [az apim api release update](/cli/azure/apim/api/release#az_apim_api_release_update) 命令添加或更改注释：

   ```azurecli
   az apim api release update --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --release-id 00000000000000000000000000000000 \
       --service-name apim-hello-world --notes "Revised notes."
   ```

   使用 `Name` 列中的值作为版本 ID。

可以通过运行 [az apim api release delete ](/cli/azure/apim/api/release#az_apim_api_release_delete) 命令删除任何版本：

```azurecli
az apim api release delete --resource-group apim-hello-word-resource-group \
    --api-id demo-conference-api --release-id 00000000000000000000000000000000 
    --service-name apim-hello-world
```

---

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>浏览开发人员门户以查看更改与更改日志

如果已尝试使用[开发人员门户](api-management-howto-developer-portal-customize.md)，则可以查看 API 变更和这里的变更日志。

1. 在 Azure 门户中，选择“API”。
1. 在顶部菜单中选择“开发人员门户”。
1. 在开发人员门户中，选择“API”，然后选择“演示会议 API” 。
1. 请注意，新的“测试”操作现已显示。
1. 选择 API 名称旁边的“Changelog”。
1. 请注意，更改日志项目已显示在此列表中。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 添加新的修订版
> * 对修订版进行非重大更改
> * 将修订版设为当前版本并添加更改日志项目
> * 浏览开发人员门户以查看更改与更改日志

转到下一教程：

> [!div class="nextstepaction"]
> [发布 API 的多个版本](api-management-get-started-publish-versions.md)
