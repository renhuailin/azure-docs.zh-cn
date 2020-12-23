---
title: StorageBlobSelector UI 元素
description: 描述 Azure 门户的 StorageBlobSelector UI 元素。
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 1085b70df67a3f16a7f7f8c5ae85c9ab271b62ac
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754364"
---
# <a name="microsoftstoragestorageblobselector-ui-element"></a>StorageBlobSelector UI 元素

用于从 Azure 存储帐户选择 blob 的控件。

## <a name="ui-sample"></a>UI 示例

向用户显示用于浏览可用存储 blob 的选项。

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-browse.png" alt-text="StorageBlobSelector-浏览&quot;:::

选择 &quot; **浏览** " 后，用户可以选择存储帐户。

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-select.png" alt-text="StorageBlobSelector-浏览&quot;:::

选择 &quot; **浏览** ":::

用户会看到存储帐户中的容器，并且可以选择其中的容器。

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-containers.png" alt-text="StorageBlobSelector-浏览&quot;:::

选择 &quot; **浏览** ":::

用户可以从容器中选择文件。

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-file.png" alt-text="StorageBlobSelector-浏览&quot;:::

选择 &quot; **浏览** ":::

控件将更新以显示选定的文件名。

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-result.png" alt-text="StorageBlobSelector-浏览&quot;:::

选择 &quot; **浏览** ":::

## <a name="schema"></a>架构

```json
{
  "name": "storageBlobSelection",
  "type": "Microsoft.Storage.StorageBlobSelector",
  "visible": true,
  "toolTip": "Select storage blob",
  "label": "Package (.zip, .cspkg)",
  "options": {
    "text": "Select Package"
  },
  "constraints": {
    "allowedFileExtensions": [ "zip", "cspkg" ]
  }
}
```

## <a name="sample-output"></a>示例输出

```json
{
  "blobName": "test.zip",
  "sasUri": "https://azstorageaccnt1.blob.core.windows.net/container1/test.zip?sp=r&se=2020-10-10T07:46:22Z&sv=2019-12-12&sr=b&sig=X4EL8ZsRmiP1TVxkVfTcGyMj2sHg1zCbFBXsDmnNOyg%3D"
}

```

## <a name="remarks"></a>备注

**AllowedFileExtensions** 属性指定允许的文件类型。

## <a name="next-steps"></a>后续步骤

* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
