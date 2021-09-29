---
title: 在 Azure API 管理中设置 Service Fabric 后端 | Microsoft Docs
description: 如何使用 Azure 门户在 Azure API 管理中创建 Service Fabric 服务后端
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: danlep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 01a7a7c34b7253acdcf046c47643d42dcdee2cb6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128666754"
---
# <a name="set-up-a-service-fabric-backend-in-api-management-using-the-azure-portal"></a>使用 Azure 门户在 API 管理中设置 Service Fabric 后端

本文介绍了如何使用 Azure 门户将 [Service Fabric](../service-fabric/service-fabric-api-management-overview.md) 服务配置为自定义 API 后端。 出于演示目的，它展示了如何将基本的无状态 ASP.NET Core Reliable Service 设置为 Service Fabric 后端。

有关背景，请参阅 [API 管理中的后端](backends.md)。

## <a name="prerequisites"></a>先决条件

将运行 Windows 的 Service Fabric 群集中的示例服务配置为自定义后端的先决条件：

* **Windows 开发环境** - 安装 [Visual Studio 2019](https://www.visualstudio.com) 和 **Azure 开发** 工作负荷、**ASP.NET 和 Web 开发** 工作负荷以及 **.NET Core 跨平台开发** 工作负荷。 然后设置 [.NET 开发环境](../service-fabric/service-fabric-get-started.md)。

* **Service Fabric 群集** - 请参阅 [教程：将运行 Windows 的 Service Fabric 群集部署到 Azure 虚拟网络](../service-fabric/service-fabric-tutorial-create-vnet-and-windows-cluster.md)。 你可以使用现有的 X.509 证书创建群集，也可以出于测试目的创建一个新的自签名证书。 群集在虚拟网络中创建。

* **示例 Service Fabric 应用** - 创建一个 Web API 应用并将其部署到 Service Fabric 群集，如 [在 Azure 中将 API 管理与 Service Fabric 集成](../service-fabric/service-fabric-tutorial-deploy-api-management.md)所述。

    这些步骤使用默认的 Web API 项目模板创建一个基本的无状态 ASP.NET Core Reliable Service。 然后，你通过 Azure API 管理公开此服务的 HTTP 终结点。

    记下应用程序名称，例如 `fabric:/myApplication/myService`。 

* **API 管理实例** - 处于 **高级** 或 **开发人员** 层级且与 Service Fabric 群集位于同一区域中的现有的或新的 API 管理实例。 如果需要一个，请[创建 API 管理实例](get-started-create-service-instance.md)。

* **虚拟网络** - 将你的 API 管理实例添加到为 Service Fabric 群集创建的虚拟网络。 API 管理需要虚拟网络中的一个专用子网。

  有关为 API 管理实例启用虚拟网络连接的步骤，请参阅[如何将 Azure API 管理与虚拟网络配合使用](api-management-using-with-vnet.md)。

## <a name="create-backend---portal"></a>创建后端 - 门户

### <a name="add-service-fabric-cluster-certificate-to-api-management"></a>将 Service Fabric 群集证书添加到 API 管理

Service Fabric 群集证书在与群集关联的 Azure 密钥保管库中存储和管理。 将此证书作为客户端证书添加到 API 管理实例。

有关将证书添加到 API 管理实例的步骤，请参阅[如何在 Azure API 管理中使用客户端证书身份验证保护后端服务](api-management-howto-mutual-certificates.md)。 

> [!NOTE]   
> 建议通过引用密钥保管库证书将证书添加到 API 管理。 

### <a name="add-service-fabric-backend"></a>添加 Service Fabric 后端

1. 在 [Azure 门户](https://portal.azure.com)，导航到 API 管理实例。
1. 在“API”下，选择“后端” > “+ 添加”。
1. 输入后端名称和可选说明。
1. 在“类型”中，选择“Service Fabric”。
1. 在“运行时 URL”中，输入 API 管理要将请求转发到其中的 Service Fabric 后端服务的名称。 示例：`fabric:/myApplication/myService`。 
1. 在“分区解析重试次数上限”中，输入一个介于 0 到 10 之间的数字。
1. 输入 Service Fabric 群集的管理终结点。 此终结点是端口 `19080` 上的群集的 URL，例如 `https://mysfcluster.eastus.cloudapp.azure.com:19080`。
1. 在“客户端证书”中，选择你在上一部分添加到 API 管理实例的 Service Fabric 群集证书。
1. 在“管理终结点授权方法”中，输入 Service Fabric 群集管理服务用于 TLS 通信的证书的指纹或服务器 X509 名称。
1. 启用“验证证书链”和“验证证书名称”设置。
1. 必要时在“授权凭据”中提供凭据，以便访问 Service Fabric 中配置的后端服务。 对于此方案中使用的示例应用，不需要使用授权凭据。
1. 选择“创建”。

:::image type="content" source="media/backends/create-service-fabric-backend.png" alt-text="创建 Service Fabric 后端":::

## <a name="use-the-backend"></a>使用后端

若要使用自定义后端，请使用 [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) 策略引用它。 此策略将传入 API 请求的默认后端服务基 URL 转换为指定的后端（在本例中为 Service Fabric 后端）。 

`set-backend-service` 策略对于现有 API 可能很有用，可以将传入请求转换为与 API 设置中指定的后端不同的后端。 为了在本文中进行演示，请创建一个测试 API 并将策略设置为将 API 请求定向到 Service Fabric 后端。 

### <a name="create-api"></a>创建 API

按照[手动添加 API](add-api-manually.md) 中的步骤创建一个空白 API。

* 在 API 设置中，将“Web 服务 URL”留空。
* 添加“API URL 后缀”，例如 fabric。

  :::image type="content" source="media/backends/create-blank-api.png" alt-text="创建空白 API":::

### <a name="add-get-operation-to-the-api"></a>向 API 中添加 GET 操作

如[部署 Service Fabric 后端服务](../service-fabric/service-fabric-tutorial-deploy-api-management.md#deploy-a-service-fabric-back-end-service)所示，Service Fabric 群集上部署的示例 ASP.NET Core 服务支持 URL 路径 `/api/values` 上的单个 HTTP GET 操作。

该路径上的默认响应是包含两个字符串的一个 JSON 数组：

```json
["value1", "value2"]
```

若要测试 API 管理与群集的集成，请在路径 `/api/values` 上向 API 添加相应的 GET 操作：

1. 选择上一步中创建的 API。
1. 选择“+ 添加操作”。
1. 在“前端”窗口中，输入以下值，然后选择“保存”。 

     | 设置             | “值”                             | 
    |---------------------|-----------------------------------|
    | **显示名称**    | 测试后端                       |  
    | **URL** | GET                               | 
    | **URL**             | `/api/values`                           | 
    
    :::image type="content" source="media/backends/configure-get-operation.png" alt-text="向 API 中添加 GET 操作":::

### <a name="configure-set-backend-policy"></a>配置 `set-backend` 策略

将 [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) 策略添加到测试 API。

1. 在“设计”选项卡上的“入站处理”部分，选择代码编辑器 ( **</>** ) 图标。 
1. 将光标置于 **&lt;inbound&gt;** 元素内。
1. 添加以下策略语句。 在 `backend-id` 中，使用你的 Service Fabric 后端的名称进行替换。

   如果未解析群集分区，则 `sf-resolve-condition` 是重试条件。 重试次数已在配置后端时设置。

    ```xml
    <set-backend-service backend-id="mysfbackend" sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")"  />
    ```
1. 选择“保存”。

    :::image type="content" source="media/backends/set-backend-service.png" alt-text="配置 set-backend-service 策略":::

### <a name="test-backend-api"></a>测试后端 API

1. 在“测试”选项卡上，选择你在前面部分创建的“GET”操作。
1. 选择 **Send**。

当配置正确时，HTTP 响应会显示一个 HTTP 成功代码，并显示从后端 Service Fabric 服务返回的 JSON。

:::image type="content" source="media/backends/test-backend-service.png" alt-text="测试 Service Fabric 后端":::

## <a name="next-steps"></a>后续步骤

* 了解如何[配置策略](api-management-advanced-policies.md)以将请求转发到后端
* 还可以使用 API 管理 [REST API](/rest/api/apimanagement/2020-06-01-preview/backend)、[Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend) 或 [Azure 资源管理器模板](../service-fabric/service-fabric-tutorial-deploy-api-management.md)来配置后端。

