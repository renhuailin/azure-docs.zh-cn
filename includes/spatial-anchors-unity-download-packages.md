---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 2/3/21
ms.author: parkerra
ms.openlocfilehash: f6c2780ccbb914228a9870cb3b5fe4b0e3d0b214
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99569576"
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

Azure 空间定位点核心包将下载到运行命令的文件夹。

重复此步骤，为你要在项目中支持的每个平台 (Android/iOS/HoloLens) 下载包。

| 平台 | 包名称                                    |
|----------|-------------------------------------------------|
| Android  | com.microsoft.azure.spatial-anchors-sdk.android@<version_number> |
| iOS      | com.microsoft.azure.spatial-anchors-sdk.ios@<version_number>     |
| HoloLens | com.microsoft.azure.spatial-anchors-sdk.windows@<version_number> |