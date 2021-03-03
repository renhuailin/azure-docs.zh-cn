---
title: 安装 Unity 的远程渲染包
description: 说明如何安装适用于 Unity 的远程呈现客户端 Dll
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 9454bef52798650fc431f8df994e1a964662b453
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720812"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>安装 Unity 的远程渲染包

Azure 远程呈现使用 Unity 包将集成封装到 Unity。
此包包含整个 c # API 以及通过 Unity 使用 Azure 远程呈现所需的所有插件二进制文件。
对于包，遵循 Unity 的命名方案，包称为 " **.com**"。

你可以选择以下选项之一来安装 Unity 包。

## <a name="install-remote-rendering-package-using-the-mixed-reality-feature-tool"></a>使用混合现实功能工具安装远程呈现包

 ([下载](https://aka.ms/mrfeaturetool)) [混合现实功能工具](https://aka.ms/MRFeatureToolDocs)，它是一种用于将混合现实功能包集成到 Unity 项目中的工具。 该包不是 [ARR 示例存储库](https://github.com/Azure/azure-remote-rendering)的一部分，并且无法从 Unity 的内部包注册表中获得。

若要将包添加到项目中，需要执行以下操作：
1. [下载 Mixed Reality 功能工具](https://aka.ms/mrfeaturetool)
1. 请按照有关如何使用该工具的 [完整说明](https://aka.ms/MRFeatureToolDocs) 进行操作。
1. 在 " **发现功能** " 页上，勾选 **Microsoft Azure 远程呈现** 包的框，并选择要添加到项目的包版本

![Mixed_Reality_feature_tool_package](media/mixed-reality-feature-tool-package.png)

若要更新本地包，只需从混合现实功能工具中选择一个较新版本，然后进行安装。 更新包有时可能会导致控制台错误。 如果出错，请尝试关闭再重新打开项目。

## <a name="install-remote-rendering-package-manually"></a>手动安装远程呈现包

若要手动安装远程呈现包，需要执行以下操作：

1. 从中的混合现实包 NPM 源下载包 `https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry` 。
    * 可以使用 [NPM](https://www.npmjs.com/get-npm) 并运行以下命令，将包下载到当前文件夹。
      ```
      npm pack com.microsoft.azure.remote-rendering --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry
      ```

    * 或者，你可以 `Scripts/DownloadUnityPackages.ps1` 从 [azure 远程呈现 GitHub 存储库](https://github.com/Azure/azure-remote-rendering)中使用 PowerShell 脚本。
        * 编辑内容 `Scripts/unity_sample_dependencies.json`
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

        * 在 PowerShell 中运行以下命令，将包下载到提供的目标目录。
          ```
          DownloadUnityPackages.ps1 -DownloadDestDir <destination directory>
          ```

1. [安装下载的包](https://docs.unity3d.com/Manual/upm-ui-tarball.html) 与 Unity 的包管理器。

若要更新本地包，只需重新运行所用的相应命令，然后重新导入该包。 更新包有时可能会导致控制台错误。 如果出错，请尝试关闭再重新打开项目。

## <a name="unity-render-pipelines"></a>Unity 呈现管道

远程呈现适用于 **:::no-loc text="Universal render pipeline":::** 和 **:::no-loc text="Standard render pipeline":::** 。 出于性能原因，建议使用通用呈现管道。

若要使用 **:::no-loc text="Universal render pipeline":::** ，其包必须安装在 Unity 中。 这可以在 Unity 的 **包管理器** UI 中完成， (包名称 **通用 RP**、版本7.3.1 或更新的) 或 `Packages/manifest.json` 文件，如 [Unity 项目设置教程](../../tutorials/unity/view-remote-models/view-remote-models.md#include-the-azure-remote-rendering-package)中所述。

## <a name="next-steps"></a>后续步骤

* [Unity 游戏对象和组件](objects-components.md)
* [教程：查看远程模型](../../tutorials/unity/view-remote-models/view-remote-models.md)
