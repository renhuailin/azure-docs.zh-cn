---
title: 如何将专用终结点用于语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何通过 Azure 专用链接提供的专用终结点使用语音服务
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: alexeyo
ms.openlocfilehash: 61be4b45df94c902c0473b94a6dd83237c72da3c
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98196107"
---
# <a name="use-speech-services-through-a-private-endpoint"></a>通过专用终结点使用语音服务

使用[Azure Private Link](../../private-link/private-link-overview.md)可以通过[专用终结点](../../private-link/private-endpoint-overview.md)连接到 Azure 中的服务。 专用终结点是专用 IP 地址，只能在特定 [虚拟网络](../../virtual-network/virtual-networks-overview.md) 和子网内访问。

本文介绍如何在 Azure 认知服务中设置和使用语音服务的专用链接和专用终结点。

> [!NOTE]
> 在继续操作之前，请查看 [如何将虚拟网络与认知服务配合使用](../cognitive-services-virtual-networks.md)。

本文还介绍了 [如何稍后删除专用终结点，但仍使用语音资源](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints)。

## <a name="create-a-custom-domain-name"></a>创建自定义域名

专用终结点需要 [用于认知服务的自定义子域名称](../cognitive-services-custom-subdomains.md)。 使用以下说明为语音资源创建一个。

