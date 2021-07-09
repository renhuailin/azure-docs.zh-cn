---
author: baanders
description: include 文件用于将模型上传到 Azure 数字孪生实例
ms.service: digital-twins
ms.topic: include
ms.date: 4/1/2021
ms.author: baanders
ms.openlocfilehash: 9fcbfc0b0fccb5f130f0c554bac93748176387b7
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111438966"
---
该模型下所示：
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

若要 **将此模型上传到孪生实例**，请运行以下 Azure CLI 命令，该命令会将上述模型上传为内联 JSON。 可以在浏览器的 [Azure Cloud Shell](../articles/cloud-shell/overview.md) 中（使用 Bash 环境）运行此命令，如果[已在本地安装 CLI](/cli/azure/install-azure-cli)，还可以在计算机上运行此命令。

```azurecli-interactive
az dt model create --dt-name <instance-name> --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' 
```