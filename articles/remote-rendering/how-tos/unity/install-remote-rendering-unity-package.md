---
title: 安装 Unity 的远程渲染包
description: 说明如何安装适用于 Unity 的远程渲染客户端 DLL
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: bfb383a7079e98db1db1f9b5077558c187bcea96
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047722"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>安装 Unity 的远程渲染包

Azure 远程渲染使用 Unity 包将集成封装到 Unity 中。
此包中包含了整个 C# API，还有将 Azure 远程渲染与 Unity 配合使用所需的所有插件二进制文件。
按照 Unity 的包命名方案，此包被称为“com.microsoft.azure.remote-rendering”。

可以选择以下某一选项来安装 Unity 包。

## <a name="install-remote-rendering-package-using-the-mixed-reality-feature-tool"></a>使用混合现实功能工具安装远程渲染包

[混合现实功能工具](/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool)（[下载](https://aka.ms/mrfeaturetool)）是用于将混合现实功能包集成到 Unity 项目中的工具。 该包不属于 [ARR 示例存储库](https://github.com/Azure/azure-remote-rendering)，并且无法从 Unity 的内部包注册表中获得。

若要将该包添加到项目，需执行以下操作：
1. [下载混合现实功能工具](https://aka.ms/mrfeaturetool)
1. 按照有关如何使用该工具的[完整说明](/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool)进行操作。
1. 在“发现功能”页上，勾选“Microsoft Azure 远程渲染”包的框，并选择要添加到项目的包的版本 

![Mixed_Reality_feature_tool_package](media/mixed-reality-feature-tool-package.png)

若要更新本地包，请直接从混合现实功能工具中选择较新的版本并进行安装。 更新包有时可能会导致控制台错误。 如果出错，请尝试关闭再重新打开项目。

## <a name="install-remote-rendering-package-manually"></a>手动安装远程渲染包

若要手动安装远程渲染包，需要执行以下操作：

1. 从位于 `https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry` 的混合现实包 NPM 源下载该包。
    * 可以使用 [NPM](https://www.npmjs.com/get-npm) 并运行以下命令，以将该包下载到当前文件夹。
      ```
      npm pack com.microsoft.azure.remote-rendering --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry
      ```

    * 也可以使用 [azure-remote-rendering GitHub 存储库](https://github.com/Azure/azure-remote-rendering)中位于 `Scripts/DownloadUnityPackages.ps1` 的 PowerShell 脚本。
        * 将 `Scripts/unity_sample_dependencies.json` 的内容编辑为
          ```json
          {
            "packages": [
              {
                "name": "com.microsoft.azure.remote-rendering", 
                "version": "latest", 
                "registry": "https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry"
              }
            ]
          }
          ```

        * 在 PowerShell 中运行以下命令，以将该包下载到提供的目标目录。
          ```
          DownloadUnityPackages.ps1 -DownloadDestDir <destination directory>
          ```

1. 使用 Unity 的包管理器[安装已下载的包](https://docs.unity3d.com/Manual/upm-ui-tarball.html)。

若要更新本地包，只需重新运行所使用的相应命令并重新导入该包即可。 更新包有时可能会导致控制台错误。 如果出错，请尝试关闭再重新打开项目。

## <a name="unity-render-pipelines"></a>Unity 渲染管道

远程渲染适用于 :::no-loc text="Universal render pipeline"::: 和 :::no-loc text="Standard render pipeline"::: 。 出于性能原因，建议使用通用渲染管道。

若要使用 :::no-loc text="Universal render pipeline":::，它的包就必须安装在 Unity 中。 此操作可以在 Unity 的包管理器 UI 中完成（包名称为“通用 RP”，版本为 7.3.1 或更高），也可以通过 `Packages/manifest.json` 文件完成，如 [Unity 项目设置教程](../../tutorials/unity/view-remote-models/view-remote-models.md#include-the-azure-remote-rendering-package)所述 。

## <a name="next-steps"></a>后续步骤

* [Unity 游戏对象和组件](objects-components.md)
* [教程：查看远程模型](../../tutorials/unity/view-remote-models/view-remote-models.md)