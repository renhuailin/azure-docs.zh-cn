---
title: Azure 容器实例方案
titleSuffix: Azure Cognitive Services
description: 了解如何在 Azure 容器实例上部署认知服务容器
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, devx-track-azurecli
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: aahi
ms.openlocfilehash: d37b01750c1d5cd900378fcb5ea7f4d1ffb0d597
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108287241"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>在 Azure 容器实例上部署和运行容器

使用以下步骤，可以轻松地通过 Azure [容器实例](../../container-instances/index.yml)在云中缩放 Azure 认知服务应用程序。 容器化可帮助你集中精力构建应用程序，而不是管理基础结构。 有关使用容器的详细信息，请参阅[功能和优势](../cognitive-services-container-support.md#features-and-benefits)。

## <a name="prerequisites"></a>先决条件

此方案适用于任何认知服务容器。 使用此方案之前，必须先创建认知服务资源。 支持容器的每个认知服务都有一篇“如何安装”文章，其中介绍了如何为容器安装并配置服务。 某些服务需要一个文件或一组文件作为容器的输入。在使用此解决方案之前，请确保了解并已成功使用过容器。

* 你正在使用的 Azure 认知服务的 Azure 资源。
* 认知服务终结点 URL - 查看容器的特定服务的“如何安装”文章，以了解终结点 URL 在 Azure 门户内的位置，以及 URL 的正确示例的样式。 确切的格式可能因服务而异。
* 认知服务密钥 - 密钥位于 Azure 资源的“密钥”页面上。 您只需要两个密钥中的一个即可。 密钥是包含 32 个字母数字字符的字符串。

* 本地主机（你的计算机）上的单个认知服务容器。 确保你可以：
  * 使用 `docker pull` 命令拉取映像。
  * 使用 `docker run` 命令通过所有必需的配置设置成功运行本地容器。
  * 调用容器的终结点，获取 HTTP 2xx 响应和 JSON 响应。

尖括号 `<>` 中的所有变量都需要替换为你自己的值。 此替换包括尖括号。

> [!IMPORTANT]
> LUIS 容器需要在运行时中拉取的 `.gz` 模型文件。 容器必须能够通过容器实例中的卷装载来访问此模型文件。 若要上传模型文件，请执行以下步骤：
> 1. [创建 Azure 文件共享](../../storage/files/storage-how-to-create-file-share.md)。 记下 Azure 存储帐户名称、密钥和文件共享名称，因为稍后需要用到它们。
> 2. [从 LUIS 门户中导出你的 LUIS 模型（打包的应用）](../LUIS/luis-container-howto.md#export-packaged-app-from-luis)。 
> 3. 在 Azure 门户中，导航到你的存储帐户资源的“概述”页，然后选择“文件共享”。 
> 4. 选择你最近创建的文件共享名称，然后选择“上传”。 接着上传打包的应用。 

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

[!INCLUDE [Portal instructions for creating an ACI instance](includes/create-container-instances-resource.md)]

# <a name="cli"></a>[CLI](#tab/cli)

[!INCLUDE [CLI instructions for creating an ACI instance](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

---


## <a name="use-the-container-instance"></a>使用容器实例

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

1. 选择“概述”并复制 IP 地址。 它将是数字 IP 地址，例如 `55.55.55.55`。
1. 打开新的浏览器选项卡，并使用 IP 地址（例如 `http://<IP-address>:5000 (http://55.55.55.55:5000`）。 此时会显示容器的主页，告知你该容器处于运行状态。

    ![容器的主页](../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

1. 选择“服务 API 说明”以查看容器的 Swagger 页面。

1. 选择任意一个 POST API，然后选择“试用”。此时会显示参数，其中包括输入。 填写这些参数。

1. 选择“执行”以将请求发送到你的容器实例。

    你已成功地在 Azure 容器实例中创建并使用了认知服务容器。

# <a name="cli"></a>[CLI](#tab/cli)

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

> [!NOTE]
> 如果针对运行状况容器运行“文本分析”，请使用以下 URL 来提交查询：`http://localhost:5000/text/analytics/v3.2-preview.1/entities/health`

---
