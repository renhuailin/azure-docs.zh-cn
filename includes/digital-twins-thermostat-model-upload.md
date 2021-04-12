---
author: baanders
description: include 文件用于将模型上传到 Azure 数字孪生实例
ms.service: digital-twins
ms.topic: include
ms.date: 3/23/2021
ms.author: baanders
ms.openlocfilehash: 987409f070f34f0fd9ee212fab8cc57e889e0146
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950582"
---
该模型下所示：
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

若要 **将此模型上传到孪生实例**，请运行以下 Azure CLI 命令，该命令会将上述模型上传为内联 JSON。 你可以在浏览器的 [Azure Cloud Shell](../articles/cloud-shell/overview.md) 中运行命令，如果[本地安装](/cli/azure/install-azure-cli)了 CLI，也可以在计算机上运行。

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

> [!Note]
> 如果在 PowerShell 环境中使用 Cloud Shell，可能需要对内联 JSON 字段上的引号字符进行转义，才能正确分析它们的值。 下面是进行这种修改后用来上传模型的命令：
>
> 上传模型：
> ```azurecli-interactive
> az dt model create --models '{  \"@id\": \"dtmi:contosocom:DigitalTwins:Thermostat;1\",  \"@type\": \"Interface\",  \"@context\": \"dtmi:dtdl:context;2\",  \"contents\": [    {      \"@type\": \"Property\",      \"name\": \"Temperature\",      \"schema\": \"double\"    }  ]}' -n {digital_twins_instance_name}
> ```