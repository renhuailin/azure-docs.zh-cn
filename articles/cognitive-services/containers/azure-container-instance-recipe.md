---
title: Azure 容器实例食谱
titleSuffix: Azure Cognitive Services
description: 了解如何在 Azure 容器实例上部署认知服务容器
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: aahi
ms.openlocfilehash: 003b4411ac791898f4a7467b9b03f29aadba2fc7
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704822"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>在 Azure 容器实例上部署和运行容器

通过以下步骤，可以轻松地通过 Azure [容器实例](../../container-instances/index.yml)缩放云中的 Azure 认知服务应用程序。 容器化可帮助你专注于构建应用程序，而不是管理基础结构。 有关使用容器的详细信息，请参阅 [功能和优势](../cognitive-services-container-support.md#features-and-benefits)。

## <a name="prerequisites"></a>先决条件

食谱适用于任何认知服务容器。 使用食谱之前，必须先创建认知服务资源。 支持容器的每个认知服务都有一个 "如何安装" 一文，用于为容器安装和配置服务。 某些服务需要一个文件或一组文件作为容器的输入，在使用此解决方案之前，请务必了解并成功使用容器。

* 你正在使用的 Azure 认知服务的 Azure 资源。
* 认知服务 **终结点 URL** -查看容器的特定服务 "如何安装"，以查找 endpoint URL 在 Azure 门户中的位置，以及正确的 url 示例。 确切的格式可以从服务更改为服务。
* 认知服务 **密钥** -密钥位于 Azure 资源的 " **密钥** " 页上。 您只需要两个密钥中的一个即可。 此键是32字母数字字符的字符串。

* 本地主机上的单个认知服务容器 (计算机) 。 请确保可以：
  * 使用命令拉取映像 `docker pull` 。
  * 使用命令通过所有必需的配置设置成功运行本地容器 `docker run` 。
  * 调用容器的终结点，获取 HTTP 2xx 和 JSON 响应的响应。

尖括号中的所有变量都 `<>` 需要替换为自己的值。 此替换包括尖括号。

> [!IMPORTANT]
> LUIS 容器需要在运行时中拉取的 `.gz` 模型文件。 容器必须能够通过卷装入容器实例访问此模型文件。 若要上传模型文件，请执行以下步骤：
> 1. [创建 Azure 文件共享](../../storage/files/storage-how-to-create-file-share.md)。 记下 Azure 存储帐户名称、密钥和文件共享名称，因为稍后需要用到它们。
> 2. [将 LUIS 模型从 LUIS 门户导出 (封装应用) ](../LUIS/luis-container-howto.md#export-packaged-app-from-luis)。 
> 3. 在 Azure 门户中，导航到存储帐户资源的 " **概述** " 页，然后选择 " **文件共享**"。 
> 4. 选择最近创建的文件共享名称，然后选择 " **上传**"。 然后上传打包的应用。 

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

[!INCLUDE [Portal instructions for creating an ACI instance](includes/create-container-instances-resource.md)]

# <a name="cli"></a>[CLI](#tab/cli)

[!INCLUDE [CLI instructions for creating an ACI instance](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

---


## <a name="use-the-container-instance"></a>使用容器实例

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

1. 选择 " **概述** " 并复制 "IP 地址"。 它将是数字 IP 地址，例如 `55.55.55.55` 。
1. 打开新的浏览器选项卡，并使用 IP 地址（例如 `http://<IP-address>:5000 (http://55.55.55.55:5000`）。 你将看到容器的主页，让你知道容器正在运行。

    ![容器的主页](../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

1. 选择 " **服务 API 说明** " 以查看容器的 swagger 页面。

1. 选择任何 **POST** api，并选择 " **试用**"。 将显示参数，包括输入。 填写参数。

1. 选择 " **执行** " 将请求发送到容器实例。

    已成功在 Azure 容器实例中创建并使用了认知服务容器。

# <a name="cli"></a>[CLI](#tab/cli)

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

> [!NOTE]
> 如果运行的是运行状况容器的文本分析，请使用以下 URL 提交查询： `http://localhost:5000/text/analytics/v3.2-preview.1/entities/health`

---
