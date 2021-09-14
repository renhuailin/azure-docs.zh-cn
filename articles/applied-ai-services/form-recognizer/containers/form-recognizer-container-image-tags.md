---
title: 表单识别器映像标记和发行说明
titleSuffix: Azure Applied AI Services
description: 所有表单识别器容器映像标记的列表。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: reference
ms.date: 09/02/2021
ms.author: lajanuar
keywords: Dcoker，容器，映像
ms.openlocfilehash: d38fd74324246463e9f74ff8d53c67ccb2ccd039
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123481387"
---
# <a name="form-recognizer-container-image-tags-and-release-notes"></a>表单识别器容器映像标记和发行说明

> [!IMPORTANT]
>
> * 表单识别器 v2.1 容器为封闭预览版。 若要使用这些容器，必须提交[在线请求](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu)并获得批准。 
>
> * 联机请求表单要求提供一个有效的电子邮件地址，该地址属于拥有 Azure 订阅 ID 的组织，并且用户需要具有或已获得对该订阅的访问权限。

## <a name="feature-containers"></a>功能容器

表单识别器功能由七个容器支持：

| 容器名称 | 完全限定的映像名称 |
|---|---|
| **布局** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout |
| **名片** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/businesscard |
| **ID 文档** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/id-document |
| **回执** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/receipt |
| **发票** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/invoice |
| **自定义 API** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/custom-api |
| **自定义监督** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/custom-supervised |

## <a name="microsoft-container-registry"></a>Microsoft 容器注册表

在 Microsoft Container Registry mcr.microsoft 中可以找到表单识别器容器映像。 <span></span>com 容器注册表联合，它是所有 Microsoft 发布的 Docker 映像的主要注册表。

* MCR 的发现体验通过 [Docker Hub](https://hub.docker.com/publishers/microsoftowner) 提供。

* 还可以查询 [mcr 内的存储库列表](https://mcr.microsoft.com/v2/_catalog)。

## <a name="form-recognizer-tags"></a>表单识别器标记

以下标记可用于表单识别器：

### <a name="latest-version"></a>[最新版本](#tab/current)

`v2.1`（封闭预览版）的发行说明：

| 容器 | Tags |
|------------|:------|
| **布局**| &bullet; `latest` </br> &bullet; `2.1-preview` </br> &bullet; `2.1.0.016140001-08108749-amd64-preview`|
| **名片** | &bullet; `latest` </br> &bullet; `2.1-preview` </br> &bullet; `2.1.016190001-amd64-preview`  </br> &bullet; `2.1.016320001-amd64-preview`  |
| **ID 文档** | &bullet; `latest` </br> &bullet; `2.1-preview`</br>&bullet; `2.1.016190001-amd64-preview`</br>&bullet; `2.1.016320001-amd64-preview` |
| **回执**| &bullet; `latest` </br> &bullet; `2.1-preview`</br>&bullet; `2.1.016190001-amd64-preview`</br>&bullet; `2.1.016320001-amd64-preview` |
| **发票**| &bullet; `latest` </br> &bullet; `2.1-preview`</br>&bullet; `2.1.016190001-amd64-preview`</br>&bullet; `2.1.016320001-amd64-preview` |
| **自定义 API** | &bullet; `latest` </br> &bullet;`2.1-distroless-20210622013115034-0cc5fcf6`</br>&bullet; `2.1-preview`|
| **自定义监督**| &bullet; `latest` </br> &bullet; `2.1-distroless-20210622013149174-0cc5fcf6`</br>&bullet; `2.1-preview`|

### <a name="previous-versions"></a>[旧版](#tab/previous)

> [!IMPORTANT]
> 表单识别器 v1.0 容器已停用。

---

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [安装和运行表单识别器容器](form-recognizer-container-install-run.md)
>