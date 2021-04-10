---
author: baanders
description: Azure 数字孪生的 include 文件 - 命令行教程的模型说明
ms.topic: include
ms.date: 3/5/2021
ms.author: baanders
ms.openlocfilehash: a94b9304ecd6c6630f6ad45652e76d2879bbc1b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463712"
---
1. 更新版本号，以指出正在提供此模型的更新版本。 为此，请将 `@id` 末尾的 1 更改为 2 。 大于当前版本号的任何数字也有效。
1. 编辑属性。 将 `Humidity` 属性的名称更改为 HumidityLevel（或根据需要将其更改为其他名称。 如果使用的名称不是 HumidityLevel，请记住自己使用的名称，并在整个教程中继续使用该名称，而不使用 HumidityLevel） 。
1. 添加属性。 在结束于第 15 行的 `HumidityLevel` 属性下方，粘贴以下代码以将 `RoomName` 属性添加到 room 中：

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="16-20":::

1. 添加关系。 在刚刚添加的 `RoomName` 属性下方，粘贴以下代码，使此类孪生能够与其他孪生形成包含关系：

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="21-24":::

完成后，更新后的模型应如下所示：

:::code language="json" source="~/digital-twins-docs-samples/models/Room.json":::

在继续操作之前，请务必保存该文件。