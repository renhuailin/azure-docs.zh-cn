---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 06/10/2021
ms.author: crtreasu
ms.openlocfilehash: f358fc0d82fe28dd06a0102afcfde7ff29fd9ffe
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124796969"
---
下一步是下载适用于 Unity 的 Azure Object Anchors 包。

# <a name="download-with-web-browser"></a>[使用 Web 浏览器下载](#tab/unity-package-web-ui)

在[此处](https://aka.ms/aoa/unity-sdk/package)找到适用于 Unity (`com.microsoft.azure.object-anchors.runtime`) 的 Azure Object Anchors 包。 选择所需的版本，并使用“下载”按钮下载该包。

# <a name="download-with-npm"></a>[使用 NPM 下载](#tab/unity-package-npm)

此步骤要求 <a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a> 已安装并且可用。

运行以下命令（将 `<version_number>` 替换为要下载的 Azure Object Anchors 的版本）：

```bash
npm pack com.microsoft.azure.object-anchors.runtime@<version_number> --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/
```

> [!NOTE]
> 若要列出 Azure Object Anchors 包的可用版本，请运行以下命令：
>
> ```bash
> npm view com.microsoft.azure.object-anchors.runtime --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/ versions
> ```

Azure Object Anchors 包将下载到运行该命令的文件夹。

# <a name="install-with-mixed-reality-feature-tool-beta"></a>[使用混合现实功能工具（beta 版本）进行安装](#tab/unity-package-mixed-reality-feature-tool)

继续执行下一步。 在稍后的步骤中将使用<a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">混合现实功能工具</a>。

---