---
title: 如何：使用 Azure 函数编写 TokenProvider
description: 如何将自定义令牌提供程序编写为 Azure 函数并对其进行部署。
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/build/tokenproviders/
ms.openlocfilehash: d6987b4e4592167fcb41a7f6654ff46140a79724
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661541"
---
# <a name="how-to-write-a-tokenprovider-with-an-azure-function"></a>如何：使用 Azure 函数编写 TokenProvider

> [!NOTE]
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。

在 [Fluid Framework](https://fluidframework.com/) 中，TokenProvider 负责创建和签署令牌，`@fluidframework/azure-client` 使用这些令牌向 Azure Fluid Relay 服务发出请求。 Fluid Framework 提供一个简单但不安全的 TokenProvider 用于开发目的，并且形象地将其命名为 InsecureTokenProvider。 每个 Fluid 服务都必须基于特定服务的身份验证和安全考虑实现一个自定义 TokenProvider。

## <a name="implementing-your-own-tokenprovider-class"></a>实现你自己的 TokenProvider 类

你创建的每个 Azure Fluid Relay 服务租户都分配有租户 ID 及其独有的租户密钥。 该密钥是一种共享机密。 你的应用/服务知道这一点，Azure Fluid Relay 服务也知道。 

TokenProvider 必须知道密钥才能签署请求，但密钥不能包含在客户端代码中。 TokenProvider 在运行时联系 Fluid 服务器，以便安全地获取密钥，而不将其公开给客户端。 这通过两个单独的 API 调用来实现：`fetchOrdererToken` 和 `fetchStorageToken`。 它们分别负责从主机提取 Orderer 和存储 URL。 这两个函数都返回表示令牌值的 `TokenResponse` 对象。

## <a name="tokenprovider-class-example"></a>TokenProvider 类示例

若要生成安全的令牌提供程序，可以选择创建无服务器 Azure 函数并将其公开为令牌提供程序。 这样，你就可以将租户密钥存储在安全的服务器上。 然后，应用程序调用 Azure 函数以生成令牌。

此示例在名为 AzureFunctionTokenProvider 的类中实现该模式。 它接受 Azure 函数的 URL、`userId` 和 `userName`。 此特定实现也作为 `@fluidframework/azure-client` 包的导出提供给你。

```typescript
import { ITokenProvider, ITokenResponse } from "@fluidframework/azure-client";

export class AzureFunctionTokenProvider implements ITokenProvider {
  constructor(
    private readonly azFunctionUrl: string,
    private readonly userId: string,
    private readonly userName: string,
  );

  public async fetchOrdererToken(tenantId: string, documentId: string): Promise<ITokenResponse> {
        return {
            jwt: await this.getToken(tenantId, documentId),
        };
    }

    public async fetchStorageToken(tenantId: string, documentId: string): Promise<ITokenResponse> {
        return {
            jwt: await this.getToken(tenantId, documentId),
        };
    }
}
```

为了确保租户密钥的安全，它存储在安全的后端位置，并且只能从 Azure 函数内部访问。 可通过以下方式提取签名令牌：向 Azure 函数发出 `GET` 请求，并提供 `tenantID`、`documentId` 以及 `userID`/`userName`。 Azure 函数负责租户 ID 和租户密钥之间的映射，以适当地生成和签署令牌，使 Azure Fluid Relay 服务能够接受它。

```typescript
private async getToken(tenantId: string, documentId: string): Promise<string> {
    const params = {
        tenantId,
        documentId,
        userId: this.userId,
        userName: this.userName,
    };
    const token = this.getTokenFromServer(params);
    return token;
}
```

下面的示例使用 [`axios`](https://www.npmjs.com/package/axios) 库发出 HTTP 请求。 你可以使用其他库或方法发出 HTTP 请求。

```typescript
private async getTokenFromServer(input: any): Promise<string> {
    return axios.get(this.azFunctionUrl, {
        params: input,
    }).then((response) => {
        return response.data as string;
    }).catch((err) => {
        return err as string;
    });
}
```

此示例演示如何创建你自己的 HTTPTrigger Azure 函数，以便通过传入租户密钥来提取令牌。

```typescript
import { AzureFunction, Context, HttpRequest } from "@azure/functions";
import { ScopeType } from "@fluidframework/azure-client";
import { generateToken } from "@fluidframework/azure-service-utils";

//Replace "myTenantKey" with your key here.
const key = "myTenantKey";

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    // tenantId, documentId, userId and userName are required parameters
    const tenantId = (req.query.tenantId || (req.body && req.body.tenantId)) as string;
    const documentId = (req.query.documentId || (req.body && req.body.documentId)) as string;
    const userId = (req.query.userId || (req.body && req.body.userId)) as string;
    const userName = (req.query.userName || (req.body && req.body.userName)) as string;
    const scopes = (req.query.scopes || (req.body && req.body.scopes)) as ScopeType[];

    if (!tenantId) {
        context.res = {
            status: 400,
            body: "No tenantId provided in query params",
        };
    }

    if (!key) {
        context.res = {
            status: 404,
            body: `No key found for the provided tenantId: ${tenantId}`,
        };
    }

    if (!documentId) {
        context.res = {
            status: 400,
            body: "No documentId provided in query params"
        };
    }

    let user = { name: userName, id: userId };

    // Will generate the token and returned by an ITokenProvider implementation to use with the AzureClient.
    const token = generateToken(
        tenantId,
        documentId,
        key,
        scopes ?? [ScopeType.DocRead, ScopeType.DocWrite, ScopeType.SummaryWrite],
        user
    );

    context.res = {
        status: 200,
        body: token
    };
};

export default httpTrigger;
```

`generateToken` 函数会为给定用户生成一个使用租户密钥签署的令牌。 这样一来，在将令牌返回给客户端时就无需向其公开密钥本身。 相反，生成的令牌用于提供对给定文档的范围内访问。 此令牌可由 `ITokenProvider` 实现返回，以用于 `AzureClient`。

## <a name="see-also"></a>请参阅

- [向身份验证令牌添加自定义数据](connect-fluid-azure-service.md#adding-custom-data-to-tokens)
