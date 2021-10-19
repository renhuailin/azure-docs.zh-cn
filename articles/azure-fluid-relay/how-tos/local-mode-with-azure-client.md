---
title: 如何：使用 AzureClient 进行本地测试
description: 如何使用 AzureClient 在没有服务的情况下测试应用程序
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: reference
ms.service: azure-fluid
ms.openlocfilehash: f73c5e62471f6c038d7338244ec4756b533fe2d7
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661639"
---
# <a name="how-to-use-azureclient-for-local-testing"></a>如何：使用 AzureClient 进行本地测试

> [!NOTE]
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。

本文逐步介绍如何在本地模式下配置 AzureClient 并使用它在本地测试 Fluid 应用程序。

## <a name="configure-and-create-an-azureclient"></a>配置和创建 AzureClient

AzureClient 可以配置为针对本地 Azure Fluid Relay 实例运行，方法是向其传递如下所示的配置。

```js
    import { AzureClient, AzureConnectionConfig, LOCAL_MODE_TENANT_ID } from "@fluidframework/azure-client";
    import { InsecureTokenProvider } from "@fluidframework/test-client-utils";

    const clientProps = {
        connection: {
            tenantId: LOCAL_MODE_TENANT_ID,
            tokenProvider: new InsecureTokenProvider("", { id: "123", name: "Test User" }),
            orderer: "http://localhost:7070",
            storage: "http://localhost:7070",
        },
    };

    const azureClient = new AzureClient(clientProps);
```

此示例使用 InsecureTokenProvider 来生成和签署 Azure Fluid Relay 服务将接受的身份验证令牌。 但是，从名称可以看出来，这是不安全的，不应在生产环境中使用。 有关 InsecureTokenProvider 的详细信息，请参阅[应用中的身份验证和授权](https://fluidframework.com/docs/build/auth/#the-token-provider)。

若要在本地运行，首先需要将 Orderer 和存储 URL 配置为指向本地 Azure Fluid Relay 服务实例运行时使用的域和端口（默认为 http://localhost:7070 ）。 最后一步是将 `tenantId` 设置为 `LOCAL_MODE_TENANT_ID`。 结合所有这些设置，将 AzureClient 配置为与本地 Azure Fluid Relay 服务一起使用。  

## <a name="enabling-debug-logging"></a>启用调试日志记录

可以在浏览器控制台中使用以下设置，从 Fluid Framework 启用内置调试日志记录。

`localStorage.debug = 'fluid:*'`

在更高级的方案中，可以将 `logger` 传递给 AzureClient。 这样一来，就可以自定义日志记录行为。 有关记录器或遥测的详细信息，请参阅 fluidframework.com 上的 [Logging and telemetry](https://fluidframework.com/docs/testing/telemetry/)（日志记录和遥测）。 

## <a name="running-azure-fluid-relay-service-locally"></a>在本地运行 Azure Fluid Relay 服务

若要使用 AzureClient 的本地模式，首先需要启动本地服务器。 从终端窗口运行 `npx @fluidframework/azure-local-service@latest` 将启动 Azure Fluid Relay 本地服务器。 服务器启动后，就可以针对本地服务运行应用程序。
