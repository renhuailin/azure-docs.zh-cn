---
title: 将本机 C++ OpenXR 教程部署到 HoloLens
description: 介绍如何在 HoloLens 上运行本机 C++ OpenXR 教程的快速入门
author: florianborn71
ms.author: flborn
ms.date: 07/01/2021
ms.topic: quickstart
ms.openlocfilehash: 41049aa7c4bd4b5e1ea5dedb3da7578f37a05d55
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113667560"
---
# <a name="quickstart-deploy-native-c-openxr-sample-to-hololens"></a>快速入门：将原生 C++ OpenXR 示例部署到 HoloLens

本快速入门介绍如何在 HoloLens 2 上部署和运行基于 OpenXR 的本机 C++ 教程应用程序。

本快速入门将介绍以下操作：

> [!div class="checklist"]
>
>* 为 HoloLens 构建教程应用程序。
>* 更改源代码中的 ARR 凭据。
>* 在设备上部署并运行示例。

## <a name="prerequisites"></a>先决条件

若要获取对 Azure 远程渲染服务的访问权限，首先需要[创建一个帐户](../../../how-tos/create-an-account.md)。

必须安装以下软件：

* Windows SDK 10.0.18362.0[（下载）](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Visual Studio 2019 最新版本[（下载）](https://visualstudio.microsoft.com/vs/older-downloads/)
* [适用于混合现实的 Visual Studio Tools](/windows/mixed-reality/install-the-tools)。 具体来说，必须安装以下工作负载：
  * **使用 C++ 的桌面开发**
  * **通用 Windows 平台 (UWP) 开发**
* GIT[（下载）](https://git-scm.com/downloads)。

## <a name="clone-the-arr-samples-repository"></a>克隆 ARR 示例存储库

第一步是克隆 Git 存储库，其中包含全局 Azure 远程渲染示例。 打开命令提示符（在 Windows 开始菜单中键入 `cmd`），并切换到要在其中存储 ARR 示例项目的目录。

运行以下命令：

```cmd
mkdir ARR
cd ARR
git clone https://github.com/Azure/azure-remote-rendering
```

最后一个命令会在 ARR 目录中创建一个子目录，其中包含 Azure 远程渲染的各个示例项目。

C++ HoloLens 教程位于子目录 NativeCpp/HoloLens-OpenXr 中。

## <a name="build-the-project"></a>生成项目

使用 Visual Studio 2019 打开位于 NativeCpp/HoloLens-OpenXr 子目录中的解决方案文件 BasicXrApp.sln 。

将生成配置切换为“调试”（或“发布”）和“ARM64”  。 此外，还要确保将调试程序模式设置为“设备”，而不是“远程计算机” ：

![Visual Studio 配置](media/vs-config-native-cpp-tutorial.png)

由于帐户凭据在教程的源代码中进行了硬编码，因此请将其更改为有效凭据。 为此，请在 Visual Studio 中打开文件 `OpenXrProgram.cpp` 并更改函数 `InitARR()` 中创建客户端的部分：

```cpp
// 2. Create Client
{
    // Users need to fill out the following with their account data and model
    RR::SessionConfiguration init;
    init.AccountId = "00000000-0000-0000-0000-000000000000";
    init.AccountKey = "<account key>";
    init.RemoteRenderingDomain = "westus2.mixedreality.azure.com"; // <change to the region that the rendering session should be created in>
    init.AccountDomain = "westus2.mixedreality.azure.com"; // <change to the region the account was created in>
    m_modelURI = "builtin://Engine";
    m_sessionOverride = ""; // If there is a valid session ID to re-use, put it here. Otherwise a new one is created
    m_client = RR::ApiHandle(RR::RemoteRenderingClient(init));
}
```

具体而言，请更改以下值：
* 更改 `init.AccountId``init.AccountKey` 和 `init.AccountDomain` 以使用帐户数据。 请参阅有关如何[检索帐户信息](../../../how-tos/create-an-account.md#retrieve-the-account-information)的段落。
* 对于 `westus2` 以外的其他[区域](../../../reference/regions.md)（例如 `"westeurope.mixedreality.azure.com"`），修改 `init.RemoteRenderingDomain` 字符串的区域部分来指定在何处创建远程渲染会话。
* 此外，可以将 `m_sessionOverride` 更改为现有会话 ID。 可在此示例外部创建会话，例如通过使用 [PowerShell 脚本](../../../samples/powershell-example-scripts.md#script-renderingsessionps1)或直接使用[会话 REST API](../../../how-tos/session-rest-api.md)。
当示例应多次运行时，建议在示例外部创建会话。 如果未传入任何会话，则该示例将在每次启动时创建一个新会话，这可能需花费几分钟的时间。

现在可以编译该应用程序了。

## <a name="launch-the-application"></a>启动应用程序

1. 使用 USB 数据线将 HoloLens 连接到电脑。
1. 打开 HoloLens 并等待，直到显示开始菜单。
1. 在 Visual Studio 中启动调试器 (F5)。 此时会自动将该应用部署到设备。

随即系统应会启动示例应用，并显示一个文本面板，通知你当前的应用程序状态。 启动时的状态为“正在启动新会话”或“正在连接到现有会话”。 模型加载完成后，内置引擎模型会直接显示在头位置。 在遮挡方面，引擎模型与本地呈现的旋转多维数据集正确交互。

如果以后想要再次启动该示例，也可以在 HoloLens 开始菜单中找到它，但请注意，其中可能编译了一个过期的会话 ID。

## <a name="next-steps"></a>后续步骤

使用 ARR 功能成功编译和部署本机 OpenXR 示例后，可以通过为远程对象添加交互代码来开始增强示例：

> [!div class="nextstepaction"]
> [概念：实体](../../../concepts/entities.md)

此外，可以注入自己的转换后的模型：

> [!div class="nextstepaction"]
> [快速入门：转换模型](../../../quickstarts/convert-model.md)
