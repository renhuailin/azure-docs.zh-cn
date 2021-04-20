---
title: 适用于运行状况容器的 docker pull
titleSuffix: Azure Cognitive Services
description: 文本分析的用于运行状况容器的 docker pull 命令
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/25/2021
ms.author: aahi
ms.openlocfilehash: 8595af7f46b63f9991d6a02279ccad76afb38311
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106089692"
---
填写并提交[认知服务请求表单](https://aka.ms/csgate)，请求访问健康状况文本分析公共预览版。  此表单可用于申请容器和托管 Web API 公共预览版。
通过该表单请求有关你、你的公司以及要使用该容器的用户方案的信息。 提交表单后，Azure 认知服务团队可以检查它，确保你满足访问专用容器注册表的条件。

> [!IMPORTANT]
> * 在此表单上，必须使用与 Azure 订阅 ID 关联的电子邮件地址。
> * 用于运行容器的文本分析资源（计费终结点和 apikey）必须已使用批准的 Azure 订阅 ID 创建。 
> * 请检查你的电子邮件（“收件箱”和“垃圾邮件”文件夹）以获取来自 Microsoft 的应用程序状态更新。
> * 此容器 URL 已更改，请参阅以下示例。 从 2021 年 4 月 26 日起，该容器将无法从 `containerpreview.azurecr.io` 下载。


获得批准后，请使用 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令从 Microsoft 公共容器注册表下载此容器映像。

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest
```
