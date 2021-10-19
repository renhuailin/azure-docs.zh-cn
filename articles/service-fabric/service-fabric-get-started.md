---
title: 设置 Windows 开发环境
description: 安装运行时、SDK 和工具并创建本地开发群集。 完成此设置后，就可以在 Windows 上开始生成应用程序。
author: peterpogorski
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: sfrev, devx-track-azurepowershell
ms.openlocfilehash: dac29dcce226ef7978353113a46dcfe9ae3ff327
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129657295"
---
# <a name="prepare-your-development-environment-on-windows"></a>在 Windows 上准备开发环境

> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [Mac OS X](service-fabric-get-started-mac.md)
>
>

若要在 Windows 开发计算机上生成并运行 [Azure Service Fabric 应用程序][1]，请安装 Service Fabric 运行时、SDK 和工具。 此外，还需要启用 SDK 中包含的 [Windows PowerShell 脚本执行](#enable-powershell-script-execution)。

## <a name="prerequisites"></a>先决条件

确保使用的是受支持的 [Windows 版本](service-fabric-versions.md#supported-windows-versions-and-support-end-date)。

## <a name="install-the-sdk-and-tools"></a>安装 SDK 和工具

Web 平台安装程序 (WebPI) 是安装 SDK 和工具的建议方法。 如果使用 WebPI 收到运行时错误，还可以在特定 Service Fabric 版本的发行说明中找到指向安装程序的直接链接。 发行说明可以在 [Service Fabric 团队博客](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)的各种发布公告中找到。

> [!NOTE]
> 不支持本地 Service Fabric 开发群集升级。

### <a name="to-use-visual-studio-2017-or-2019"></a>使用 Visual Studio 2017 或 2019

Service Fabric 工具是 Visual Studio 2019 和 2017 中 Azure 开发工作负荷的一部分。 在 Visual Studio 安装过程中启用此工作负荷。
此外还需使用 Web 平台安装程序安装 Microsoft Azure Service Fabric SDK 和运行时。

* [安装 Microsoft Azure Service Fabric SDK][core-sdk]

### <a name="sdk-installation-only"></a>仅安装 SDK

如果只需要 SDK，则安装此包：

* [安装 Microsoft Azure Service Fabric SDK][core-sdk]

当前版本包括：

* Service Fabric SDK 和 Tools 5.1.335
* Service Fabric 运行时 8.1.335

有关支持版本的列表，请参阅 [Service Fabric 版本](service-fabric-versions.md)

> [!NOTE]
> 应用程序或群集升级不支持单机群集 (OneBox)；如果需要执行群集升级，或在执行应用程序升级时遇到任何问题，请删除 OneBox 群集并重新创建它。 

## <a name="enable-powershell-script-execution"></a>启用 PowerShell 脚本执行

Service Fabric 使用 Windows PowerShell 脚本创建本地开发群集和部署 Visual Studio 中的应用程序。 默认情况下，Windows 会阻止这些脚本运行。 要启用它们，必须修改 PowerShell 执行策略。 以管理员身份打开 PowerShell 并输入以下命令：

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="install-docker-optional"></a>安装 Docker（可选）

[Service Fabric 是容器业务流程协调程序](service-fabric-containers-overview.md)，用于跨计算机群集部署微服务。 若要在本地开发群集上运行 Windows 容器应用程序，必须先安装用于 Windows 的 Docker。 获取[用于 Windows 的 Docker CE (稳定版)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description)。 安装并启动 Docker 以后，右键单击任务栏图标，并选择“切换到 Windows 容器”。  此步骤是运行基于 Windows 的 Docker 映像所必需的。

## <a name="next-steps"></a>后续步骤

完成设置开发环境之后，便可开始生成和运行应用。

* [了解如何创建、部署和管理应用程序](service-fabric-tutorial-create-dotnet-app.md)
* [了解编程模型：Reliable Services 和 Reliable Actors](service-fabric-choose-framework.md)
* [在 GitHub 上检查 Service Fabric 代码示例](/samples/browse/?products=azure)
* [使用 Service Fabric 资源管理器可视化群集](service-fabric-visualizing-your-cluster.md)
* [在 Windows 上准备 Linux 开发环境](service-fabric-local-linux-cluster-windows.md)
* 了解 [Service Fabric 支持选项](service-fabric-support.md)

[1]: https://azure.microsoft.com/campaigns/service-fabric/ "Service Fabric 活动页"
[2]: https://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI 链接"
[full-bundle-dev15]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI 链接"
[core-sdk]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI 链接"
[powershell5-download]:https://www.microsoft.com/download/details.aspx?id=54616
