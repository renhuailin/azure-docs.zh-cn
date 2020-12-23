---
title: 异常探测器环境变量
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/30/2020
ms.author: mbullwin
ms.openlocfilehash: 5148114da7fcc654b1f0af431440ce74c7bc8d31
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371777"
---
### <a name="create-an-environment-variable"></a>创建环境变量

>[!NOTE]
> 在 2019 年 7 月 1 日之后创建的非试用资源的终结点使用如下所示的自定义子域格式。 有关详细信息和区域终结点的完整列表，请参阅[认知服务的自定义子域名](../../cognitive-services-custom-subdomains.md)。 

从创建的资源使用密钥和终结点，创建两个用于身份验证的环境变量：

* `ANOMALY_DETECTOR_KEY` - 用于验证请求的资源密钥。
* `ANOMALY_DETECTOR_ENDPOINT` - 用于发送 API 请求的资源终结点。 它将如下所示： 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

使用操作系统的说明。

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx ANOMALY_DETECTOR_KEY <replace-with-your-anomaly-detector-key>
setx ANOMALY_DETECTOR_ENDPOINT <replace-with-your-anomaly-detector-endpoint>
```

添加环境变量后，请重启控制台窗口。

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

添加环境变量后，请从控制台窗口运行 `source ~/.bashrc`，使更改生效。

#### <a name="macos"></a>[macOS](#tab/unix)

编辑 `.bash_profile`，然后添加环境变量：

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

添加环境变量后，请从控制台窗口运行 `source .bash_profile`，使更改生效。

***