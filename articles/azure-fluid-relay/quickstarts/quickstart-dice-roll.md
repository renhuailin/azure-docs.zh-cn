---
title: 快速入门：摇骰子
description: 使用 Azure Fluid Relay 服务快速创建摇骰子应用
author: hickeys
ms.service: azure-fluid
ms.topic: quickstart
ms.date: 09/09/2021
ms.author: hickeys
ms.openlocfilehash: f3a25a47cc1bee143f562e936f7927f48d452b4c
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129662022"
---
# <a name="quickstart-dice-roller"></a>快速入门：摇骰子

> [!NOTE]
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。

本快速入门将演练创建使用 Azure Fluid Relay 服务的摇骰子应用的过程。 此快速入门分为两部分。 第一部分将创建应用本身，并针对本地 Fluid 服务器运行它。 第二部分将重新配置应用，以针对 Azure Fluid Relay 服务而不是本地开发服务器运行。

此快速入门中使用的示例代码可在[此处](https://github.com/microsoft/FluidHelloWorld/tree/main-azure)找到。

## <a name="set-up-your-development-environment"></a>设置开发环境

若要按照快速入门执行操作，需要一个 Azure 帐户，并[预配了 Azure Fluid Relay 服务](../how-tos/provision-fluid-azure-portal.md)。 如果没有帐户，可以[免费适用 Azure](https://azure.com/free)。

还需要在计算机上安装以下软件。

- 代码编辑器 - 建议使用 [Visual Studio Code](https://code.visualstudio.com/)。
- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/en/download) 12.17 或更高版本

## <a name="getting-started-locally"></a>本地入门

首先，需要从 GitHub 下载示例应用。 打开新的命令窗口并导航到要下载代码的文件夹，然后使用 Git 克隆 [FluidHelloWorld 存储库](https://github.com/microsoft/FluidHelloWorld)。 克隆过程将创建一个名为 FluidHelloWorld 的子文件夹，该子文件夹包含项目文件。

```cli
git clone -b main-azure https://github.com/microsoft/FluidHelloWorld.git
```

导航到新创建的文件夹，安装依赖项，然后启动应用。

```cli
cd FluidHelloWorld
npm install
...
npm start
```


运行命令 `npm start` 时，将发生两个情况。 首先，将在本地进程中启动 Fluid 服务器。 此服务器仅用于开发。 稍后将升级到 Azure 托管的生产服务器。 其次，新的浏览器选项卡将打开一个页面，其中包含摇骰子应用的新实例。 可以使用同一 URL 打开新选项卡，以创建摇骰子应用的其他实例。 默认情况下，应用的每个实例都配置为使用本地 Fluid 服务。 单击任何应用实例中的“滚动”按钮，并注意骰子的状态会在每个客户端中更改。

## <a name="upgrading-to-azure-fluid-relay"></a>升级到 Azure Fluid Relay

若要针对 Azure Fluid Relay 服务运行，需要更新应用的配置，以连接到 Azure 服务而不是本地服务器。

### <a name="configure-and-create-an-azure-client"></a>配置和创建 Azure 客户端

若要配置 Azure 客户端，请将 `app.js` 的 `serviceConfig` 对象中的值替换为 Azure Fluid Relay 服务配置值。 可以在 Azure 门户的 Fluid Relay 资源的“访问密钥”部分中找到这些值。

```javascript
const serviceConfig = {
    connection: {
        tenantId: LOCAL_MODE_TENANT_ID, // REPLACE WITH YOUR TENANT ID
        tokenProvider: new InsecureTokenProvider("" /* REPLACE WITH YOUR PRIMARY KEY */, { id: "userId" }),
        orderer: "http://localhost:7070", // REPLACE WITH YOUR ORDERER ENDPOINT
        storage: "http://localhost:7070", // REPLACE WITH YOUR STORAGE ENDPOINT
    }
};
```

> [!WARNING]
> 在开发过程中，可以使用 `InsecureTokenProvider` 生成 Azure Fluid Relay 服务将接受的身份验证令牌，并对其进行签名。 但是，从名称可以看出来，这是不安全的，不应在生产环境中使用。 Azure Fluid Relay 资源创建过程提供了一个密钥，可用于对安全请求进行签名。 为了确保此机密不会被泄露，应将其替换为 ITokenProvider 的另一个实现，该实现在发布到生产之前从开发人员提供的安全后端服务提取令牌。

### <a name="build-and-run-the-client-only"></a>仅生成并运行客户端

现在，你已指定应用使用 Azure 而不是本地服务器，因此无需在启动客户端应用时启动本地 Fluid 服务器。 可以使用此命令，在启动客户端时不同时启动服务器。 

```bash
npm run start:client
```

🥳恭喜🎉 你已成功迈出了开启 Fluid 协作世界的第一步。
