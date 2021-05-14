---
author: baanders
description: include 文件用于将模型上传到 Azure 数字孪生实例
ms.service: digital-twins
ms.topic: include
ms.date: 4/1/2021
ms.author: baanders
ms.openlocfilehash: 3a9ec169f91ebe048914c3ef2163e4fde7485389
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109783659"
---
该模型下所示：
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

若要 **将此模型上传到孪生实例**，请运行以下 Azure CLI 命令，该命令会将上述模型上传为内联 JSON。 你可以在浏览器（使用 **Bash** 环境）的 [Azure Cloud Shell](../articles/cloud-shell/overview.md) 中运行命令，如果 [已在本地安装](/cli/azure/install-azure-cli) CLI，则也可以在你的计算机上运行。

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' --dt-name {digital_twins_instance_name}
```