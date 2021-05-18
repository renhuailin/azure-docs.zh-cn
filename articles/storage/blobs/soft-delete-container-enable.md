---
title: 启用和管理容器的软删除（预览版）
titleSuffix: Azure Storage
description: 启用容器软删除（预览版），以便在错误地修改或删除数据时更轻松地恢复数据。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/05/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 2097c1743e07b5563bc75d3d1cce48aa11b98e5f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216337"
---
# <a name="enable-and-manage-soft-delete-for-containers-preview"></a>启用和管理容器的软删除（预览版）

容器软删除（预览版）可防止意外或错误地修改或删除数据。 为存储帐户启用容器软删除后，容器及其内容被删除后可以在指定的保持期内恢复。

如果你的数据有可能被应用程序或其他存储帐户用户意外修改或删除，Microsoft 建议启用容器软删除。 本文介绍如何启用容器的软删除。 有关容器软删除的更多详细信息（包括如何注册预览版），请参阅[容器的软删除（预览版）](soft-delete-container-overview.md)。

对于端到端的数据保护，Microsoft 建议同时启用 blob 的软删除和 Blob 版本控制。 若要了解如何同时启用 blob 的软删除，请参阅[启用和管理 blob 的软删除](soft-delete-blob-enable.md)。 若要了解如何启用 blob 版本控制，请参阅 [Blob 版本控制](versioning-overview.md)。

> [!IMPORTANT]
>
> 容器软删除目前处于预览阶段。 有关 beta 版本、预览版或尚未正式发布的版本的 Azure 功能所适用的法律条款，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="enable-container-soft-delete"></a>启用容器软删除

可通过使用 Azure 门户或 Azure 资源管理器模板，随时为存储帐户启用或禁用容器软删除。

# <a name="portal"></a>[Portal](#tab/azure-portal)

若要使用 Azure 门户为存储帐户启用容器软删除，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中导航到存储帐户。
1. 在“Blob 服务”下，找到“数据保护”设置 。
1. 将“容器软删除”属性设置为“已启用”。
1. 在“保留策略”下，指定软删除的容器可由 Azure 存储保留多长时间。
1. 保存更改。

:::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-configure.png" alt-text="显示如何在 Azure 门户中启用容器软删除的屏幕截图":::

# <a name="template"></a>[模板](#tab/template)

若要使用 Azure 资源管理器模板启用容器软删除，请创建一个设置了 containerDeleteRetentionPolicy 属性的模板。 以下步骤说明了如何在 Azure 门户中创建模板。

1. 在 Azure 门户中，选择“创建资源”。
1. 在“搜索市场”中键入“模板部署”，然后按 **ENTER**。 
1. 依次选择“模板部署”、“创建”、“在编辑器中生成自己的模板”  。
1. 在模板编辑器中粘贴以下 JSON。 将 `<account-name>` 占位符替换为存储帐户的名称。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "variables": {},
      "resources": [
          {
              "type": "Microsoft.Storage/storageAccounts/blobServices",
              "apiVersion": "2019-06-01",
              "name": "<account-name>/default",
              "properties": {
                  "containerDeleteRetentionPolicy": {
                      "enabled": true,
                      "days": 7
                  }
              }
          }
      ]
    }
    ```

---

1. 指定保持期。 默认值为 7。
1. 保存模板。
1. 指定帐户的资源组，然后选择“查看 + 创建”按钮来部署模板并启用容器软删除。

## <a name="view-soft-deleted-containers"></a>查看软删除的容器

启用软删除后，可在 Azure 门户中查看软删除的容器。 软删除的容器在指定的保持期内可见。 保持期到期后，软删除的容器将被永久删除，不再可见。

若要在 Azure 门户中查看软删除的容器，请执行以下步骤：

1. 在 Azure 门户中导航到你的存储帐户，查看你的容器列表。
1. 切换“显示删除的容器”开关，以将已删除的容器包含在列表中。

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-list.png" alt-text="显示如何在 Azure 门户中查看软删除的容器的屏幕截图":::

## <a name="restore-a-soft-deleted-container"></a>还原软删除的容器

可在保持期内还原软删除的容器及其内容。 若要在 Azure 门户中还原软删除的容器，请执行以下步骤：

1. 在 Azure 门户中导航到你的存储帐户，查看你的容器列表。
1. 显示要还原的容器的上下文菜单，然后从菜单中选择“撤消删除”。

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-restore.png" alt-text="显示如何在 Azure 门户中还原软删除的容器的屏幕截图":::

## <a name="next-steps"></a>后续步骤

- [容器软删除（预览版）](soft-delete-container-overview.md)
- [blob 的软删除](soft-delete-blob-overview.md)
- [Blob 版本控制](versioning-overview.md)
