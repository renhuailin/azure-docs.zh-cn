---
title: 将自己的函数引入 Azure Static Web Apps
description: 将现有的 Azure Functions 应用与 Azure Static Web Apps 站点一起使用。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/07/2021
ms.author: cshoe
ms.openlocfilehash: 7925bd70488106943f0030e6b26534938fbc7d36
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128570884"
---
# <a name="bring-your-own-functions-to-azure-static-web-apps"></a>将自己的函数引入 Azure Static Web Apps

有两种可能的配置支持 Azure Static Web Apps API：托管函数和自带函数。 有关这两种配置的区别的详细信息，请参阅 [API 参考](apis.md)。

本文演示如何将现有的 Azure Functions 应用链接到 Azure Static Web Apps 资源。

> [!NOTE]
> 只能在 Azure Static Web Apps 标准计划中使用自带函数。

## <a name="example"></a>示例

假设有一个 Azure Functions 应用，该应用通过以下位置公开终结点。

```url
https://my-functions-app.azurewebsites.net/api/getProducts
```

链接之后，可以通过静态 Web 应用中的 `api` 路径来访问相同的终结点，如本示例 URL 所示。

```url
https://red-sea-123.azurestaticapps.net/api/getProducts
```

 两个终结点 URL 都指向同一函数。

## <a name="link-an-existing-azure-functions-app"></a>链接现有 Azure Functions 应用

在关联现有 Functions 应用之前，首先需要根据静态 Web 应用的配置进行调整。

1. 在`""`工作流配置[文件中，将 `api_location` 值设置为空白字符串（](./build-configuration.md)）。

1. 在 [Azure 门户](https://portal.azure.com)中打开静态 Web 应用实例。

1. 从“设置”菜单中，选择“Functions”。

1. 从“环境”下拉列表中，选择“生产”。

1. 在“Functions 源”标签旁边，选择“连接到 Functions 应用”。

1. 从“订阅”下拉列表中，选择自己的 Azure 订阅名称。

1. 从“Functions 应用”下拉菜单中，选择要链接到 Static Web Apps 的现有Functions 应用的名称。

1. 选择“链接”按钮。

    :::image type="content" source="media/functions-bring-your-own/azure-static-web-apps-link-existing-functions-app.png" alt-text="链接现有 Functions 应用":::

> [!IMPORTANT]
> 在链接现有的 Functions 应用程序之前，请确保在[工作流配置](./build-configuration.md)文件中，将 `api_location` 值设置为空白字符串（`""`）。

## <a name="deployment"></a>部署

你需要负责为 Azure Functions 设置[部署工作流](../azure-functions/functions-deployment-technologies.md)。

## <a name="security-constraints"></a>安全约束

- 身份验证和授权：如果尚未在现有 Functions 应用上设置身份验证和授权策略，则静态 Web 应用对 API 具有独占访问权。 若要使 Functions 应用可供其他应用程序访问，请添加另一个标识提供者或更改安全设置以允许未经身份验证的访问。

  > [!NOTE]
  > 如果在链接的 Functions 应用中启用身份验证和授权，则必须使用 Azure 应用服务授权，并且授权提供程序为版本 2。

- 必需的公共可访问性：现有 Functions 应用不需要应用以下安全配置。
  - 限制 Functions 应用的 IP 地址。
  - 限制通过专用链接或服务终结点的流量。

- 函数访问密钥：如果函数需要[访问密钥](../azure-functions/security-concepts.md#function-access-keys)，则必须向密钥提供从静态应用到 API 的调用。

## <a name="restrictions"></a>限制

- 一个 Azure Functions Web 应用只能供一个静态 Web 应用使用。
- [工作流配置](./build-configuration.md)中的 `api_location` 值必须设置为空白字符串。
- 仅在静态 Web 应用生产环境中受支持。
- 虽然 Azure Functions 应用可能会响应各种触发器，但静态 Web 应用只能通过 Http 终结点访问函数。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加 API](add-api.md)