> [!WARNING]
> 启用了自定义域名的语音资源使用不同的方法与语音服务交互。 你可能需要为这两种方案调整应用程序代码：已 [启用专用终结点](#use-a-speech-resource-with-a-custom-domain-name-and-a-private-endpoint-enabled) ，但 [*未* 启用专用终结点](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints)。
>
> 启用自定义域名时，操作是 [不可逆](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name)的。 返回到 [区域名称](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) 的唯一方法是创建新的语音资源。
>
> 如果你的语音资源有大量关联的自定义模型和通过 [Speech Studio](https://speech.microsoft.com/)创建的项目，我们强烈建议你在修改生产中使用的资源之前，使用测试资源尝试配置。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要使用 Azure 门户创建自定义域名，请执行以下步骤：

1. 转到 [Azure 门户](https://portal.azure.com/)并登录 Azure 帐户。
1. 选择所需的语音资源。
1. 在左侧窗格的 " **资源管理** " 组中，选择 " **网络**"。
1. 在 " **防火墙和虚拟网络** " 选项卡上，选择 " **生成自定义域名**"。 此时将显示一个新的右侧面板，其中包含为资源创建唯一自定义子域的说明。
1. 在 " **生成自定义域名** " 面板中，输入自定义域名。 你的完整自定义域将如下所示： `https://{your custom name}.cognitiveservices.azure.com` 。 
    
    请记住，创建自定义域名后，将 _无法_ 更改该域名。
    
    输入自定义域名后，请选择 " **保存**"。
1. 操作完成后，在 " **资源管理** " 组中，选择 " **密钥和终结点**"。 确认资源的新终结点名称以这种方式启动： `https://{your custom name}.cognitiveservices.azure.com` 。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 创建自定义域名，请确认您的计算机具有 PowerShell 版本7、windows 或更高版本的 Azure PowerShell 模块版本5.1.0 或更高版本。 若要查看这些工具的版本，请执行以下步骤：

1. 在 PowerShell 窗口中，输入：

    `$PSVersionTable`

    确认 `PSVersion` 值为 7. x 或更高。 若要升级 PowerShell，请按照 [安装各种版本的 PowerShell](/powershell/scripting/install/installing-powershell)中的说明进行操作。

1. 在 PowerShell 窗口中，输入：

    `Get-Module -ListAvailable Az`

    如果未显示任何内容，或者 Azure PowerShell 模块的版本早于5.1.0，请按照 [安装 Azure PowerShell 模块](/powershell/azure/install-Az-ps) 升级中的说明进行操作。

在继续操作之前，请运行 `Connect-AzAccount` 以创建与 Azure 的连接。

## <a name="verify-that-a-custom-domain-name-is-available"></a>验证自定义域名是否可用

检查要使用的自定义域是否可用。 以下代码通过使用认知服务 REST API 中的 " [检查域可用性](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) " 操作来确认域可用。

> [!TIP]
> 在 Azure Cloud Shell 中，以下代码将 *不* 起作用。

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
如果所需的名称可用，你会看到如下所示的响应：
```azurepowershell
isSubdomainAvailable : True
reason               :
type                 :
subdomainName        : my-custom-name
```
如果名称已被占用，则会看到以下响应：
```azurepowershell
isSubdomainAvailable : False
reason               : Sub domain name 'my-custom-name' is already used. Please pick a different name.
type                 :
subdomainName        : my-custom-name
```
## <a name="create-your-custom-domain-name"></a>创建自定义域名

若要为选定的语音资源启用自定义域名，请使用 [AzCognitiveServicesAccount](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount) cmdlet。

> [!WARNING]
> 在以下代码成功运行后，你将为语音资源创建自定义域名。 请记住，此名称 *无法* 更改。

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

本部分需要 Azure CLI 的最新版本。 如果使用 Azure Cloud Shell，则已安装最新版本。

## <a name="verify-that-the-custom-domain-name-is-available"></a>验证自定义域名是否可用

检查要使用的自定义域是否免费。 使用 "认知服务 REST API 的 [检查域可用性](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) 方法。

复制以下代码块，插入首选的自定义域名，并将其保存到文件 `subdomain.json` 。

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

将该文件复制到当前文件夹，或将其上传到 Azure Cloud Shell，并运行以下命令。 将 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 替换为你的 Azure 订阅 ID。

```azurecli-interactive
az rest --method post --url "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18" --body @subdomain.json
```
如果所需的名称可用，你会看到如下所示的响应：
```azurecli
{
  "isSubdomainAvailable": true,
  "reason": null,
  "subdomainName": "my-custom-name",
  "type": null
}
```

如果名称已被占用，则会看到以下响应：
```azurecli
{
  "isSubdomainAvailable": false,
  "reason": "Sub domain name 'my-custom-name' is already used. Please pick a different name.",
  "subdomainName": "my-custom-name",
  "type": null
}
```
## <a name="enable-a-custom-domain-name"></a>启用自定义域名

若要为所选语音资源启用自定义域名，请使用 [az cognitiveservices account account update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update) 命令。

选择包含语音资源的 Azure 订阅。 如果你的 Azure 帐户只有一个活动订阅，则可以跳过此步骤。 将 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 替换为你的 Azure 订阅 ID。
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
将自定义域名设置为所选资源。 将示例参数值替换为实际的参数值，并运行以下命令。

> [!WARNING]
> 成功执行以下命令后，你将为语音资源创建自定义域名。 请记住，此名称 *无法* 更改。

```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

***

## <a name="enable-private-endpoints"></a>启用专用终结点

建议使用附加到虚拟网络的 [专用 DNS 区域](../../dns/private-dns-overview.md) ，其中包含专用终结点的必要更新。 默认情况下，在设置过程中创建专用 DNS 区域。 如果你使用自己的 DNS 服务器，则可能还需要更改 DNS 配置。 

在为生产语音资源预配专用终结点 *之前* ，请决定 DNS 策略。 并测试 DNS 更改，尤其是在使用自己的 DNS 服务器时。

使用以下项目之一来创建专用终结点。 这些文章使用 web 应用作为示例资源来实现专用终结点。

- [使用 Azure 门户创建专用终结点](../../private-link/create-private-endpoint-portal.md)
- [使用 Azure PowerShell 创建专用终结点](../../private-link/create-private-endpoint-powershell.md)
- [使用 Azure CLI 创建专用终结点](../../private-link/create-private-endpoint-cli.md)

使用这些参数，而不是所选项目中的参数：

| 设置             | 值                                    |
|---------------------|------------------------------------------|
| 资源类型       | **Microsoft.CognitiveServices/accounts** |
| 资源            | **\<your-speech-resource-name>**         |
| 目标子资源 | **帐户**                              |

**专用终结点的 DNS：** 查看 [有关认知服务资源中的专用终结点的 DNS](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints)一般原则。 然后通过执行以下部分中所述的检查，确认 DNS 配置是否正常工作。

### <a name="resolve-dns-from-the-virtual-network"></a>解析虚拟网络中的 DNS

*需要* 进行此检查。

请按照以下步骤从虚拟网络测试自定义 DNS 条目：

1. 登录到已附加到专用终结点的虚拟网络中的虚拟机。 
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

仅当你在资源的 "**网络**" 部分中启用了 "**所有网络**" 选项或 "**所选网络和专用终结点** 访问" 选项时才执行此检查。 

如果计划使用专用终结点来访问资源，则可以跳过此部分。

1. 登录到连接到允许访问资源的网络的计算机。
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

3. 确认 IP 地址与专用终结点的 IP 地址匹配。

> [!NOTE]
> 解析的 IP 地址指向虚拟网络代理终结点，该终结点将网络流量调度到认知服务资源的专用终结点。 对于具有自定义域名但 *没有* 专用终结点的资源，该行为将有所不同。 有关详细信息，请参阅 [此部分](#dns-configuration) 。

## <a name="adjust-existing-applications-and-solutions"></a>调整现有应用程序和解决方案

启用了自定义域的语音资源使用不同的方法与语音服务交互。 这适用于启用了域的无需专用终结点的已启用域的语音资源。 本部分中的信息适用于这两种方案。

### <a name="use-a-speech-resource-with-a-custom-domain-name-and-a-private-endpoint-enabled"></a>使用带有自定义域名和专用终结点的语音资源

启用了自定义域名和专用终结点的语音资源使用不同的方法与语音服务交互。 本部分介绍如何通过语音服务 REST Api 和 [语音 SDK](speech-sdk.md)使用此类资源。

> [!NOTE]
> 如果某个语音资源没有专用终结点，但启用了自定义域名，则还可以通过一种特殊的方式与语音服务交互。 这种方式不同于启用了专用终结点的语音资源的方案。 例如，如果你具有这样的资源 (例如，你有一个具有专用终结点的资源，但随后决定将其删除) ，请参阅 [将语音资源与自定义域名结合使用和不使用私有终结点](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints)部分。

#### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-rest-apis"></a>使用自定义域名和专用终结点的语音资源：使用 REST Api

`my-private-link-speech.cognitiveservices.azure.com`本部分将使用作为语音资源 DNS 名称 (自定义域) 的示例。

语音服务包含用于 [语音到文本](rest-speech-to-text.md) 和 [文本到语音](rest-text-to-speech.md)转换的 REST api。 对于启用了私有终结点的方案，请考虑以下信息。

语音到文本有两个 REST Api。 每个 API 的用途不同，并使用不同的终结点，并在启用私有终结点的方案中使用时需要不同的方法。

语音到文本 REST Api 包括：
- 用于[批处理](batch-transcription.md)和[自定义语音](custom-speech-overview.md)的[语音到文本 REST API 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30)。 3.0 是 v2.0 [的后继版本](/azure/cognitive-services/speech-service/migrate-v2-to-v3)
- 用于在线脚本的[短音频的语音到文本 REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) 

在专用终结点方案中，对短音频和文本到语音 REST API 使用语音到文本 REST API。 它相当于本文后面所述的 [语音 SDK 案例](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk) 。 

语音到文本 REST API 3.0 使用一组不同的终结点，因此它需要对启用了私有终结点的方案使用不同的方法。

下一小节将介绍这两种情况。

##### <a name="speech-to-text-rest-api-v30"></a>从语音到文本 REST API 3。0

通常，语音资源使用 [认知服务区域终结点](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) 与 [语音到文本 REST API 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30)通信。 这些资源的命名格式如下： <p/>`{region}.api.cognitive.microsoft.com`.

下面是一个示例请求 URL：

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```
为语音资源启用自定义域后 (对于专用终结点) 是必需的，该资源将对基本 REST API 终结点使用以下 DNS 名称模式： <p/>`{your custom name}.cognitiveservices.azure.com`.

这意味着，在我们的示例中，REST API 终结点名称将为： <p/>`my-private-link-speech.cognitiveservices.azure.com`.

并且需要将示例请求 URL 转换为：
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
此 URL 应可从连接了专用终结点的虚拟网络中访问， (提供了 [正确的 DNS 解析](#resolve-dns-from-the-virtual-network)) 。

为语音资源启用自定义域名后，通常会将所有请求 Url 中的主机名替换为新的自定义域主机名。 请求的所有其他部分 (与 `/speechtotext/v3.0/transcriptions` 前面示例中的路径相同) 会保持不变。

> [!TIP]
> 一些客户开发使用区域终结点的 DNS 名称的区域部分的应用程序 (例如，将请求发送到特定 Azure 区域中部署的语音资源) 。
>
> 语音资源的自定义域 *不* 包含有关在其中部署资源的区域的信息。 因此，前面所述的应用程序逻辑将 *不* 起作用，需要更改。

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>短音频和文本到语音转换的语音到文本 REST API REST API

短音频和[文本到语音转换](rest-text-to-speech.md) [REST API 的语音到文本](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio)REST API 使用两种类型的终结点：
- [认知服务区域终结点](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) ，用于与认知服务进行通信 REST API 获取授权令牌
- 所有其他操作的特殊终结点

[本小节](#general-principles)提供了有关特殊终结点以及如何为启用了专用终结点的语音资源转换其 URL 的详细说明。 SDK 介绍的同一原则适用于语音到文本 REST API v1.0 和文本到语音转换 REST API。

熟悉上一段落中提到的子节中的材料，并查看以下示例。 该示例说明文本到语音 REST API。 对于短音频，使用语音到文本 REST API 完全等效。

> [!NOTE]
> 当你对专用终结点方案中的短音频使用语音到文本 REST API 时，请使用 [通过](rest-speech-to-text.md#request-headers) 该 `Authorization` [标头](rest-speech-to-text.md#request-headers)传递的授权令牌。 通过标头将语音订阅密钥传递到特殊终结点 `Ocp-Apim-Subscription-Key` 将 *不* 起作用，并将生成错误401。

**文本到语音 REST API 用法示例**

我们将西欧用作示例 Azure 区域，并将 `my-private-link-speech.cognitiveservices.azure.com` 其用作 (自定义域) 的语音资源 DNS 名称示例。 本示例中的自定义域名 `my-private-link-speech.cognitiveservices.azure.com` 属于在西欧区域中创建的语音资源。

若要获取区域中支持的语音列表，请执行以下两项操作：

- 获取授权令牌：
  ```http
  https://westeurope.api.cognitive.microsoft.com/sts/v1.0/issuetoken
  ```
- 使用令牌获取语音列表：
  ```http
  https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
  ```
请参阅 [文本到语音 REST API 文档](rest-text-to-speech.md)中前面步骤的更多详细信息。

对于启用了专用终结点的语音资源，需要修改相同操作序列的端点 Url。 相同的顺序如下所示：

- 获取授权令牌：
  ```http
  https://my-private-link-speech.cognitiveservices.azure.com/v1.0/issuetoken
  ```
  请参阅前面的 [语音到文本 REST API 3.0](#speech-to-text-rest-api-v30) 节中的详细说明。

- 使用获取的令牌获取语音列表：
  ```http
  https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
  ```
  请参阅语音 SDK [一般原则](#general-principles) 子节中的详细说明。

#### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk"></a>使用自定义域名和专用终结点的语音资源：使用语音 SDK

使用带有自定义域名的语音 SDK 和启用了专用终结点的语音资源，需要你查看并有可能更改应用程序代码。

`my-private-link-speech.cognitiveservices.azure.com`本部分将使用作为语音资源 DNS 名称 (自定义域) 的示例。

##### <a name="general-principles"></a>一般原则

通常在 SDK 方案 (和文本到语音的 REST API 方案中) ，语音资源将专用的区域终结点用于不同的服务产品。 这些终结点的 DNS 名称格式为：

`{region}.{speech service offering}.speech.microsoft.com`

DNS 名称的示例如下：

`westeurope.stt.speech.microsoft.com`

 (DNS 名称) 中第一个元素的区域的所有可能值都列在 " [语音服务支持的区域](regions.md)" 中。 下表列出了 (DNS 名称) 的第二个元素的语音服务产品的可能值：

| DNS 名称值 | 语音服务产品                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [自定义命令](custom-commands.md)                       |
| `convai`       | [对话听录](conversation-transcription.md) |
| `s2s`          | [语音翻译](speech-translation.md)                 |
| `stt`          | [语音转文本](speech-to-text.md)                         |
| `tts`          | [文本转语音](text-to-speech.md)                         |
| `voice`        | [自定义语音](how-to-custom-voice.md)                      |

因此，前面的示例 (`westeurope.stt.speech.microsoft.com`) 在西欧中代表语音到文本终结点。

专用于终结点的终结点通过特定代理与语音服务通信。 因此， *你必须更改端点连接 url*。 

"标准" 终结点 URL 如下所示： <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

专用终结点 URL 如下所示： <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**示例1。** 应用程序使用以下 URL 进行通信 (语音识别使用西欧) 中的美国英语基础模型：

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

若要在语音资源的自定义域名为的情况下，将其用于启用了专用终结点的方案中 `my-private-link-speech.cognitiveservices.azure.com` ，你必须按如下所示修改 URL：

```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

请注意以下详细信息：

- 主机名 `westeurope.stt.speech.microsoft.com` 将替换为自定义域主机名 `my-private-link-speech.cognitiveservices.azure.com` 。
- 原始 DNS 名称 () 的第二个元素 `stt` 成为 URL 路径中的第一个元素，位于原始路径之前。 因此原始 URL `/speech/recognition/conversation/cognitiveservices/v1?language=en-US` 将变为 `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` 。

**示例2。** 应用程序通过使用自定义语音模型，使用以下 URL 在西欧中合成语音：
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

以下等效 URL 使用启用的专用终结点，其中语音资源的自定义域名为 `my-private-link-speech.cognitiveservices.azure.com` ：

```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

示例1中的同一原则已应用，但此时间的关键元素是 `voice` 。

##### <a name="modifying-applications"></a>修改应用程序

请按照以下步骤修改你的代码：

1. 确定应用程序终结点 URL：

   - [为应用程序启用日志记录](how-to-use-logging.md) ，并运行日志记录活动。
   - 在日志文件中，搜索 `SPEECH-ConnectionUrl` 。 在 "匹配行" 中， `value` 参数包含应用程序用于访问语音服务的完整 URL。

   示例：

   ```
   (114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
   ```

   因此，该应用程序在本示例中使用的 URL 是：

   ```
   wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
   ```

2. `SpeechConfig`使用完整的端点 URL 创建实例：

   1. 修改刚才确定的终结点，如前面的 [一般原则](#general-principles) 部分所述。

   1. 修改的实例的创建方式 `SpeechConfig` 。 您的应用程序很可能会使用如下所示的内容：
      ```csharp
      var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
      ```
      由于前面几节中所述的主机名和 URL 更改，这种方式不适用于启用了专用终结点的语音资源。 如果尝试使用启用了专用终结点的资源的密钥来运行现有应用程序而不进行任何修改，则将收到 (401) 的身份验证错误。

      若要使其正常工作，请修改如何实例化 `SpeechConfig` 类并使用 "从终结点"/"通过终结点" 初始化。 假设我们定义了以下两个变量：
      - `subscriptionKey` 包含启用了专用终结点的语音资源的键。
      - `endPoint` 包含完全 *修改* 的终结点 URL， (使用相应编程语言) 所需的类型。 在我们的示例中，此变量应包含：
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
> 不会更改终结点 URI 中指定的查询参数，即使它们是由其他 Api 设置的也是如此。 例如，如果在 URI 中将识别语言定义为查询参数 `language=en-US` ，并且还 `ru-RU` 通过相应的属性将设置为，则使用 URI 中的语言设置。 然后，有效语言就是 `en-US` 。
>
> 终结点 URI 中设置的参数始终优先。 其他 Api 只能重写终结点 URI 中未指定的参数。

进行此修改后，应用程序应使用启用了专用终结点的语音资源。 我们正在致力于对专用终结点方案进行更多的无缝支持。

### <a name="use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints"></a>使用带有自定义域名且没有专用终结点的语音资源

在本文中，我们多次指出，为语音资源启用自定义域是不 *可逆* 的。 与使用 [区域终结点名称](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)的语音服务的通信相比，此类资源将使用不同的方式进行通信。

本部分介绍如何将语音资源用于启用了自定义域名的语音资源，但 *没有* 任何具有语音服务 REST Api 和 [语音 SDK](speech-sdk.md)的专用终结点。 这可能是在私有终结点方案中曾经使用过的资源，但会删除其专用终结点。

#### <a name="dns-configuration"></a>DNS 配置

请记住，启用了私有终结点的语音资源的自定义域 DNS 名称如何 [从公用网络进行解析](#resolve-dns-from-other-networks)。 在这种情况下，IP 地址解析为虚拟网络的代理终结点。 此终结点用于将网络流量分派给启用了专用终结点的认知服务资源。

但是，删除 *所有* 资源专用终结点后 (或直接在启用自定义域名) 后，语音资源的 CNAME 记录将为重新预配。 它现在指向相应 [认知服务区域终结点](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)的 IP 地址。

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
将其与 [此部分](#resolve-dns-from-other-networks)的输出进行比较。

#### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-rest-apis"></a>具有自定义域名和无专用终结点的语音资源：使用 REST Api

##### <a name="speech-to-text-rest-api-v30"></a>从语音到文本 REST API 3。0

语音到文本 REST API 3.0 使用完全等同于 [启用了终结点的语音资源](#speech-to-text-rest-api-v30)的情况。

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>短音频和文本到语音转换的语音到文本 REST API REST API

在这种情况下，短音频的语音到文本 REST API 的使用和文本到语音处理 REST API 的使用与常规情况没有差别，对于短音频，语音到文本 REST API 例外。  (参见下面的注释。 ) 应该使用 " [语音到文本" REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) 中所述的两个 api，以获取短音频和 [文本到语音的 REST API](rest-text-to-speech.md) 文档。

> [!NOTE]
> 使用语音到文本 REST API 在自定义域方案中使用短音频时，请使用 [通过](rest-speech-to-text.md#request-headers) `Authorization` [标头](rest-speech-to-text.md#request-headers)传递的授权令牌。 通过标头将语音订阅密钥传递到特殊终结点 `Ocp-Apim-Subscription-Key` 将 *不* 起作用，并将生成错误401。

#### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-speech-sdk"></a>具有自定义域名和无专用终结点的语音资源：使用语音 SDK

如果在没有专用终结点的 *情况* 下，将 speech SDK 与启用了域的自定义语音资源一起使用，则需要对应用程序代码进行查看和可能更改。 请注意，这些更改不同于 [启用了专用终结点的语音资源](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk)的情况。 我们正在致力于对专用终结点和自定义域方案进行更多的无缝支持。

`my-private-link-speech.cognitiveservices.azure.com`本部分将使用作为语音资源 DNS 名称 (自定义域) 的示例。

在 [启用了专用于终结点的语音资源](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk)的部分中，我们介绍了如何确定终结点 URL，对其进行修改，并使其能够通过类实例的 "from 终结点"/"通过终结点" 初始化进行 `SpeechConfig` 。

但是，如果在删除所有专用终结点之后尝试运行同一应用程序 (为相应的 DNS 记录重新设置) ，会收到 (404) 的内部服务错误。 原因是 [DNS 记录](#dns-configuration) 现在指向区域认知服务终结点而不是虚拟网络代理，因此， `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` 不会在此处找到 URL 路径。

如果将应用程序以以下代码的样式回滚到的标准实例化 `SpeechConfig` ，则应用程序将终止，并出现身份验证错误 (401) ：

```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```

##### <a name="modifying-applications"></a>修改应用程序

若要让应用程序使用带有自定义域名的语音资源，而无需使用专用终结点，请执行以下步骤：

1. 请求来自认知服务 REST API 的授权令牌。 [本文](../authentication.md#authenticate-with-an-authentication-token) 介绍如何获取令牌。

   使用终结点 URL 中的自定义域名。 在我们的示例中，此 URL 为：
   ```http
   https://my-private-link-speech.cognitiveservices.azure.com/sts/v1.0/issueToken
   ```
   > [!TIP]
   > 可以在 Azure 门户中找到此 URL。 在语音资源页面上的 " **资源管理** " 组中，选择 " **密钥和终结点**"。

1. `SpeechConfig`使用在上一节中获取的授权令牌创建实例。 假设我们定义了以下变量：

   - `token`：在上一节中获取的授权标记
   - `azureRegion`：语音资源 [区域](regions.md) 的名称 (例如： `westeurope`) 
   - `outError`：仅针对 [目标 C](/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithauthorizationtokenregionerror) 大小写 () 

   创建 `SpeechConfig` 如下所示的实例：

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
> 调用方需要确保授权标记有效。 在授权令牌过期之前，调用方需要通过使用新的有效令牌调用此资源库来刷新该令牌。 由于在创建新的识别器或合成器时复制了配置值，因此新的令牌值将不适用于已创建的识别器或合成器。
>
> 为此，请设置相应的识别器或合成器的授权令牌以刷新该令牌。 如果不刷新令牌，识别器或合成器将在运行时遇到错误。

进行此修改后，你的应用程序应使用没有专用终结点的自定义域名的语音资源。

## <a name="pricing"></a>定价

有关定价详细信息，请参阅 [Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link)。

## <a name="learn-more"></a>了解更多

* [Azure 专用链接](../../private-link/private-link-overview.md)
* [语音 SDK](speech-sdk.md)
* [语音到文本 REST API](rest-speech-to-text.md)
* [文本到语音 REST API](rest-text-to-speech.md)
