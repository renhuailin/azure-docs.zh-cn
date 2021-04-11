---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 03/18/2021
ms.author: parkerra
ms.openlocfilehash: d91c0aeda2b7ae2f133d2099cbc9d238fd19d287
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "104719703"
---
若要下载所需的包，你需要安装 <a href="https://www.npmjs.com/get-npm" target="_blank">NPM</a>。

运行以下命令，将 `<version_number>` 替换为要下载到当前文件夹的 Azure 空间定位点的版本：

```bash
npm pack com.microsoft.azure.spatial-anchors-sdk.core@<version_number> --registry https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM
```

> [!NOTE]
> 要列出 Azure 空间定位点包的可用版本，请运行以下命令：
>
> ```bash
> npm view com.microsoft.azure.spatial-anchors-sdk.core --registry https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM versions
> ```

> [!WARNING]
> ASA SDK 2.7.0 是支持的最低版本。 如果使用 Unity 2020，则 ASA SDK 2.9.0 是支持的最低版本。

Azure 空间定位点核心包将下载到运行命令的文件夹。

重复此步骤，为你要在项目中支持的每个平台 (Android/iOS/HoloLens) 下载包。

| 平台 | 包名称                                    |
|----------|-------------------------------------------------|
| Android  | com.microsoft.azure.spatial-anchors-sdk.android@<version_number> |
| iOS      | com.microsoft.azure.spatial-anchors-sdk.ios@<version_number>     |
| HoloLens | com.microsoft.azure.spatial-anchors-sdk.windows@<version_number> |