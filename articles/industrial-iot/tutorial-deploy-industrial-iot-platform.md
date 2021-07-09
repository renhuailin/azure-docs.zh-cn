---
title: 部署 Azure 工业 IoT 平台
description: 本教程将介绍如何部署 IIoT 平台。
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 995ee04e913ad4e363045e0aacc295aaec25f3e6
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110677919"
---
# <a name="tutorial-deploy-the-azure-industrial-iot-platform"></a>教程：部署 Azure 工业 IoT 平台

在本教程中，学习以下内容：

> [!div class="checklist"]
> * 关于 IIoT 平台的主要组件
> * 关于不同的安装类型
> * 如何部署工业 IoT 平台

## <a name="prerequisites"></a>先决条件

- 必须创建 Azure 订阅
- 下载 [Git](https://git-scm.com/downloads)
- 用于身份验证的 Azure Active Directory (AAD) 应用注册需要“全局管理员”、“应用程序管理员”或“云应用程序管理员”权限才能提供租户范围的管理员许可（更多选项见下文）
- 部署支持的操作系统为 Windows、Linux 和 Mac
- IoT Edge 支持 Windows 10 IoT Enterprise LTSC 和 Ubuntu Linux 16.08/18.04 LTS Linux

## <a name="main-components"></a>主要组件

- 最小依赖项：IoT 中心、Cosmos DB、服务总线、事件中心、密钥保管库、存储
- 标准依赖项：最小 + SignalR 服务、AAD 应用注册、设备预配服务、时序见解、工作簿、日志分析、Application Insights
- 微服务：应用服务计划、应用服务
- UI（Web 应用）：（与微服务共享的）应用服务计划、应用服务
- 模拟：虚拟机、虚拟网络、IoT Edge
- Azure Kubernetes 服务

## <a name="installation-types"></a>安装类型

- 最小：最小依赖项
- 本地：最小和标准依赖项
- 服务：本地和微服务
- 模拟：最小依赖项和模拟组件
- 应用：服务和 UI
- 所有（默认）：应用和模拟

## <a name="deployment"></a>部署

1. 若要开始部署 IIoT 平台，请从命令提示符或终端克隆存储库。

    git clone https://github.com/Azure/Industrial-IoT  cd Industrial-IoT

2. 启动引导式部署，该脚本将收集所需的信息，例如 Azure 帐户、订阅、目标资源以及组和应用程序名称。

    在 Windows 上：
        ```
        .\deploy -version <version>
        ```

    在 Linux 或 Mac 上：
        ```
        ./deploy.sh -version <version>
        ```

    将 \<version> 替换为要部署的版本。

3. 微服务和 UI 是需要进行身份验证的 Web 应用程序，这需要在 AAD 中进行三次应用注册。 如果缺少所需的权限，则有两种可能的解决方案：

    - 要求 AAD 管理员为应用程序授予租户范围的管理员许可
    - AAD 管理员可以创建 AAD 应用程序。 deploy/scripts（部署/脚本）文件夹包含用于从部署中单独执行 AAD 注册的 aad- register.ps1 脚本。 脚本的输出是一个文件，其中包含要在部署过程中使用的相关信息，并且必须使用 - aadConfig 参数传递到同一文件夹中的 deploy.ps1 脚本。
        ```bash
        cd deploy/scripts
        ./aad-register.ps1 -Name <application-name> -Output aad.json
        ./deploy.ps1 -aadConfig aad.json
        ```

对于需要暂存、回滚、缩放和复原能力的生产部署，可以将平台部署到 [Azure Kubernetes 服务 (AKS) ](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-aks.md)

引用：
- [部署 Azure 工业 IoT 平台](https://github.com/Azure/Industrial-IoT/tree/master/docs/deploy)
- [如何进行一体化部署](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-all-in-one.md)
- [如何将平台部署到 AKS](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-aks.md)


## <a name="next-steps"></a>后续步骤
现在，您已部署 IIoT 平台，接下来可以了解如何自定义组件的配置：

> [!div class="nextstepaction"]
> [自定义组件的配置](tutorial-configure-industrial-iot-components.md)
