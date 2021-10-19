---
title: 如何：将测试自动化与 Azure Fluid Relay 一起使用
description: 如何使用测试自动化库为 Fluid 应用程序创建自动测试
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/testing/testing/
ms.openlocfilehash: 0087be9dccc1b040720ea53f5e6aae6d64f6fa17
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661575"
---
# <a name="how-to-use-test-automation-with-azure-fluid-relay"></a>如何：将测试自动化与 Azure Fluid Relay 一起使用

> [!NOTE]
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。

测试和自动化对于保持代码的质量和使用寿命至关重要。 在内部，Fluid 使用由 [Mocha](https://mochajs.org/)、[Jest](https://jestjs.io/)、[Puppeteer](https://github.com/puppeteer/puppeteer) 和 [Webpack](https://webpack.js.org/) 提供支持的一系列单元和集成测试。

你可以使用本地 @fluidframework/azure-local-service 或使用 Azure Fluid Relay 服务中的测试租户运行测试。 AzureClient 可以配置为连接到远程服务和本地服务，这使你能够在针对实时和本地服务实例的测试之间使用单一客户端类型。 唯一的区别是用于创建客户端的配置。

## <a name="automation-against-azure-fluid-relay"></a>针对 Azure Fluid Relay 的自动化

自动化可以通过与生产租户相同的方式连接到 Azure Fluid Relay 的测试租户，并且只需要适当的连接配置。 有关更多详细信息，请参阅[如何：连接到 Azure Fluid Relay 服务](connect-fluid-azure-service.md)。

## <a name="creating-an-adaptable-test-client"></a>创建适应性强的测试客户端

为了创建适应性强的测试客户端，你需要根据服务目标对 AzureClient 进行不同的配置。 下面的函数使用环境变量来确定这一点。 你可以在测试脚本中设置环境变量来控制目标服务。

```typescript
function createAzureClient(): AzureClient {
    const useAzure = process.env.FLUID_CLIENT === "azure";
    const tenantKey = useAzure ? process.env.FLUID_TENANTKEY as string : "";
    const user = { id: "userId", name: "Test User" };

    const connectionConfig = useAzure ? {
        tenantId: "myTenantId",
        tokenProvider: new InsecureTokenProvider(tenantKey, user),
        orderer: "https://myOrdererUrl",
        storage: "https://myStorageUrl",
    } : {
        tenantId: LOCAL_MODE_TENANT_ID,
        tokenProvider: new InsecureTokenProvider("", user),
        orderer: "http://localhost:7070",
        storage: "http://localhost:7070",
    };

    const clientProps = {
        connection: config,
    };

    return new AzureClient(clientProps);
}
```

测试可以调用此函数来创建 AzureClient 对象，而无需考虑底层服务。 下面的 [Mocha](https://mochajs.org/) 测试会在运行任何测试之前创建服务客户端，然后使用它来运行每个测试。 其中有一个使用服务客户端来创建容器的测试，只要不引发任何错误，测试就会通过。

```typescript
describe("ClientTest", () => {
    const client = createAzureClient();
    let documentId: string;

    it("can create Azure container successfully", async () => {
        const schema: ContainerSchema = {
            initialObjects: {
                customMap: SharedMap
            },
        };
        documentId = await container.attach();
        const { container, services } = await azureClient.createContainer(schema);
    });
});

```

## <a name="running-tests"></a>运行测试

可以在项目的 package.json 中添加以下 npm 脚本来运行测试：

```json
"scripts": {
    "start:local": "npx @fluidframework/azure-local-service@latest > local-service.log 2>&1",
    "test:mocha": "mocha",
    "test:azure": "cross-env process.env.FLUID_CLIENT='\"azure\"' && npm run test:mocha",
    "test:local": "start-server-and-test start:local 7070 test:mocha"
}
```

若要安装上述脚本所需的依赖项，可以使用以下命令：

```bash
npm install cross-env start-server-and-test mocha
```

`test:local` 脚本使用 [start-server-and-test](https://www.npmjs.com/package/start-server-and-test) 库启动本地服务器，等待 7070 端口（本地服务器使用的默认端口）响应，运行测试脚本，然后终止本地服务器。
