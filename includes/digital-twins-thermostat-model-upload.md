---
author: baanders
description: include 文件用于将模型上传到 Azure 数字孪生实例
ms.service: digital-twins
ms.topic: include
ms.date: 4/1/2021
ms.author: baanders
ms.openlocfilehash: add49cabaece1187cb9bcda3a94c92feb08b2439
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304285"
---
该模型下所示：
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

若要 **将此模型上传到孪生实例**，请运行以下 Azure CLI 命令，该命令会将上述模型上传为内联 JSON。 你可以在浏览器（使用 **Bash** 环境）的 [Azure Cloud Shell](../articles/cloud-shell/overview.md) 中运行命令，如果[已在本地安装](/cli/azure/install-azure-cli) CLI，则也可以在你的计算机上运行。

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```