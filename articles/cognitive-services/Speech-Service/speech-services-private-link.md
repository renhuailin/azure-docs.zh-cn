---
title: 对专用终结点使用语音服务
titleSuffix: Azure Cognitive Services
description: 如何：将语音服务用于 Azure 专用链接提供的专用终结点
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: alexeyo
ms.openlocfilehash: 01a0171ed2b660fbabebf4276a74f8a3ea631bde
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516530"
---
# <a name="using-speech-services-with-private-endpoints-provided-by-azure-private-link"></a>将语音服务用于 Azure 专用链接提供的专用终结点

[Azure 专用链接](../../private-link/private-link-overview.md) 允许通过 [专用终结点](../../private-link/private-endpoint-overview.md)连接到 Azure 中的各种 PaaS 服务。 专用终结点是特定 [虚拟网络](../../virtual-network/virtual-networks-overview.md) 和子网中的专用 IP 地址。

本文介绍如何在 Azure 认知语音服务中设置和使用专用链接和专用终结点。 

> [!NOTE]
> 本文介绍了如何设置和使用与 Azure 认知语音服务的专用链接。 在继续下一步之前，请熟悉有关将 [虚拟网络用于认知服务](../cognitive-services-virtual-networks.md)的一般文章。

为私有终结点方案启用语音资源需要执行以下任务：
- [创建语音资源自定义域名](#create-custom-domain-name)
- [创建和配置专用终结点 (s) ](#enabling-private-endpoints)
- [调整现有应用程序和解决方案](#using-speech-resource-with-custom-domain-name-and-private-endpoint-enabled)

如果以后决定删除所有专用终结点，但继续使用该资源，则 [本节](#using-speech-resource-with-custom-domain-name-without-private-endpoints)将介绍必要的操作。

## <a name="create-custom-domain-name"></a>创建自定义域名

专用终结点需要使用 [认知服务自定义子域名称](../cognitive-services-custom-subdomains.md)。 使用下面的说明为语音资源创建一个。

> [!WARNING]
> 启用了自定义域名的语音资源使用不同的方法与语音服务交互。 大多数情况下，你必须在启用 [专用终结点](#using-speech-resource-with-custom-domain-name-and-private-endpoint-enabled) 的情况下调整应用程序代码，而 [**不** 是启用专用终结点](#using-speech-resource-with-custom-domain-name-without-private-endpoints) 的方案。
>
> 启用自定义域名的操作是 [**不可逆**](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name)的。 返回到 [区域名称](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) 的唯一方法是创建新的语音资源。 
>
> 特别是，如果您的语音资源包含大量关联的自定义模型和通过 [Speech Studio](https://speech.microsoft.com/) 创建的项目，我们 **强烈** 建议使用测试资源尝试配置，然后仅修改在生产中使用的资源。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

- 中转到 [Azure 门户](https://portal.azure.com/) 并登录到你的 Azure 帐户
- 选择所需的语音资源
- 选择 *网络* (*资源管理* 组)  
- 在 " *防火墙和虚拟网络* " 选项卡中 (默认) 单击 " **生成自定义域名** " 按钮
- 将显示一个新面板，其中包含为资源创建唯一自定义子域的说明
> [!WARNING]
> 创建自定义域名后，将 **无法** 更改该域名。 请参阅上述警告中的详细信息。
- 操作完成后，你可能需要选择 " *密钥和终结点* " (*资源管理* 组 ") 并验证资源的新终结点名称，格式为 <p />`{your custom name}.cognitiveservices.azure.com`

# <a name="powershell"></a>[PowerShell](#tab/powershell)

本部分需要本地运行 PowerShell 版本 7. x 或更高版本的 Azure PowerShell 模块版本5.1.0 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-Az-ps)。

继续运行 `Connect-AzAccount` 以创建与 Azure 的连接。

## <a name="verify-custom-domain-name-availability"></a>验证自定义域名可用性

需要检查您要使用的自定义域是否免费。 我们将使用 REST API 的认知服务中的 " [检查域可用性](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) " 方法。 请参阅下面代码块中的注释，其中说明了这些步骤。

> [!TIP]
> 下面的代码在 Azure Cloud Shell 中将 **不** 起作用。

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription containing Speech resource
# If your Azure account has only one active subscription
# you can skip this step
Set-AzContext -SubscriptionId $subId

# Preparing OAuth token which is used in request
# to Cognitive Services REST API
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Preparing and executing the request to Cognitive Services REST API
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
如果需要可用的名称，你将获得如下所示的响应：
```azurepowershell
isSubdomainAvailable : True
reason               :
type                 :
subdomainName        : my-custom-name
```
如果名称已被占用，你将获得以下响应：
```azurepowershell
isSubdomainAvailable : False
reason               : Sub domain name 'my-custom-name' is already used. Please pick a different name.
type                 :
subdomainName        : my-custom-name
```
## <a name="enabling-custom-domain-name"></a>启用自定义域名

若要为选定的语音资源启用自定义域名，请使用 [AzCognitiveServicesAccount](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount) cmdlet。 请参阅下面代码块中的注释，其中说明了这些步骤。

> [!WARNING]
> 成功执行下面的代码后，将为语音资源创建自定义域名。 **不能** 更改此名称。 请参阅上述警告中的详细信息。

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription containing Speech resource
# If your Azure account has only one active subscription
# you can skip this step
$subId = "Your Azure subscription Id"
Set-AzContext -SubscriptionId $subId

# Set the custom domain name to the selected resource
# WARNING! THIS IS NOT REVERSIBLE!
Set-AzCognitiveServicesAccount -ResourceGroupName $resourceGroup `
    -Name $speechResourceName -CustomSubdomainName $subdomainName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本部分需要 Azure CLI 的最新版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="verify-custom-domain-name-availability"></a>验证自定义域名可用性

需要检查您要使用的自定义域是否免费。 我们将使用 REST API 的认知服务中的 " [检查域可用性](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) " 方法。 

复制以下代码块，插入自定义域名并将其保存到文件 `subdomain.json` 。

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

将该文件复制到当前文件夹，或将其上载到 Azure Cloud Shell 并执行以下命令。  (替换 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 为你的 Azure 订阅 ID) 。

```azurecli-interactive
az rest --method post --url "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18" --body @subdomain.json
```
如果需要可用的名称，你将获得如下所示的响应：
```azurecli
{
  "isSubdomainAvailable": true,
  "reason": null,
  "subdomainName": "my-custom-name",
  "type": null
}
```

如果名称已被占用，你将获得以下响应：
```azurecli
{
  "isSubdomainAvailable": false,
  "reason": "Sub domain name 'my-custom-name' is already used. Please pick a different name.",
  "subdomainName": "my-custom-name",
  "type": null
}
```
## <a name="enabling-custom-domain-name"></a>启用自定义域名

若要启用所选语音资源的自定义域名，请使用 [az cognitiveservices account account update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update) 命令。

选择包含语音资源的 Azure 订阅。 如果你的 Azure 帐户只有一个活动订阅，则可以跳过此步骤。  (替换 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 为你的 Azure 订阅 ID) 。
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
将自定义域名设置为所选资源。 将示例参数值替换为实际的参数值，并执行以下命令。
> [!WARNING]
> 成功执行以下命令后，你将为语音资源创建自定义域名。 **不能** 更改此名称。 请参阅上述警告中的详细信息。
```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

**_

## <a name="enabling-private-endpoints"></a>启用专用终结点

使用 Azure 门户、Azure PowerShell 或 Azure CLI 启用专用终结点。

建议将附加到虚拟网络的 [专用 DNS 区域](../../dns/private-dns-overview.md) 用于专用终结点的必要更新，在预配过程中，我们会默认创建这些更新。 但是，如果使用自己的 DNS 服务器，则可能需要对 DNS 配置进行其他更改。 请参阅 " [用于专用终结点的 DNS](#dns-for-private-endpoints) " 部分。 最佳做法是在为生产语音资源) 预配专用终结点 (*之前* 决定 DNS 策略。 我们还建议进行初步测试，尤其是在使用自己的 DNS 服务器的情况下。

使用以下文章创建)  (专用终结点。 本文使用 Web 应用作为示例资源，以使用专用终结点进行启用。 请改用以下参数：

| 设置             | 值                                    |
|---------------------|------------------------------------------|
| 资源类型       | **Cognitiveservices account/帐户** |
| 资源            | **\<your-speech-resource-name>**         |
| 目标子资源 | **帐户**                              |

- [使用 Azure 门户创建专用终结点](../../private-link/create-private-endpoint-portal.md)
- [使用 Azure PowerShell 创建专用终结点](../../private-link/create-private-endpoint-powershell.md)
- [使用 Azure CLI 创建专用终结点](../../private-link/create-private-endpoint-cli.md)

### <a name="dns-for-private-endpoints"></a>专用终结点的 DNS

熟悉 [针对认知服务资源中的专用终结点的 DNS](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints)一般原则。 然后检查 DNS 配置是否正常工作 (参阅) 的下一小节。

#### <a name="mandatory-check-dns-resolution-from-the-virtual-network"></a> (必需检查) 。 来自虚拟网络的 DNS 解析

我们将使用 `my-private-link-speech.cognitiveservices.azure.com` 此部分的示例语音资源 DNS 名称。

登录到已附加到专用终结点的虚拟网络中的虚拟机。 打开 Windows 命令提示符或 Bash shell，执行 "nslookup" 命令并确保它成功解析资源自定义域名：
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  168.63.129.16

Non-authoritative answer:
Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
Address:  172.28.0.10
Aliases:  my-private-link-speech.cognitiveservices.azure.com
```
检查解析的 IP 地址是否对应于专用终结点的地址。

#### <a name="optional-check-dns-resolution-from-other-networks"></a> (可选检查) 。 来自其他网络的 DNS 解析

如果你计划在 "混合" 模式下使用启用了专用终结点的语音资源，则必须执行此项检查，即已在资源的 "*网络*" 部分中启用了 "*所有网络*" 或 "已 *选择网络" 和 "专用终结点* 访问" 选项。 如果计划使用专用终结点访问资源，则可以跳过此部分。

我们将使用 `my-private-link-speech.cognitiveservices.azure.com` 此部分的示例语音资源 DNS 名称。

在连接到你允许其访问资源的网络的任何计算机上，打开 Windows 命令提示符或 Bash shell，执行 "nslookup" 命令并确保它成功解析资源自定义域名：
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

请注意，IP 地址已解析到 VNet 代理终结点，该终结点用于将网络流量分派给启用了专用终结点的认知服务资源。 对于启用了自定义域名的资源，此行为将有所不同，但 *不* 会配置专用终结点。 请参阅 [此部分](#dns-configuration)。

## <a name="adjusting-existing-applications-and-solutions"></a>调整现有应用程序和解决方案 

启用了自定义域的语音资源使用不同的方法与语音服务交互。 这适用于已启用自定义域且[无需](#using-speech-resource-with-custom-domain-name-without-private-endpoints)专用终结[点的语音](#using-speech-resource-with-custom-domain-name-and-private-endpoint-enabled)资源。 当前部分提供了这两种情况所需的信息。

### <a name="using-speech-resource-with-custom-domain-name-and-private-endpoint-enabled"></a>使用启用了自定义域名和专用终结点的语音资源

启用了自定义域名和专用终结点的语音资源使用不同的方法与语音服务交互。 本部分介绍如何将此类资源与语音服务 REST API 和 [语音 SDK](speech-sdk.md)结合使用。

> [!NOTE]
> 请注意，没有专用终结点但启用了 **自定义域名** 的语音资源也有一种特殊的方式来与语音服务交互，但这种方式不同于启用了专用终结点的语音资源的情况。 如果你有这样的资源 (说，你有一个具有专用终结点的资源，但随后决定删除它们) 确保熟悉 [通信部分](#using-speech-resource-with-custom-domain-name-without-private-endpoints)。

#### <a name="speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-rest-api"></a>具有自定义域名和专用终结点的语音资源。 使用 REST API

`my-private-link-speech.cognitiveservices.azure.com`本部分将使用作为语音资源 DNS 名称 (自定义域) 的示例。

##### <a name="note-on-speech-services-rest-api"></a>有关语音服务的说明 REST API

语音服务已 REST API 用于 [语音到文本](rest-speech-to-text.md) 和 [文本到语音](rest-text-to-speech.md)转换。 对于启用了专用终结点的方案，需要考虑以下事项。

语音到文本具有两个不同的 REST Api。 每个 API 的用途不同，并使用不同的终结点，并在启用私有终结点方案时需要不同的方法。

语音到文本 REST Api 包括：
- [语音到文本 REST API 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) 用于 [批处理](batch-transcription.md) 脚本和 [自定义语音](custom-speech-overview.md)。 3.0 是 v2.0 [的后继版本](/azure/cognitive-services/speech-service/migrate-v2-to-v3)。
- [短音频的语音到文本 REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) 用于在线脚本。 

在专用终结点方案中，对短音频和文本到语音 REST API 使用语音到文本 REST API 的情况与本文后面介绍的 [语音 SDK 案例](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk) 相同。 

语音到文本 REST API 3.0 使用一组不同的终结点，因此需要为启用专用终结点的方案使用不同的方法。

以下小节介绍了这两种情况。


##### <a name="speech-to-text-rest-api-v30"></a>从语音到文本 REST API 3。0

通常，语音资源使用 [认知服务区域终结点](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) 与 [语音到文本 REST API 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30)通信。 这些资源的命名格式如下： <p/>`{region}.api.cognitive.microsoft.com`

下面是一个示例请求 URL：

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```
为语音资源启用自定义域后 (对于专用终结点是必需的) 此类资源将对基本 REST API 终结点使用以下 DNS 名称模式： <p/>`{your custom name}.cognitiveservices.azure.com`

这意味着，在我们的示例中，REST API 终结点名称将为： <p/>`my-private-link-speech.cognitiveservices.azure.com`

上面的示例请求 URL 需要转换为：
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
此 URL 应可从连接了专用终结点的虚拟网络中访问， (提供了 [正确的 DNS 解析](#mandatory-check-dns-resolution-from-the-virtual-network)) 。

一般来说，在为语音资源启用自定义域名后，需要将所有请求 Url 中的主机名替换为新的自定义域主机名。 请求的所有其他部分 (如 `/speechtotext/v3.0/transcriptions` 以上示例中的路径) 保持不变。

> [!TIP]
> 一些客户开发了使用区域终结点 DNS 名称的区域部分的应用程序 (例如，将请求发送到特定 Azure 区域中部署的语音资源) 。
>
> 语音资源自定义域名 **不** 包含有关在其中部署资源的区域的信息。 因此，以上所述的应用程序逻辑将 **不** 起作用，需要更改。

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>短音频和文本到语音转换的语音到文本 REST API REST API

用于短音频和[文本到语音转换](rest-text-to-speech.md)[的语音到文本 REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) REST API 使用两种类型的终结点：
- [认知服务区域终结点](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) ，用于与认知服务进行通信 REST API 获取授权令牌
- 所有其他操作的特殊终结点

下面的 "使用语音 SDK 的用法" [部分中提供](#general-principle) 了特殊终结点以及如何为启用了专用终结点的语音资源转换其 URL 的详细说明。 SDK 所述的同一原则适用于语音到文本1.0 版和文本到语音 REST API。

熟悉上一段落中提到的子节中的材料，并查看以下示例。  (示例说明文本到语音转换 REST API;对于短音频，使用语音到文本 REST API 完全等效) 

> [!NOTE]
> 使用 **语音到文本 REST API** 专用终结点方案中的短音频时，需要使用 [通过](rest-speech-to-text.md#request-headers) `Authorization` [标头](rest-speech-to-text.md#request-headers)传递的授权令牌; 通过标头将语音订阅密钥传递到专用终结点 `Ocp-Apim-Subscription-Key` 将 **不** 起作用，并将生成错误401。

**文本到语音 REST API 用法示例。**

我们会将西欧用作示例 Azure 区域，并将 `my-private-link-speech.cognitiveservices.azure.com` 其用作 (自定义域) 的语音资源 DNS 名称示例。 `my-private-link-speech.cognitiveservices.azure.com`本示例中的自定义域名属于西欧区域中创建的语音资源。

若要获取区域中支持的语音列表，需要执行以下两项操作：

- 通过获取授权令牌
```http
https://westeurope.api.cognitive.microsoft.com/sts/v1.0/issuetoken
```
- 使用获取的令牌通过获取语音列表
```http
https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
```
 (在 [文本到语音 REST API 文档](rest-text-to-speech.md) 中查看上述步骤的更多详细信息) 

对于启用了专用终结点的语音资源，需要修改相同操作序列的终结点 Url。 相同的顺序如下所示：
- 通过获取授权令牌
```http
https://my-private-link-speech.cognitiveservices.azure.com/v1.0/issuetoken
```
 (参阅上方 [的语音到文本 REST API 3.0](#speech-to-text-rest-api-v30) 子节中的详细说明) 
- 使用获取的令牌通过获取语音列表
```http
https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
```
 (请参阅下面的 "使用语音 SDK 的用法" 部分中的 [常规原则](#general-principle) 子节中的详细说明) 

#### <a name="speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk"></a>具有自定义域名和专用终结点的语音资源。 使用语音 SDK

将语音 SDK 与自定义域名和专用终结点启用的语音资源一起使用需要检查和更改应用程序代码。 我们正在致力于对专用终结点方案进行更多的无缝支持。

`my-private-link-speech.cognitiveservices.azure.com`本部分将使用作为语音资源 DNS 名称 (自定义域) 的示例。

##### <a name="general-principle"></a>一般原则

通常在 SDK 方案 (和文本到语音的 REST API 方案中) 语音资源使用不同服务产品的特殊区域终结点。 这些终结点的 DNS 名称格式为： </p>`{region}.{speech service offering}.speech.microsoft.com`

示例： </p>`westeurope.stt.speech.microsoft.com`

[此处](regions.md)列出了 dns 名称) 的区域 (第一个元素的所有可能值，如下表所示，其中显示了语音服务产品的可能值 (DNS 名称) 的第二个元素：

| DNS 名称值 | 语音服务产品                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [自定义命令](custom-commands.md)                       |
| `convai`       | [对话听录](conversation-transcription.md) |
| `s2s`          | [语音翻译](speech-translation.md)                 |
| `stt`          | [语音转文本](speech-to-text.md)                         |
| `tts`          | [文本到语音转换](text-to-speech.md)                         |
| `voice`        | [自定义语音](how-to-custom-voice.md)                      |

因此，上面 (的示例 `westeurope.stt.speech.microsoft.com`) 在西欧中代表语音到文本终结点。

启用专用终结点的终结点通过特殊的代理与语音服务通信，并因此 **需要更改端点连接 url**。 以下原则适用： "标准" 终结点 URL 遵循的模式 <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

它应更改为： <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**示例1。** 应用程序使用以下 URL 进行通信 (语音识别在西欧) 中使用美国英语的基本模型： 
```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

在启用专用终结点的情况下，如果语音资源的自定义域名是 `my-private-link-speech.cognitiveservices.azure.com` 此 URL，则需要修改此 URL，如下所示：
```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

让我们看看：
- 主机名 `westeurope.stt.speech.microsoft.com` 替换为自定义域主机名 `my-private-link-speech.cognitiveservices.azure.com`
- 原始 DNS 名称 () 的第二个元素 `stt` 成为 URL 路径的第一个元素，位于原始路径之前，即原始 URL `/speech/recognition/conversation/cognitiveservices/v1?language=en-US` 变为 `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US`
 
**示例2。** 应用程序正在使用西欧) 中的自定义语音模型 (语音综合进行通信： 
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```
在启用专用终结点的情况下，如果语音资源的自定义域名是 `my-private-link-speech.cognitiveservices.azure.com` 此 URL，则需要修改此 URL，如下所示： 
```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

与示例1中的原则相同，但此时间的关键元素是 `voice` 。

##### <a name="modifying-applications"></a>修改应用程序

若要将上一节中所述的原则应用于应用程序代码，需要执行两个主要操作：

- 确定应用程序正在使用的终结点 URL
- 按上一部分所述修改终结点 URL，并 `SpeechConfig` 使用此修改后的 URL 显式创建类实例

###### <a name="determining-application-endpoint-url"></a>确定应用程序终结点 URL

- [为应用程序启用日志记录](how-to-use-logging.md) ，并运行它以生成日志
- 在日志文件中搜索 `SPEECH-ConnectionUrl` 。 此字符串将包含 `value` 参数，该参数又将包含应用程序所使用的完整 URL

带有终结点 URL 的日志文件行的示例：
```
(114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
```
因此，该应用程序在本示例中使用的 URL 是：
```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```
###### <a name="creating-speechconfig-instance-using-full-endpoint-url"></a>`SpeechConfig`使用完整终结点 URL 创建实例

按照上述 [一般原则](#general-principle) 中所述，修改上一部分中确定的终结点。

现在，您需要修改的实例的创建方式 `SpeechConfig` 。 你的当今应用程序很可能会使用如下所示的内容：
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
由于在前面的部分中介绍的主机名和 URL 更改，这对于启用专用终结点的语音资源不起作用。 如果尝试使用启用了专用终结点的资源的密钥来运行现有应用程序而不进行任何修改，则 (401) 会出现身份验证错误。

若要使其正常工作，需要修改如何实例化 `SpeechConfig` 类并使用 "来自终结点"/"通过终结点" 初始化。 假设我们定义了以下两个变量：
- `subscriptionKey` 包含启用了专用终结点的语音资源的密钥
- `endPoint` 包含 (使用通信编程语言) 所需类型的完整 **修改后** 的终结点 URL。 在本示例中，此变量应包含
```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```
接下来，我们需要实例化 `SpeechConfig` 类，如下所示：
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
> 终结点 URI 中指定的查询参数不会更改，即使它们是由任何其他 Api 设置的。 例如，如果在 URI 中将识别语言定义为查询参数 "language = en-us"，并通过 "通信" 属性将其设置为 "ru"，则 URI 中的语言设置优先，有效语言为 "en-us"。 只有未在终结点 URI 中指定的参数可由其他 Api 设置。

修改后，应用程序应使用启用了专用的语音资源。 我们正在致力于对专用终结点方案进行更多的无缝支持。

### <a name="using-speech-resource-with-custom-domain-name-without-private-endpoints"></a>将语音资源与自定义域名结合使用（无需专用终结点）

在本文中，我们多次指出，为语音资源启用自定义域是不 **可逆** 的，因此，此类资源将使用不同的方式与使用 [区域终结点名称](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)) 的 "常用" (方式进行通信。

本部分介绍如何将语音资源用于启用了自定义域名的语音资源，但 **没有** 任何具有语音服务 REST API 和 [语音 SDK](speech-sdk.md)的专用终结点。 这可能是在私有终结点方案中曾经使用过的资源，但在) 删除其专用终结点 (。

#### <a name="dns-configuration"></a>DNS 配置

请记住，启用专用终结点的语音资源的自定义域 DNS 名称如何 [从公用网络解析](#optional-check-dns-resolution-from-other-networks)。 在这种情况下，IP 地址解析点到 VNet 代理终结点，该终结点用于将网络流量分派给启用了专用终结点的认知服务资源。

但是，删除 **所有** 资源专用终结点后 (或直接在启用自定义域名后，) 语音资源的 CNAME 记录将重新预配，并将其指向 "通信 [认知服务区域" 终结点](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)的 IP 地址。

因此，该命令的输出将如下所 `nslookup` 示：
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
将其与 [此部分](#optional-check-dns-resolution-from-other-networks)的输出进行比较。

#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-rest-api"></a>带有自定义域名的语音资源，无需专用终结点。 使用 REST API

##### <a name="speech-to-text-rest-api-v30"></a>从语音到文本 REST API 3。0

语音到文本 REST API 3.0 使用完全等同于 [启用了专用终结点的语音资源](#speech-to-text-rest-api-v30)的情况。

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>短音频和文本到语音转换的语音到文本 REST API REST API

在这种情况下，对于短音频和文本到语音 REST API 使用的语音到文本 REST API 与常规情况没有任何差异，对于短 (音频的语音到文本 REST API，请参阅下面的 ") "。 对于短音频和[文本到语音 REST API](rest-text-to-speech.md)文档，应按[语音到文本 REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio)中所述使用这两个 api。

> [!NOTE]
> 在自定义域中 **为短音频使用语音到文本 REST API** 时，需要使用 [通过](rest-speech-to-text.md#request-headers) `Authorization` [标头](rest-speech-to-text.md#request-headers)传递的授权令牌; 通过标头将语音订阅密钥传递到专用终结点 `Ocp-Apim-Subscription-Key` 将 **不** 起作用，并将生成错误401。

#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-speech-sdk"></a>带有自定义域名的语音资源，无需专用终结点。 使用语音 SDK

将语音 SDK 与自定义域名一起使用启用了 **无需** 专用终结点的语音资源需要检查和更改应用程序代码。 请注意，与 [启用专用终结点的语音资源](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk)的情况相比，这些更改是 **不同** 的。 我们正在致力于对专用终结点/自定义域方案进行更多的无缝支持。

`my-private-link-speech.cognitiveservices.azure.com`本部分将使用作为语音资源 DNS 名称 (自定义域) 的示例。

在 " [启用了专用终结点的语音资源](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk) " 部分中，我们介绍了如何确定所使用的终结点 URL，对其进行修改，使其可以通过类实例的 "from 终结点"/"通过终结点初始化" `SpeechConfig` 。

但是，如果在删除所有专用终结点之后尝试运行同一应用程序 (允许一些时间访问通信 DNS 记录重新设置) 你将收到 (404) 的内部服务错误。 原因是 [DNS 记录](#dns-configuration) 现在指向区域认知服务终结点，而不是 VNet 代理，因此，不会在那里找到类似的 URL 路径 `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` ，因此 (404) 出现 "找不到" 错误。

如果你将应用程序 "回滚" 到样式为的 "标准" 实例， `SpeechConfig`
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
应用程序将终止，并出现身份验证错误 (401) 。

##### <a name="modifying-applications"></a>修改应用程序

若要使应用程序能够使用自定义域名而无需专用终结点的语音资源方案，需要执行以下操作：
- 通过认知服务 REST API 请求授权令牌
- `SpeechConfig`使用 "从授权令牌"/"使用授权令牌" 方法实例化类 

###### <a name="requesting-authorization-token"></a>正在请求授权令牌

请参阅 [此文](../authentication.md#authenticate-with-an-authentication-token) ，了解如何通过认知服务 REST API 获取令牌。 

使用终结点 URL 中的自定义域名，此 URL 为：
```http
https://my-private-link-speech.cognitiveservices.azure.com/sts/v1.0/issueToken
```
> [!TIP]
> 你可能会在 Azure 门户的语音资源的 *密钥和终结点* (*资源管理* 组) 部分中找到此 URL。

###### <a name="creating-speechconfig-instance-using-authorization-token"></a>`SpeechConfig`使用授权令牌创建实例

需要 `SpeechConfig` 使用在上一部分中获得的授权令牌来实例化类。 假设我们定义了以下变量：

- `token` 包含上一节中获取的授权令牌
- `azureRegion` 包含语音资源 [区域](regions.md) 的名称的 (例如： `westeurope`) 
- `outError` (仅适用于 [目标 C](/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithauthorizationtokenregionerror) 大小写) 

接下来，我们需要实例化 `SpeechConfig` 类，如下所示：
```csharp
var config = SpeechConfig.FromAuthorizationToken(token, azureRegion);
```
```cpp
auto config = SpeechConfig::FromAuthorizationToken(token, azureRegion);
```
```java
SpeechConfig config = SpeechConfig.fromAuthorizationToken(token, azureRegion);
```
```python
import azure.cognitiveservices.speech as speechsdk
speech_config = speechsdk.SpeechConfig(auth_token=token, region=azureRegion)
```
```objectivec
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithAuthorizationToken:token region:azureRegion error:outError];
```
> [!NOTE]
> 调用方需要确保授权标记有效。 在授权令牌过期之前，调用方需要通过使用新的有效令牌调用此资源库来刷新该令牌。 当创建新的识别器/合成器时，将复制配置值，新的令牌值将不适用于已创建的识别器。 对于之前创建的识别器/合成器，你需要设置相应的识别器/合成器的授权令牌以刷新令牌。 否则，识别器/合成器将在识别/合成过程中遇到错误。

进行此修改后，应用程序应使用自定义域名启用了无专用终结点的语音资源。 我们正在致力于自定义域/私有终结点方案的更流畅支持。

## <a name="pricing"></a>定价

有关定价详细信息，请参阅 [Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link)。

## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure 专用链接](../../private-link/private-link-overview.md)
* 了解有关[SPEECH SDK](speech-sdk.md)的详细信息
* 详细了解 [语音到文本 REST API](rest-speech-to-text.md)
* 了解有关[文本到语音 REST API 的](rest-text-to-speech.md)详细信息
