---
author: baanders
description: include 文件，用于描述用于解决 Azure 数字孪生的跨租户限制的令牌解决方案
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 08c48b3600a975f43a5ad94e2cc6efd88047d0d8
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108205642"
---
执行此操作的一种方法是使用以下 CLI 命令，其中 `<home-tenant-ID>` 是包含 Azure 数字孪生实例的 Azure AD 租户的 ID：

```azurecli-interactive
az account get-access-token --tenant <home-tenant-ID> --resource https://digitaltwins.azure.net
```

发出请求后，该标识将接收为 `https://digitaltwins.azure.net` Azure AD 资源颁发的令牌，该令牌具有与 Azure 数字孪生实例相匹配的租户 ID 声明。 如果在 API 请求或 `Azure.Identity` 代码中使用此令牌，应该可以使用联合标识访问 Azure 数字孪生资源。
