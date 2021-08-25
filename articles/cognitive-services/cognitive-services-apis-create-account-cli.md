---
title: 使用 Azure CLI 创建认知服务资源
titleSuffix: Azure Cognitive Services
description: 通过使用 Azure 命令行界面创建和订阅资源，开始使用 Azure 认知服务。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
keywords: 认知服务, 认知智能, 认知解决方案, ai 服务
ms.topic: quickstart
ms.date: 06/04/2021
ms.author: aahi
ms.openlocfilehash: 1c838a9b805eed20fec9f9feabf4aa24c58b1cd5
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429930"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>快速入门：使用 Azure 命令行接口 (CLI) 创建认知服务资源

使用本快速入门可通过 [Azure 命令行接口 (CLI)](/cli/azure/install-azure-cli) 开始使用 Azure 认知服务。

Azure 认知服务是包含 REST API 和客户端库 SDK 的云服务，可帮助开发人员将认知智能内置于应用程序，而无需具备直接的人工智能 (AI) 或数据科学技能或知识。 借助 Azure 认知服务，开发人员可以通过能够看、听、说、理解甚至开始推理的认知解决方案，轻松将认知功能添加到他们的应用程序中。

认知服务由你在 Azure 订阅中创建的 Azure [资源](../azure-resource-manager/management/manage-resources-portal.md)表示。 创建资源后，请使用生成的密钥和终结点对应用程序进行身份验证。

本快速入门介绍如何使用 [Azure 命令行接口 (CLI)](/cli/azure/install-azure-cli) 注册 Azure 认知服务以及创建包含单服务或多服务订阅的帐户。 这些服务由 Azure [资源](../azure-resource-manager/management/manage-resources-portal.md)表示，可用于连接到一个或多个 Azure 认知服务 API。

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>先决条件

* 一个有效的 Azure 订阅 - 免费[创建订阅](https://azure.microsoft.com/free/cognitive-services)。
* [Azure 命令行接口 (CLI)](/cli/azure/install-azure-cli)
* [!INCLUDE [contributor-requirement](./includes/quickstarts/contributor-requirement.md)]
* [!INCLUDE [terms-azure-portal](./includes/quickstarts/terms-azure-portal.md)]

## <a name="install-the-azure-cli-and-sign-in"></a>安装 Azure CLI 并登录

安装 [Azure CLI](/cli/azure/install-azure-cli)。 若要登录到本地安装的 CLI，请运行 [az login](/cli/azure/reference-index#az_login) 命令：

```azurecli-interactive
az login
```

也可以使用绿色的“尝试”按钮在浏览器中运行这些命令。

## <a name="create-a-new-azure-cognitive-services-resource-group"></a>创建新的 Azure 认知服务资源组

在创建认知服务资源之前，必须具有 Azure 资源组才能包含该资源。 在创建新资源时，可以选择新建资源组，或使用现有的资源组。 本文介绍如何创建新资源组。

### <a name="choose-your-resource-group-location"></a>选择资源组位置

若要创建资源，需要为订阅提供一个可用的 Azure 位置。 可以使用 [az account list-locations](/cli/azure/account#az_account_list_locations) 命令检索可用位置的列表。 可以从多个位置访问大部分认知服务。 选择离你最近的位置，或查看哪些位置可供服务使用。

> [!IMPORTANT]
> * 请记住选择的 Azure 位置，因为在调用 Azure 认知服务时需要用到。
> * 某些认知服务的可用性因区域而异。 有关详细信息，请参阅 [Azure 产品在各区域中的推出情况](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)。

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

选择 Azure 位置后，在 Azure CLI 中使用 [az group create](/cli/azure/group#az_group_create) 命令创建新的资源组。

在以下示例中，请将 Azure 位置 `westus2` 替换为你的订阅可用的某个 Azure 位置。

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>创建认知服务资源

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>选择认知服务和定价层

创建新资源时，需要知道你要使用哪种服务，以及所需的[定价层](https://azure.microsoft.com/pricing/details/cognitive-services/)（或 SKU）。 创建资源时，需要使用此信息和其他信息作为参数。

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]


[!INCLUDE [SKUs and pricing](./includes/quickstarts/sku-pricing.md)]

可以使用 [az cognitiveservices account list-kinds](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_list_kinds) 命令查找可用认知服务“种类”的列表：

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>将新资源添加到资源组

若要创建并订阅新的认知服务资源，请使用 [az cognitiveservices account create](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_create) 命令。 此命令会将新的可计费资源添加到前面创建的资源组。 创建新资源时，需要知道你要使用哪种服务，及其定价层（或 SKU）和 Azure 位置：

可以使用以下命令为异常检测器创建名为 `anomaly-detector-resource` 的 F0（免费）资源。

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>获取资源的密钥

若要登录到本地安装的命令行接口 (CLI)，请使用 [az login](/cli/azure/reference-index#az_login) 命令。

```azurecli-interactive
az login
```

使用 [az cognitiveservices account keys list](/cli/azure/cognitiveservices/account/keys#az_cognitiveservices_account_keys_list) 命令获取认知服务资源的密钥。

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>定价层和计费

定价层（以及你收到的账单金额）基于你使用身份验证信息发送的事务数。 每个定价层指定：
* 每秒允许的最大事务数 (TPS)。
* 在定价层中启用的服务功能。
* 预定义数目的事务的成本。 超过此金额将产生[定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)中为服务指定的额外费用。

## <a name="get-current-quota-usage-for-your-resource"></a>获取资源的当前配额使用情况

使用 [az cognitiveservices account list-usage](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_list_usage) 命令获取认知服务资源的使用情况。

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务资源，可以删除资源或资源组。 删除资源组也会删除该组中包含的任何其他资源。

若要删除资源组及其关联的资源，请使用 az group delete 命令。

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

如果需要恢复已删除的资源，请参阅[恢复已删除的认知服务资源](manage-resources.md)。

## <a name="see-also"></a>另请参阅

* 有关如何安全地使用认知服务的说明，请参阅 **[对 Azure 认知服务的请求进行身份验证](authentication.md)** 。
* 请参阅 **[什么是 Azure 认知服务？](./what-are-cognitive-services.md)** ，以获取认知服务中不同类别的列表。
* 若要查看认知服务支持的自然语言列表，请参阅 **[自然语言支持](language-support.md)** 。
* 请参阅 **[使用认知服务作为容器](cognitive-services-container-support.md)** 以了解如何使用本地认知服务。
* 请参阅 **[计划和管理认知服务的成本](plan-manage-costs.md)** ，以估计使用认知服务的成本。
