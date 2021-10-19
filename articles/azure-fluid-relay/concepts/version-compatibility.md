---
title: Fluid Framework 发行版的版本兼容性
description: >
  如何确定 Fluid Framework 发行版的哪些版本与 Azure Fluid Relay 服务兼容。
services: azure-fluid
author: tylerbutler
ms.author: tylerbu
ms.date: 09/28/2021
ms.topic: article
ms.service: azure-fluid
ms.openlocfilehash: 62493eeb270a171fe874a877dfd51ac404eaab27
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661567"
---
# <a name="version-compatibility-with-fluid-framework-releases"></a>Fluid Framework 发行版的版本兼容性

> [!NOTE]
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。

若要将应用程序连接到 Azure Fluid Relay 服务，请使用 @fluidframework/azure-client 库。 还需使用 fluid-framework 库来使用由 Fluid Framework 提供的核心数据结构。

由于使用的是 Azure Fluid Relay，因此应该首先安装最新版本的 @fluidframework/azure-client，并使用该版本确定要使用的 fluid-framework 版本。 库对它所依赖的 fluid-framework 包的版本表示对等依赖关系。

可以使用 [install-peerdeps](https://www.npmjs.com/package/install-peerdeps) 工具，通过以下命令安装 @fluidframework/azure-client 和兼容版本的 fluid-framework：

```bash
npx install-peerdeps @fluidframework/azure-client
```

> [!TIP]
> 公共预览期间，@fluidframework/azure-client 和 fluid-framework 的版本将匹配 。 也就是说，如果 @fluidframework/azure-client 的当前版本为 0.48，则它将与 fluid-framework 0.48 兼容 。 反之亦然。

## <a name="compatibility-table"></a>兼容性表

| npm 包                         | 最低版本 | API                                                              |
| ----------------------------------  | :-------------- | :--------------------------------------------------------------- |
| @fluidframework/azure-client        | [0.48.4][]      | [API](https://fluidframework.com/docs/apis/azure-client/)        |
| fluid-framework                     | [0.48.4][]      | [API](https://fluidframework.com/docs/apis/fluid-framework/)     |
| @fluidframework/azure-service-utils | [0.48.4][]      | [API](https://fluidframework.com/docs/apis/azure-service-utils/) |
| @fluidframework/test-client-utils   | [0.48.4][]      | [API](https://fluidframework.com/docs/apis/test-client-utils/)   |

[0.48.4]: https://fluidframework.com/docs/updates/v0.48/

## <a name="next-steps"></a>后续步骤

- [预配 Azure Fluid Relay 服务](../how-tos/connect-fluid-azure-service.md)
- [连接到 Azure Fluid Relay 服务](../how-tos/connect-fluid-azure-service.md)
- [使用 AzureClient 进行本地测试](../how-tos/local-mode-with-azure-client.md)
