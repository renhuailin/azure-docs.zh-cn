---
title: 数据丢失防护
description: 认知服务数据丢失防护功能可让客户配置其认知服务资源有权访问的出站 URL 列表。 此配置将为客户创建另一个控制级别来防止数据丢失。
author: gclarkmt
ms.author: gregc
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/02/2021
ms.custom: template-concept
ms.openlocfilehash: c9a5ac391607249393d362f0e0b5b007989ea138
ms.sourcegitcommit: 9caa850a2b26773e238f8ba6f4ca151c47260915
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2021
ms.locfileid: "113601375"
---
# <a name="configure-data-loss-prevention-for-azure-cognitive-services"></a>配置 Azure 认知服务的数据丢失防护

认知服务数据丢失防护功能可让客户配置其认知服务资源有权访问的出站 URL 列表。 此配置将为客户创建另一个控制级别来防止数据丢失。 本文介绍为认知服务资源启用数据丢失防护功能所要执行的步骤。

## <a name="prerequisites"></a>先决条件

在发出请求之前，需要具有 Azure 帐户和 Azure 认知服务订阅。 如果已有帐户，请继续并跳到下一节。 如果还没有帐户，我们会提供指南，可在几分钟内完成设置：[创建 Azure 认知服务帐户](cognitive-services-apis-create-account.md)。

[创建帐户](https://azure.microsoft.com/free/cognitive-services/)后，可以从 [Azure 门户](cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)获取订阅密钥。

## <a name="enabling-data-loss-prevention"></a>启用数据丢失防护

启用数据丢失防护需要执行两个步骤。 首先，必须将属性 restrictOutboundNetworkAccess 设置为 true。 将此属性设置为 true 后，还需要提供已批准的 URL 列表。 该 URL 列表将添加到 allowedFqdnList 属性。 allowedFqdnList 属性包含逗号分隔的 URL 的数组。

>[!NOTE]
>
> * `allowedFqdnList` 属性值最多支持 1000 个 URL。
> * 该属性既支持 IP 地址，也支持完全限定的域名，即 `www.microsoft.com` 值。
> * 最长可能需要经过 15 分钟，更新的列表才会生效。 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 安装 [Azure CLI](/cli/azure/install-azure-cli) 并[登录](/cli/azure/authenticate-azure-cli)，或选择“试用”。

1. 查看认知服务资源的详细信息。

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
    ```

1. 查看认知服务资源的当前属性。

    ```azurecli-interactive
    az rest -m get \
        -u /subscriptions/{subscription ID}}/resourceGroups/{resource group}/providers/Microsoft.CognitiveServices/accounts/{account name}?api-version=2021-04-30 \
    ```

1. 配置 restrictOutboundNetworkAccess 属性，并使用已批准的 URL 更新允许的 FqdnList

    ```azurecli-interactive
    az rest -m patch \
        -u /subscriptions/{subscription ID}}/resourceGroups/{resource group}/providers/Microsoft.CognitiveServices/accounts/{account name}?api-version=2021-04-30 \
        -b '{"properties": { "restrictOutboundNetworkAccess": true, "allowedFqdnList": [ "microsoft.com" ] }}'
    ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. 安装 [Azure PowerShell](/powershell/azure/install-az-ps) 并[登录](/powershell/azure/authenticate-azureps)，或选择“试用”。

1. 显示认知服务资源的当前属性。

    ```azurepowershell-interactive
    $getParams = @{
        ResourceGroupName = 'myresourcegroup'
        ResourceProviderName = 'Microsoft.CognitiveServices'
        ResourceType = 'accounts'
        Name = 'myaccount'
        ApiVersion = '2021-04-30'
        Method = 'GET'
    }
    Invoke-AzRestMethod @getParams
    ```

1. 配置 restrictOutboundNetworkAccess 属性，并使用已批准的 URL 更新允许的 FqdnList

    ```azurepowershell-interactive
    $patchParams = @{
        ResourceGroupName = 'myresourcegroup'
        ResourceProviderName = 'Microsoft.CognitiveServices'
        ResourceType = 'accounts'
        Name = 'myaccount'
        ApiVersion = '2021-04-30'
        Payload = '{"properties": { "restrictOutboundNetworkAccess": true, "allowedFqdnList": [ "microsoft.com" ] }}'
        Method = 'PATCH'
    }
    Invoke-AzRestMethod @patchParams
    ```

---

## <a name="supported-services"></a>支持的服务

以下服务支持数据丢失防护配置：

- 计算机视觉
- 内容审查器
- 自定义视觉
- 人脸
- 表单识别器
- 语音服务
- QnA Maker

## <a name="next-steps"></a>后续步骤

- [配置虚拟网络](cognitive-services-virtual-networks.md)
