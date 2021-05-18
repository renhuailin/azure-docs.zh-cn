---
title: 查找 Azure Service Fabric 网格示例
description: 下面是可用 Service Fabric 网格示例应用程序的索引。 这些示例中的源代码展示了如何使用 Service Fabric 资源模型实现特定的方案。
ms.date: 12/03/2018
ms.topic: conceptual
ms.openlocfilehash: 2b38cd6a6c0f3aaab4ff5b77be82aee242afef23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99627022"
---
# <a name="find-service-fabric-mesh-samples"></a>查找 Service Fabric 网格示例

> [!IMPORTANT]
> Azure Service Fabric 网格的预览版已停用。 不允许再通过 Service Fabric 网格 API 进行新的部署。 对现有部署的支持将会持续到 2021 年 4 月 28 日。
> 
> 有关详细信息，请参阅 [Azure Service Fabric 网格预览版停用](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)。

此表概述了可用的 Service Fabric 网格示例应用程序。 这些示例中的源代码展示了如何使用 Service Fabric 资源模型实现特定的方案。

有关直接将模板部署到 Azure 的详细信息，请参阅[示例模板 GitHub 页面](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md)。

|示例模板|方案描述|源代码|开发人员工具|
|------------|--------------------|----------|----------------------|
| [Hello World 应用](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | 承载在容器中的静态网页。 对于 Linux，它使用 nginx；对于 Windows，它使用 IIS | [源代码](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | 无要求 |
| [适用于 Azure 文件卷的计数器应用](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.md) | 通过将基于 Azure 文件的卷装载在容器内来存储状态。 <br><br> **注意：** 此模板需要一个已预配了 [说明](../storage/files/storage-how-to-create-file-share.md)的“Azure 文件”文件共享 | [源代码](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio 网格工具 |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | 创建一个具有前端和后端服务且使用基于 DNS 的解析的应用程序。 在[此处](./service-fabric-mesh-tutorial-create-dotnetcore.md)用作教程 | [源代码](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Visual Studio 网格工具 |
| [视觉对象应用](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | 缩放和升级应用程序中的微服务。 | [源代码](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Visual Studio 网格工具 |
| [投票应用](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | 创建一个具有前端和后端服务且使用基于 DNS 的解析的应用程序 | [源代码](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | 对于 Windows 版本，使用 Visual Studio 网格工具，对于 Linux 版本，可以使用 VS Code/dotnet CLI |
| [适用于 Service Fabric 可靠卷的计数器应用](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.sfreliablevolume.md)| 通过将基于 Service Fabric 可靠磁盘的卷装载在容器内来存储状态。| [源代码](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio 网格工具 |
