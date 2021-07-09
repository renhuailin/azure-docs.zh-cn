---
title: 如何将专用终结点与语音服务结合使用
titleSuffix: Azure Cognitive Services
description: 了解如何将语音服务与 Azure 专用链接提供的专用终结点结合使用
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: alexeyo
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d5de7ed4536ce7c83de4cc1e9a2d886015188e20
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2021
ms.locfileid: "110695342"
---
# <a name="use-speech-service-through-a-private-endpoint"></a>通过专用终结点使用语音服务

使用 [Azure 专用链接](../../private-link/private-link-overview.md)，可以通过[专用终结点](../../private-link/private-endpoint-overview.md)连接到 Azure 中的服务。 专用终结点是只能在特定[虚拟网络](../../virtual-network/virtual-networks-overview.md)和子网中访问的专用 IP 地址。

本文说明如何在 Azure 认知服务中通过语音服务设置和使用专用链接和专用终结点。
本文还介绍了如何在以后删除专用终结点，但仍使用语音资源。

> [!NOTE]
> 在继续操作之前，请查看[如何将虚拟网络与认知服务结合使用](../cognitive-services-virtual-networks.md)。



为专用终结点方案设置语音资源需要执行以下任务：
1. [创建自定义域名](#create-a-custom-domain-name)
1. [启用专用终结点](#turn-on-private-endpoints)
1. [调整现有应用程序和解决方案](#adjust-an-application-to-use-a-speech-resource-with-a-private-endpoint)

[!INCLUDE [](includes/speech-vnet-service-enpoints-private-endpoints.md)]

本文介绍如何将专用终结点与语音服务一起使用。 [此处](speech-service-vnet-service-endpoint.md)说明了 VNet 服务终结点的用法。


## <a name="create-a-custom-domain-name"></a>创建自定义域名

专用终结点需要[将自定义子域名称用于认知服务](../cognitive-services-custom-subdomains.md)。 使用以下说明可为语音资源创建一个专用终结点。

> [!WARNING]
> 启用了自定义域名的语音资源使用不同的方法与语音服务交互。 可能必须为以下两种方案调整应用程序代码：[使用专用终结点](#adjust-an-application-to-use-a-speech-resource-with-a-private-endpoint)以及[不使用专用终结点](#adjust-an-application-to-use-a-speech-resource-without-private-endpoints)。
>
> 启用自定义域名时，此操作[不可逆](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name)。 恢复为[区域名称](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)的唯一方法是创建新语音资源。
>
> 如果语音资源具有大量通过 [Speech Studio](https://speech.microsoft.com/) 创建的关联自定义模型和项目，则强烈建议在修改生产中使用的资源之前，使用测试资源尝试配置。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要使用 Azure 门户创建自定义域名，请遵循以下步骤：

1. 转到 [Azure 门户](https://portal.azure.com/)并登录 Azure 帐户。
1. 选择所需语音资源。
1. 在左窗格的“资源管理”组中，选择“网络” 。
1. 在“防火墙和虚拟网络”选项卡上，选择“生成自定义域名” 。 一个新的右面板随即出现，其中包含有关为资源创建唯一自定义子域的说明。
1. 在“生成自定义域名”面板中，输入自定义域名。 完整自定义域会如下所示：`https://{your custom name}.cognitiveservices.azure.com`。 
    
    请记住，创建自定义域名后，无法更改域名。
    
    输入自定义域名后，请选择“保存”。
1. 操作完成后，在“资源管理”组中，选择“密钥和终结点” 。 确认资源的新终结点名称以这种方式启动：`https://{your custom name}.cognitiveservices.azure.com`。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 创建自定义域名，请确认计算机具有 PowerShell 版本 7.x 或更高版本，其中包含 Azure PowerShell 模块版本 5.1.0 或更高版本。 若要查看这些工具的版本，请执行以下步骤：

1. 在 PowerShell 窗口中，输入：

    `$PSVersionTable`

    确认 `PSVersion` 值为 7.x 或更高。 若要升级 PowerShell，请按照[安装各种版本的 PowerShell](/powershell/scripting/install/installing-powershell) 中的说明进行操作。

1. 在 PowerShell 窗口中，输入：

    `Get-Module -ListAvailable Az`

    如果未显示任何内容，或者 Azure PowerShell 模块的版本低于 5.1.0，请按照[安装 Azure PowerShell 模块](/powershell/azure/install-Az-ps)中的说明进行升级。

继续操作之前，运行 `Connect-AzAccount` 以创建与 Azure 的连接。

## <a name="verify-that-a-custom-domain-name-is-available"></a>验证自定义域名是否可用

检查要使用的自定义域是否可用。 以下代码使用认知服务 REST API 中的[检查域可用性](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability)操作来确认域可用。

> [!TIP]
> 在 Azure Cloud Shell 中，以下代码不起作用。

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
Set-AzContext -SubscriptionId $subId

# Prepare the OAuth token to use in the request to the Cognitive Services REST API.
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Prepare and send the request to the Cognitive Services REST API.
$uri = "https://management.azure.com/subscriptions/" + $subId + `
    "/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18"
$body = @{
subdomainName = $subdomainName
type = "Microsoft.CognitiveServices/accounts"
}
$jsonBody = $body | ConvertTo-Json
Invoke-RestMethod -Method Post -Uri $uri -ContentType "application/json" -Authentication Bearer `
    -Token $token -Body $jsonBody | Format-List
```
如果所需名称可用，你会看到如下所示的响应：
```azurepowershell
isSubdomainAvailable : True
reason               :
type                 :
subdomainName        : my-custom-name
```
如果名称已被占用，你会看到以下响应：
```azurepowershell
isSubdomainAvailable : False
reason               : Sub domain name 'my-custom-name' is already used. Please pick a different name.
type                 :
subdomainName        : my-custom-name
```
## <a name="create-your-custom-domain-name"></a>创建自定义域名

若要为所选语音资源启用自定义域名，请使用 [Set-AzCognitiveServicesAccount](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount) cmdlet。

> [!WARNING]
> 在以下代码成功运行后，你会为语音资源创建自定义域名。 请记住，此名称无法更改。

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
$subId = "Your Azure subscription Id"
Set-AzContext -SubscriptionId $subId

# Set the custom domain name to the selected resource.
# WARNING: THIS CANNOT BE CHANGED OR UNDONE!
Set-AzCognitiveServicesAccount -ResourceGroupName $resourceGroup `
    -Name $speechResourceName -CustomSubdomainName $subdomainName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

此节需要最新版本的 Azure CLI。 如果你使用的是 Azure Cloud Shell，则表示已安装最新版本。

## <a name="verify-that-the-custom-domain-name-is-available"></a>验证自定义域名是否可用

检查要使用的自定义域是否免费。 使用认知服务 REST API 中的[检查域可用性](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability)方法。

复制以下代码块，插入首选自定义域名，并保存到文件 `subdomain.json`。

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

将文件复制到当前文件夹，或将它上传到 Azure Cloud Shell，并运行以下命令。 将 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 替换为你的 Azure 订阅 ID。

```azurecli-interactive
az rest --method post --url "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18" --body @subdomain.json
```
如果所需名称可用，你会看到如下所示的响应：
```azurecli
{
  "isSubdomainAvailable": true,
  "reason": null,
  "subdomainName": "my-custom-name",
  "type": null
}
```

如果名称已被占用，你会看到以下响应：
```azurecli
{
  "isSubdomainAvailable": false,
  "reason": "Sub domain name 'my-custom-name' is already used. Please pick a different name.",
  "subdomainName": "my-custom-name",
  "type": null
}
```
## <a name="turn-on-a-custom-domain-name"></a>启用自定义域名

若要为所选语音资源使用自定义域名，请使用 [az cognitiveservices account update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update) 命令。

选择包含语音资源的 Azure 订阅。 如果 Azure 帐户只有一个活动订阅，则可以跳过此步骤。 将 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 替换为你的 Azure 订阅 ID。
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
将自定义域名设置为所选资源。 将示例参数值替换为实际参数值，并运行以下命令。

> [!WARNING]
> 成功执行以下命令后，你会为语音资源创建自定义域名。 请记住，此名称无法更改。

```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

***

## <a name="turn-on-private-endpoints"></a>启用专用终结点

建议使用附加到虚拟网络的[专用 DNS 区域](../../dns/private-dns-overview.md)，并带有专用终结点的必要更新。 你可以在预配过程中创建专用 DNS 区域。 如果使用自己的 DNS 服务器，则可能还需要更改 DNS 配置。

在为生产语音资源预配专用终结点之前，决定 DNS 策略。 并测试 DNS 更改，尤其是在使用自己的 DNS 服务器时。

按照以下文章之一来创建专用终结点。
这些文章使用 Web 应用作为示例资源（通过专用终结点提供）。

- [使用 Azure 门户创建专用终结点](../../private-link/create-private-endpoint-portal.md)
- [使用 Azure PowerShell 创建专用终结点](../../private-link/create-private-endpoint-powershell.md)
- [使用 Azure CLI 创建专用终结点](../../private-link/create-private-endpoint-cli.md)

使用以下这些参数，而不是所选文章中的参数：

| 设置             | 值                                    |
|---------------------|------------------------------------------|
| 资源类型       | **Microsoft.CognitiveServices/accounts** |
| 资源            | **\<your-speech-resource-name>**         |
| 目标子资源 | **帐户**                              |

专用终结点的 DNS：查看[认知服务资源中专用终结点的 DNS](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints) 的一般原则。 然后通过执行以下各节中所述的检查，来确认 DNS 配置是否正常工作。

### <a name="resolve-dns-from-the-virtual-network"></a>解析来自虚拟网络的 DNS

此检查是必需的。

按照以下步骤测试来自虚拟网络的自定义 DNS 条目：

1. 登录位于专用终结点连接到的虚拟网络中的虚拟机。 
1. 打开 Windows 命令提示符或 Bash shell，运行 `nslookup` 并确认它已成功解析资源的自定义域名。

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  168.63.129.16

   Non-authoritative answer:
   Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
   Address:  172.28.0.10
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
   ```

1. 确认 IP 地址与专用终结点的 IP 地址匹配。

### <a name="resolve-dns-from-other-networks"></a>解析来自其他网络的 DNS

仅当在资源的“网络”部分启用了“所有网络”选项或“所选网络和专用终结点”访问选项时，才执行此检查  。 

如果计划仅使用专用终结点访问资源，则可以跳过此部分。

1. 登录连接到允许访问资源的网络的计算机。
2. 打开 Windows 命令提示符或 Bash shell，运行 `nslookup` 并确认它已成功解析资源的自定义域名。

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  fe80::1

   Non-authoritative answer:
   Name:    vnetproxyv1-weu-prod.westeurope.cloudapp.azure.com
   Address:  13.69.67.71
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
             my-private-link-speech.privatelink.cognitiveservices.azure.com
             westeurope.prod.vnet.cog.trafficmanager.net
   ```

> [!NOTE]
> 解析的 IP 地址指向虚拟网络代理终结点，该终结点会将网络流量分配给认知服务资源的专用终结点。 对于具有自定义域名但没有专用终结点的资源，行为会有所不同。 有关详细信息，请参阅[此节](#dns-configuration)。

## <a name="adjust-an-application-to-use-a-speech-resource-with-a-private-endpoint"></a>调整应用程序以使用具有专用终结点的语音资源

具有自定义域的语音资源以不同的方式与语音服务进行交互。 对于具有和没有专用终结点的启用了自定义域的语音资源，情况都是如此。 此节中的信息适用于这两种方案。

按照本部分的说明调整现有的应用程序和解决方案，以便使用启用了自定义域名和专用终结点的语音资源。

启用了自定义域名和专用终结点的语音资源使用不同的方法与语音服务交互。 此节说明如何将此类资源与语音服务 REST API 和[语音 SDK](speech-sdk.md) 结合使用。

> [!NOTE]
> 无专用终结点但使用自定义域名的语音资源也以特殊方式与语音服务进行交互。
> 此方法不同于使用专用终结点的语音资源的方案。 必须考虑这一点，因为你以后可能会决定删除专用终结点。
> 请参阅本文后面的“调整应用程序以使用无专用终结点的语音资源”。

### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-rest-apis"></a>具有自定义域名和专用终结点的语音资源：与 REST API 结合使用

对于此节，我们会使用 `my-private-link-speech.cognitiveservices.azure.com` 作为示例语音资源 DNS 名称（自定义域）。

语音服务具有用于[语音转文本](rest-speech-to-text.md)和[文本转语音](rest-text-to-speech.md)的 REST API。 对于启用了专用终结点的方案，请考虑以下信息。

语音转文本具有两个 REST API。 每个 API 都有不同的用途，使用不同的终结点，当你在启用了专用终结点的方案中使用时需要不同的方法。

语音转文本 REST API 包括：
- [语音转文本 REST API v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30)，用于[批量听录](batch-transcription.md)和[自定义语音识别](custom-speech-overview.md)。 v3.0 是 [v2.0 的后继版本](./migrate-v2-to-v3.md)
- [适用于短音频的语音转文本 REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio)，用于联机听录 

适用于短音频的语音转文本 REST API 和文本转语音 REST API 在专用终结点方案中的用法是相同的。 它相当于本文后面所述的[语音 SDK 情况](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk)。 

语音转文本 REST API v3.0 使用一组不同的终结点，因此它对启用了专用终结点的方案需要不同的方法。

下一小节会介绍这两种情况。

#### <a name="speech-to-text-rest-api-v30"></a>语音转文本 REST API v3.0

通常，语音资源使用[认知服务区域终结点](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)与[语音转文本 REST API v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) 通信。 这些资源的命名格式如下： <p/>`{region}.api.cognitive.microsoft.com`.

下面是一个示例请求 URL：

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```

> [!NOTE]
> 请参阅[此文](sovereign-clouds.md)，以了解 Azure 政府和 Azure 中国终结点。

为语音资源启用自定义域（这对专用终结点来说是必需的）后，该资源会对基本 REST API 终结点使用以下 DNS 名称模式： <p/>`{your custom name}.cognitiveservices.azure.com`

这意味着在我们的示例中，REST API 终结点名称会是： <p/>`my-private-link-speech.cognitiveservices.azure.com`

示例请求 URL 需要转换为：
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
此 URL 应可从连接了专用终结点的虚拟网络访问（提供了[正确的 DNS 解析](#resolve-dns-from-the-virtual-network)）。

为语音资源启用自定义域名后，通常会将所有请求 URL 中的主机名替换为新的自定义域主机名。 请求的所有其他部分（如前面示例中的路径 `/speechtotext/v3.0/transcriptions`）会保持不变。

> [!TIP]
> 一些客户会开发使用区域终结点 DNS 名称的区域部分的应用程序（例如，用于将请求发送到特定 Azure 区域中部署的语音资源）。
>
> 语音资源的自定义域不包含有关部署资源的区域的信息。 因此，前面所述的应用程序逻辑会不起作用，需要更改。

#### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>适用于短音频的语音转文本 REST API 和文本转语音 REST API

[适用于短音频的语音转文本 REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio)和[文本转语音 REST API](rest-text-to-speech.md) 使用两种类型的终结点：
- [认知服务区域终结点](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)，用于与认知服务 REST API 通信以获取授权令牌
- 所有其他操作的特殊终结点

> [!NOTE]
> 请参阅[此文](sovereign-clouds.md)，以了解 Azure 政府和 Azure 中国终结点。

有关与语音 SDK 结合使用的[此小节](#construct-endpoint-url)提供了有关特殊终结点以及如何为启用了专用终结点的语音资源转换其 URL 的详细说明。 对 SDK 介绍的相同原则适用于短音频的语音转文本 REST API 和文本转语音 REST API。

熟悉上一段落中提到的小节中的材料，并查看以下示例。 该示例说明文本转语音 REST API。 适用于短音频的语音转文本 REST API 的用法完全等效。

> [!NOTE]
> 在专用终结点方案中使用适用于短音频的语音转文本 REST API 和文本转语音 REST API 时，请使用通过 `Ocp-Apim-Subscription-Key` 标头传递的订阅密钥。 （请参阅[适用于短音频的语音转文本 REST API](rest-speech-to-text.md#request-headers) 和[文本转语音 REST API](rest-text-to-speech.md#request-headers)的详细信息）
>
> 仅当在语音资源的“网络”部分启用了“所有网络”访问选项后，才能使用授权令牌并通过 `Authorization` 标头将它传递到特殊终结点 。 在其他情况下，在尝试获取授权令牌时会收到 `Forbidden` 或 `BadRequest` 错误。

**文本转语音 REST API 使用示例**

我们将西欧用作示例 Azure 区域，并将 `my-private-link-speech.cognitiveservices.azure.com` 用作示例语音资源 DNS 名称（自定义域）。 我们示例中的自定义域名 `my-private-link-speech.cognitiveservices.azure.com` 属于在西欧区域中创建的语音资源。

若要获取区域中支持的语音列表，请执行以下请求：

```http
https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
```
请参阅[文本转语音 REST API 文档](rest-text-to-speech.md)中的更多详细信息。

对于启用了专用终结点的语音资源，需要修改相同操作的终结点 URL。 相同请求会如下所示：

```http
https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
```
请参阅语音 SDK 的[构造终结点 URL](#construct-endpoint-url) 小节中的详细说明。

### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk"></a>具有自定义域名和专用终结点的语音资源：与语音 SDK 结合使用

将语音 SDK 与启用了自定义域名和专用终结点的语音资源结合使用需要查看并可能会更改应用程序代码。

对于此节，我们会使用 `my-private-link-speech.cognitiveservices.azure.com` 作为示例语音资源 DNS 名称（自定义域）。

#### <a name="construct-endpoint-url"></a>构造终结点 URL

通常在 SDK 方案中（以及在适用于短音频的语音转文本 REST API 和文本转语音 REST API 方案中），语音资源会将专用区域终结点用于不同的服务产品。 这些终结点的 DNS 名称格式为：

`{region}.{speech service offering}.speech.microsoft.com`

示例 DNS 名称为：

`westeurope.stt.speech.microsoft.com`

区域（DNS 名称的第一个元素）的所有可能值都在[语音服务支持的区域](regions.md)中列出。 （请参阅[此文](sovereign-clouds.md)来了解 Azure 政府和 Azure 中国终结点。）下表提供了语音服务产品（DNS 名称的第二个元素）的可能值：

| DNS 名称值 | 语音服务产品                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [自定义命令](custom-commands.md)                       |
| `convai`       | [对话听录](conversation-transcription.md) |
| `s2s`          | [语音翻译](speech-translation.md)                 |
| `stt`          | [语音转文本](speech-to-text.md)                         |
| `tts`          | [文本转语音](text-to-speech.md)                         |
| `voice`        | [自定义语音](how-to-custom-voice.md)                      |

因此，前面示例 (`westeurope.stt.speech.microsoft.com`) 代表西欧中的语音转文本终结点。

启用了专用终结点的终结点通过特殊代理与语音服务通信。 因此，必须更改终结点连接 URL。 

“标准”终结点 URL 如下所示： <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

专用终结点 URL 如下所示： <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

示例 1。 一个应用程序使用以下 URL 进行通信（使用西欧美国英语的基础模型的语音识别）：

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

若要在语音资源的自定义域名为 `my-private-link-speech.cognitiveservices.azure.com` 时在启用了专用终结点的方案中使用它，必须按如下所示修改 URL：

```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

请注意详细信息：

- 主机名 `westeurope.stt.speech.microsoft.com` 会替换为自定义域主机名 `my-private-link-speech.cognitiveservices.azure.com`。
- 原始 DNS 名称的第二个元素 (`stt`) 成为 URL 路径的第一个元素，位于原始路径之前。 因此原始 URL `/speech/recognition/conversation/cognitiveservices/v1?language=en-US` 变为 `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US`。

示例 2。 一个应用程序通过自定义语音模型，使用以下 URL 在西欧合成语音：
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

以下等效 URL 使用专用终结点，其中，语音资源的自定义域名为 `my-private-link-speech.cognitiveservices.azure.com`：

```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

示例 1 中的相同原则会适用，但这次的关键元素是 `voice`。

#### <a name="modifying-applications"></a>修改应用程序

请按照以下步骤修改代码：

1. 确定应用程序终结点 URL：

   - [为应用程序启用日志记录](how-to-use-logging.md)，并运行它来记录活动。
   - 在日志文件中，搜索 `SPEECH-ConnectionUrl`。 在匹配行中，`value` 参数包含应用程序用于访问语音服务的完整 URL。

   示例：

   ```
   (114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
   ```

   因此，该应用程序在此示例中使用的 URL 是：

   ```
   wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
   ```

2. 使用完整终结点 URL 创建 `SpeechConfig` 实例：

   1. 修改刚才确定的终结点，如前面[构造终结点 URL](#construct-endpoint-url) 一节所述。

   1. 修改创建 `SpeechConfig` 实例的方式。 应用程序很可能会使用如下所示的内容：
      ```csharp
      var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
      ```
      由于前面几节中所述的主机名和 URL 更改，这不适用于启用了专用终结点的语音资源。 如果尝试使用启用了专用终结点的资源的密钥来运行现有应用程序而不进行任何修改，则会收到身份验证错误 (401)。

      若要使它正常工作，请修改实例化 `SpeechConfig` 类的方式并使用“from endpoint”/“with endpoint”初始化。 假设我们定义了以下两个变量：
      - `subscriptionKey` 包含启用了专用终结点的语音资源的密钥。
      - `endPoint` 包含完整的修改后终结点 URL（使用相应编程语言所需的类型）。 在我们的示例中，此变量应包含：
        ```
        wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
        ```

      创建一个 `SpeechConfig` 实例：
      ```csharp
      var config = SpeechConfig.FromEndpoint(endPoint, subscriptionKey);
      ```
      ```cpp
      auto config = SpeechConfig::FromEndpoint(endPoint, subscriptionKey);
      ```
      ```java
      SpeechConfig config = SpeechConfig.fromEndpoint(endPoint, subscriptionKey);
      ```
      ```python
      import azure.cognitiveservices.speech as speechsdk
      speech_config = speechsdk.SpeechConfig(endpoint=endPoint, subscription=subscriptionKey)
      ```
      ```objectivec
      SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithEndpoint:endPoint subscription:subscriptionKey];
      ```

> [!TIP]
> 不更改终结点 URI 中指定的查询参数，即使它们是由其他 API 所设置。 例如，如果识别语言在 URI 中定义为查询参数 `language=en-US`，并且还通过相应属性设置为 `ru-RU`，则使用 URI 中的语言设置。 因而有效语言是 `en-US`。
>
> 终结点 URI 中设置的参数始终优先。 其他 API 只能替代终结点 URI 中未指定的参数。

进行此修改后，应用程序应使用启用了专用终结点的语音资源。 我们在致力于更加无缝地支持专用终结点方案。

## <a name="adjust-an-application-to-use-a-speech-resource-without-private-endpoints"></a>调整应用程序以使用无专用终结点的语音资源

在本文中，我们多次指出，为语音资源启用自定义域是不可逆的。 与使用[区域终结点名称](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)的资源相比，此类资源会使用不同方式与语音服务通信。

本部分说明了如何将具有自定义域名但没有任何专用终结点的语音资源与语音服务 REST API 和[语音 SDK](speech-sdk.md) 结合使用。 这可能是在专用终结点方案中曾经使用过，但已删除了其专用终结点的资源。

### <a name="dns-configuration"></a>DNS 配置

请记住启用了专用终结点的语音资源的自定义域 DNS 名称如何[从公用网络进行解析](#resolve-dns-from-other-networks)。 在这种情况下，解析的 IP 地址指向虚拟网络的代理终结点。 该终结点用于将网络流量分派给启用了专用终结点的认知服务资源。

但是，删除所有资源专用终结点后（或就在启用自定义域名后），语音资源的 CNAME 记录会重新预配。 它现在指向相应[认知服务区域终结点](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)的 IP 地址。

因此 `nslookup` 命令的输出应如下所示：
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  fe80::1

Non-authoritative answer:
Name:    apimgmthskquihpkz6d90kmhvnabrx3ms3pdubscpdfk1tsx3a.cloudapp.net
Address:  13.93.122.1
Aliases:  my-private-link-speech.cognitiveservices.azure.com
          westeurope.api.cognitive.microsoft.com
          cognitiveweprod.trafficmanager.net
          cognitiveweprod.azure-api.net
          apimgmttmdjylckcx6clmh2isu2wr38uqzm63s8n4ub2y3e6xs.trafficmanager.net
          cognitiveweprod-westeurope-01.regional.azure-api.net
```
将它与来自[此节](#resolve-dns-from-other-networks)的输出进行比较。

### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-rest-apis"></a>具有自定义域名但没有专用终结点的语音资源：与 REST API 结合使用

#### <a name="speech-to-text-rest-api-v30"></a>语音转文本 REST API v3.0

语音转文本 REST API v3.0 用法完全等同于[启用了专用终结点的语音资源](#speech-to-text-rest-api-v30)的情况。

#### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>适用于短音频的语音转文本 REST API 和文本转语音 REST API

在这种情况下，适用于短音频的语音转文本 REST API 的用法和文本转语音 REST API 的用法与常规情况没有区别，但有一个例外。 （请参阅下面的注释。）应使用在[适用于短音频的语音转文本 REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) 和[文本转语音 REST API](rest-text-to-speech.md) 文档中介绍的两个 API。

> [!NOTE]
> 在自定义域方案中使用适用于短音频的语音转文本 REST API 和文本转语音 REST API 时，请使用通过 `Ocp-Apim-Subscription-Key` 标头传递的订阅密钥。 （请参阅[适用于短音频的语音转文本 REST API](rest-speech-to-text.md#request-headers) 和[文本转语音 REST API](rest-text-to-speech.md#request-headers)的详细信息）
>
> 仅当在语音资源的“网络”部分启用了“所有网络”访问选项后，才能使用授权令牌并通过 `Authorization` 标头将它传递到特殊终结点 。 在其他情况下，在尝试获取授权令牌时会收到 `Forbidden` 或 `BadRequest` 错误。

### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-speech-sdk"></a>具有自定义域名但没有专用终结点的语音资源：与语音 SDK 结合使用

将语音 SDK 与启用了自定义域但没有专用终结点的语音资源结合使用等效于在[语音 SDK 文档](speech-sdk.md)中所述的常规情况。

如果修改了代码以便与[启用了专用终结点的语音资源](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk)结合使用，请考虑以下事项。

在有关[启用了专用终结点的语音资源](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk)的一节中，我们说明了如何确定终结点 URL、对它进行修改以及通过 `SpeechConfig` 类实例的“from endpoint”/“with endpoint”初始化使它正常工作。

但是，如果在删除所有专用终结点之后（让相应 DNS 记录有时间进行重新预配）尝试运行相同应用程序，则会收到内部服务错误 (404)。 原因是 [DNS 记录](#dns-configuration)现在指向区域认知服务终结点而不是虚拟网络代理，不会在其中找到类似于 `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` 的 URL 路径。

需要按照以下代码的样式将应用程序回滚到 `SpeechConfig` 的标准实例化：

```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```

[!INCLUDE [](includes/speech-vnet-service-enpoints-private-endpoints-simultaneously.md)]

## <a name="pricing"></a>定价

有关定价详细信息，请参阅 [Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link)。

## <a name="learn-more"></a>了解更多信息

* [通过虚拟网络服务终结点使用语音服务](speech-service-vnet-service-endpoint.md)
* [Azure 专用链接](../../private-link/private-link-overview.md)
* [Azure VNet 服务终结点](../../virtual-network/virtual-network-service-endpoints-overview.md)
* [语音 SDK](speech-sdk.md)
* [语音转文本 REST API](rest-speech-to-text.md)
* [文本转语音 REST API](rest-text-to-speech.md)
