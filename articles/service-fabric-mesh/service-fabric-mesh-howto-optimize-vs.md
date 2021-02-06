---
title: 优化 Visual Studio for Azure Service Fabric 网格
description: 本文展示了如何针对 Service Fabric 网格项目优化 Visual Studio 性能以便首次调试运行 (F5) 更为快速。
author: georgewallace
ms.author: gwallace
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: e676286dca852c94f94e8bd8ff2ab73ee92b1412
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625746"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>针对 Service Fabric 网格项目优化 Visual Studio 性能

> [!IMPORTANT]
> Azure Service Fabric 网格的预览已停用。 不允许再通过 Service Fabric 的网格 API 来进行新的部署。 对现有部署的支持将持续到2021年4月28日。
> 
> 有关详细信息，请参阅 [Azure Service Fabric 网格预览停](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)用。

本文展示了如何针对 Service Fabric 网格项目优化 Visual Studio 性能以便首次调试运行 (F5) 更为快速。  

## <a name="change-visual-studio-settings"></a>更改 Visual Studio 设置
 
在 Visual Studio 中的 "**工具**  >  **选项**"   >  **Service Fabric "网格工具**" "  >  **常规**" 下，可以调整以下设置：

- “在项目打开时拉取所需的 Docker 映像”可以通过在项目加载时启动映像下载过程使首次调试运行 (F5) 更为快速。  
- “在项目打开时部署应用程序”可以通过在项目打开后启动部署过程使首次调试运行 (F5) 更为快速。  
- “在项目关闭时删除应用程序”通过在项目关闭时删除网格应用回收分配给该应用的资源（CPU、RAM）。  

当你在 Service Fabric 工具输出窗口中看到有消息指出 Visual Studio“正在拉取映像”、“正在预热”或“正在删除应用程序”时，说明它引用了上述设置。 可以关闭这些设置。

## <a name="next-steps"></a>后续步骤

通读[调试网格应用教程](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)