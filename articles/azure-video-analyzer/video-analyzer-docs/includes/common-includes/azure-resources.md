---
author: naiteeks
ms.topic: include
ms.service: azure-video-analyzer
ms.date: 04/20/2021
ms.author: juliako
ms.openlocfilehash: 1cce5d0f49604ce310e40de037b9a77e04615bc9
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110371779"
---
部署过程大约需要 20 分钟。 完成部署后，Azure 订阅中会部署某些 Azure 资源，包括：

1. **视频分析器帐户** - 此 [云服务](../../overview.md)用于注册视频分析器边缘模块，并用于播放录制的视频和视频分析。
1. **存储帐户** - 用于存储录制的视频和视频分析。
1. **托管标识** - 这是用户分配的[托管标识](../../../../active-directory/managed-identities-azure-resources/overview.md)，用于管理对上述存储帐户的访问。
1. **虚拟机** - 这是将用作模拟边缘设备的虚拟机。
1. **IoT 中心** - 这充当消息中心，用于在 IoT 应用程序、IoT Edge 模块以及它管理的设备之间进行双向通信。

除上述资源外，还会在存储帐户的“deployment-output”文件共享中创建以下项，用于快速入门和教程：

- **appsettings.json** - 此文件包含设备连接字符串和在 Visual Studio Code 中运行示例应用程序所需的其他属性。
- **env.txt** - 此文件包含使用 Visual Studio Code 生成部署清单所需的环境变量。
- **deployment.json** - 这是模板用于将 Edge 模块部署到模拟边缘设备的部署清单。

<!-- TODO: provide a link to the readme.md in github.com/azure-video-analyzer/setup/readme.md where we can list out all resources like virtual network etc. -->

> [!TIP]
> 如果在创建 Azure 资源时遇到问题，请查看[故障排除指南](../../troubleshoot.md)来解决一些常见问题。
