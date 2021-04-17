---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 03/30/2021
ms.author: parkerra
ms.openlocfilehash: 7faab3340483d99fa276de06f3fd7787457edb9e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076681"
---
下一步是下载适用于 Unity 的 Azure 空间定位点包。 

> [!WARNING]
> ASA SDK 2.7.0 是支持的最低版本。 如果使用 Unity 2020，则 ASA SDK 2.9.0 是支持的最低版本。

若要在 Unity 中使用 Azure 空间定位点，你将需要下载核心包以及适用于你计划支持的每个平台 (Android/iOS/HoloLens) 的平台特定包。

| 平台 | 包名称                                    |
|----------|-------------------------------------------------|
| Android/iOS/HoloLens  | com.microsoft.azure.spatial-anchors-sdk.core@<version_number> |
| Android  | com.microsoft.azure.spatial-anchors-sdk.android@<version_number> |
| iOS      | com.microsoft.azure.spatial-anchors-sdk.ios@<version_number>     |
| HoloLens | com.microsoft.azure.spatial-anchors-sdk.windows@<version_number> |

# <a name="download-with-web-browser"></a>[使用 Web 浏览器下载](#tab/unity-package-web-ui)

在[此处](https://aka.ms/aoa/unity-sdk/package)查找适用于 Unity 的 Azure 空间定位点包。 选择所需的版本，并使用“下载”按钮下载该包。 重复此步骤，为你计划支持的每个平台下载包。

# <a name="download-with-npm"></a>[使用 NPM 下载](#tab/unity-package-npm)

此步骤要求 <a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a> 已安装并且可用。

运行以下命令，将 `<version_number>` 替换为要下载到当前文件夹的 Azure 空间定位点的版本：

```bash
npm pack com.microsoft.azure.spatial-anchors-sdk.core@<version_number> --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/
```

> [!NOTE]
> 要列出 Azure 空间定位点包的可用版本，请运行以下命令：
>
> ```bash
> npm view com.microsoft.azure.spatial-anchors-sdk.core --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/ versions
> ```

Azure 空间定位点核心包将下载到运行命令的文件夹。 重复此步骤，为你计划支持的每个平台下载包。

# <a name="install-with-mixed-reality-feature-tool-beta"></a>[使用混合现实功能工具（beta 版本）进行安装](#tab/unity-package-mixed-reality-feature-tool)

继续执行下一步。 在稍后的步骤中将使用<a a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">混合现实功能工具</a>。

---