---
title: 创建沉浸式阅读器资源
titleSuffix: Azure Applied AI Services
description: 本文将介绍如何使用自定义子域创建新的沉浸式阅读器资源，然后在 Azure 租户中配置 Azure AD。
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: d40c0cbf80fd62cc18fb534de1c95ff215dbf578
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778165"
---
# <a name="create-an-immersive-reader-resource-and-configure-azure-active-directory-authentication"></a>创建沉浸式阅读器资源并配置 Azure Active Directory 身份验证

在本文中，我们提供了将创建沉浸式阅读器资源和配置 Azure Active Directory (Azure AD) 身份验证的脚本。 每当创建沉浸式阅读器资源时，无论是使用此脚本还是在门户中创建，都必须使用 Azure AD 权限进行配置。 此脚本将帮助你实现此目的。

此脚本旨在为你创建和配置所有必要的沉浸式阅读器和 Azure AD 资源，只需一步即可完成。 不过，你也可以仅为现有的沉浸式阅读器资源配置 Azure AD 身份验证，例如，如果恰好在 Azure 门户中创建了一个资源。

对于某些客户，可能必须创建多个沉浸式阅读器资源，以用于开发和生产，或者可能用于部署服务的多个不同区域。 对于这些情况，可以返回并多次使用该脚本来创建不同的沉浸式阅读器资源，并使用 Azure AD 权限对其进行配置。

该脚本设计得非常灵活。 它首先会查找订阅中的现有沉浸式阅读器和 Azure AD 资源，并仅在它们不存在时根据需要进行创建。 如果是首次创建沉浸式阅读器资源，则脚本会执行所需的一切。 如果只想使用它为门户中创建的现有沉浸式阅读器资源配置 Azure AD，它也会执行此操作。 它还可用于创建和配置多个沉浸式阅读器资源。

## <a name="set-up-powershell-environment"></a>设置 PowerShell 环境

1. 首先打开 [Azure Cloud Shell](../../cloud-shell/overview.md)。 请确保在左上角下拉列表中或键入 `pwsh` 时，将 Cloud Shell 设置为 PowerShell。

1. 将以下代码片段复制并粘贴到 shell 中。

    ```azurepowershell-interactive
    function Create-ImmersiveReaderResource(
        [Parameter(Mandatory=$true, Position=0)] [String] $SubscriptionName,
        [Parameter(Mandatory=$true)] [String] $ResourceName,
        [Parameter(Mandatory=$true)] [String] $ResourceSubdomain,
        [Parameter(Mandatory=$true)] [String] $ResourceSKU,
        [Parameter(Mandatory=$true)] [String] $ResourceLocation,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupName,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupLocation,
        [Parameter(Mandatory=$true)] [String] $AADAppDisplayName="ImmersiveReaderAAD",
        [Parameter(Mandatory=$true)] [String] $AADAppIdentifierUri,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecret,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecretExpiration
    )
    {
        $unused = ''
        if (-not [System.Uri]::TryCreate($AADAppIdentifierUri, [System.UriKind]::Absolute, [ref] $unused)) {
            throw "Error: AADAppIdentifierUri must be a valid URI"
        }

        Write-Host "Setting the active subscription to '$SubscriptionName'"
        $subscriptionExists = Get-AzSubscription -SubscriptionName $SubscriptionName
        if (-not $subscriptionExists) {
            throw "Error: Subscription does not exist"
        }
        az account set --subscription $SubscriptionName

        $resourceGroupExists = az group exists --name $ResourceGroupName
        if ($resourceGroupExists -eq "false") {
            Write-Host "Resource group does not exist. Creating resource group"
            $groupResult = az group create --name $ResourceGroupName --location $ResourceGroupLocation
            if (-not $groupResult) {
                throw "Error: Failed to create resource group"
            }
            Write-Host "Resource group created successfully"
        }

        # Create an Immersive Reader resource if it doesn't already exist
        $resourceId = az cognitiveservices account show --resource-group $ResourceGroupName --name $ResourceName --query "id" -o tsv
        if (-not $resourceId) {
            Write-Host "Creating the new Immersive Reader resource '$ResourceName' (SKU '$ResourceSKU') in '$ResourceLocation' with subdomain '$ResourceSubdomain'"
            $resourceId = az cognitiveservices account create `
                            --name $ResourceName `
                            --resource-group $ResourceGroupName `
                            --kind ImmersiveReader `
                            --sku $ResourceSKU `
                            --location $ResourceLocation `
                            --custom-domain $ResourceSubdomain `
                            --query "id" `
                            -o tsv

            if (-not $resourceId) {
                throw "Error: Failed to create Immersive Reader resource"
            }
            Write-Host "Immersive Reader resource created successfully"
        }

        # Create an Azure Active Directory app if it doesn't already exist
        $clientId = az ad app show --id $AADAppIdentifierUri --query "appId" -o tsv
        if (-not $clientId) {
            Write-Host "Creating new Azure Active Directory app"
            $clientId = az ad app create --password $AADAppClientSecret --end-date "$AADAppClientSecretExpiration" --display-name $AADAppDisplayName --identifier-uris $AADAppIdentifierUri --query "appId" -o tsv

            if (-not $clientId) {
                throw "Error: Failed to create Azure Active Directory app"
            }
            Write-Host "Azure Active Directory app created successfully."
            Write-Host "NOTE: To manage your Active Directory app client secrets after this Immersive Reader Resource has been created please visit https://portal.azure.com and go to Home -> Azure Active Directory -> App Registrations -> $AADAppDisplayName -> Certificates and Secrets blade -> Client Secrets section" -ForegroundColor Yellow
        }

        # Create a service principal if it doesn't already exist
        $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv
        if (-not $principalId) {
            Write-Host "Creating new service principal"
            az ad sp create --id $clientId | Out-Null
            $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv

            if (-not $principalId) {
                throw "Error: Failed to create new service principal"
            }
            Write-Host "New service principal created successfully"
        }

        # Sleep for 5 seconds to allow the new service principal to propagate
        Write-Host "Sleeping for 5 seconds"
        Start-Sleep -Seconds 5

        Write-Host "Granting service principal access to the newly created Immersive Reader resource"
        $accessResult = az role assignment create --assignee $principalId --scope $resourceId --role "Cognitive Services User"
        if (-not $accessResult) {
            throw "Error: Failed to grant service principal access"
        }
        Write-Host "Service principal access granted successfully"

        # Grab the tenant ID, which is needed when obtaining an Azure AD token
        $tenantId = az account show --query "tenantId" -o tsv

        # Collect the information needed to obtain an Azure AD token into one object
        $result = @{}
        $result.TenantId = $tenantId
        $result.ClientId = $clientId
        $result.ClientSecret = $AADAppClientSecret
        $result.Subdomain = $ResourceSubdomain

        Write-Host "Success! " -ForegroundColor Green -NoNewline
        Write-Host "Save the following JSON object to a text file for future reference:"
        Write-Output (ConvertTo-Json $result)
    }
    ```

1. 运行函数 `Create-ImmersiveReaderResource`，并根据需要为以下 '<PARAMETER_VALUES>' 占位符提供你自己的值。

    ```azurepowershell-interactive
    Create-ImmersiveReaderResource -SubscriptionName '<SUBSCRIPTION_NAME>' -ResourceName '<RESOURCE_NAME>' -ResourceSubdomain '<RESOURCE_SUBDOMAIN>' -ResourceSKU '<RESOURCE_SKU>' -ResourceLocation '<RESOURCE_LOCATION>' -ResourceGroupName '<RESOURCE_GROUP_NAME>' -ResourceGroupLocation '<RESOURCE_GROUP_LOCATION>' -AADAppDisplayName '<AAD_APP_DISPLAY_NAME>' -AADAppIdentifierUri '<AAD_APP_IDENTIFIER_URI>' -AADAppClientSecret '<AAD_APP_CLIENT_SECRET>' -AADAppClientSecretExpiration '<AAD_APP_CLIENT_SECRET_EXPIRATION>'
    ```

    完整命令将如下所示。 为了清楚起见，我们在此处将每个参数都置于其自己的行上，这样你就可以查看整个命令。 请勿按原样复制或使用此命令。 复制并使用加上自己值的上述命令。 此示例为上面的 '<PARAMETER_VALUES>' 提供了虚拟值。 你的名称将有所不同，因为你将为这些值提出自己的名称。

    ```
    Create-ImmersiveReaderResource
        -SubscriptionName 'MyOrganizationSubscriptionName'
        -ResourceName 'MyOrganizationImmersiveReader'
        -ResourceSubdomain 'MyOrganizationImmersiveReader'
        -ResourceSKU 'S0'
        -ResourceLocation 'westus2'
        -ResourceGroupName 'MyResourceGroupName'
        -ResourceGroupLocation 'westus2'
        -AADAppDisplayName 'MyOrganizationImmersiveReaderAADApp'
        -AADAppIdentifierUri 'https://MyOrganizationImmersiveReaderAADApp'
        -AADAppClientSecret 'SomeStrongPassword'
        -AADAppClientSecretExpiration '2021-12-31'
    ```

    | 参数 | 注释 |
    | --- | --- |
    | SubscriptionName |要用于沉浸式阅读器资源的 Azure 订阅的名称。 为了创建资源，必须拥有订阅。 |
    | ResourceName |  必须为字母数字，并且可能包含“-”，但前提是“-”不是第一个或最后一个字符。 长度不能超过 63 个字符。|
    | ResourceSubdomain |沉浸式阅读器资源需要自定义子域。 当调用沉浸式阅读器服务以启动阅读器时，SDK 将使用该子域。 该子域必须在全局中独一无二。 该子域必须为字母数字，并且可能包含“-”，但前提是“-”不是第一个或最后一个字符。 长度不能超过 63 个字符。 如果该资源已存在，则可以选择此参数。 |
    | ResourceSKU |选项：`S0`（标准层）或 `S1`（教育/非盈利组织）。 请访问我们的[认知服务定价页面](https://azure.microsoft.com/pricing/details/cognitive-services/immersive-reader/)，了解有关每个可用 SKU 的详细信息。 如果该资源已存在，则可以选择此参数。 |
    | ResourceLocation |选项：`eastus`、`eastus2`、`southcentralus`、`westus`、`westus2`、`australiaeast`、`southeastasia`、`centralindia`、`japaneast`、`northeurope`、`uksouth` 和 `westeurope`。 如果该资源已存在，则可以选择此参数。 |
    | ResourceGroupName |资源是在订阅中的资源组中创建的。 提供现有资源组的名称。 如果资源组尚不存在，则将创建一个具有该名称的新资源组。 |
    | ResourceGroupLocation |如果资源组尚不存在，则需要提供一个用于创建组的位置。 要查找位置列表，请运行 `az account list-locations`。 使用返回结果的 name 属性（无空格）。 如果资源组已存在，则可以选择此参数。 |
    | AADAppDisplayName |Azure Active Directory 应用程序显示名称。 如果未找到现有的 Azure AD 应用程序，则将创建一个具有此名称的新应用程序。 如果 Azure AD 应用程序已存在，则可以选择此参数。 |
    | AADAppIdentifierUri |Azure AD 应用的 URI。 如果未找到现有的 Azure AD 应用，则将创建一个具有此 URI 的新应用。 例如 `https://immersivereaderaad-mycompany`。 |
    | AADAppClientSecret |创建的密码，稍后将在获取启动沉浸式阅读器的令牌时用于进行身份验证。 该密码的长度必须至少为 16 个字符，至少包含 1 个特殊字符和 1 个数字字符。 若要在创建此资源之后管理 Azure AD 应用程序客户密码，请访问 https://portal.azure.com 并转到“主页”->“Azure Active Directory”->“应用注册”->“`[AADAppDisplayName]`”->“证书和密码边栏选项卡”->“客户端密码部分（如下面的‘管理 Azure AD 应用程序密码’屏幕截图中所示）”。 |
    | AADAppClientSecretExpiration |`[AADAppClientSecret]` 的有效期截止日期或日期/时间（例如“2020-12-31T11:59:59+00:00”或“2020-12-31”）。 |

    管理 Azure AD 应用程序密码

    ![Azure 门户证书和密码边栏选项卡](./media/client-secrets-blade.png)

1. 将 JSON 输出复制到某个文本文件供稍后使用。 输出应如下所示。

    ```json
    {
      "TenantId": "...",
      "ClientId": "...",
      "ClientSecret": "...",
      "Subdomain": "..."
    }
    ```

## <a name="next-steps"></a>后续步骤

* 查看 [Node.js 快速入门](./quickstarts/client-libraries.md?pivots=programming-language-nodejs)，了解通过 Node.js 使用沉浸式阅读器 SDK 还可以做什么
* 查看 [Android 教程](./how-to-launch-immersive-reader.md)，了解通过 Java 或 Kotlin for Android 使用沉浸式阅读器 SDK 可执行的其他操作
* 查看 [iOS 教程](./how-to-launch-immersive-reader.md)，了解通过 Swift for iOS 使用沉浸式阅读器 SDK 可执行的其他操作
* 查看 [Python 教程](./how-to-launch-immersive-reader.md)，了解通过 Python 使用沉浸式阅读器 SDK 还可以做什么
* 浏览[沉浸式阅读器 SDK ](https://github.com/microsoft/immersive-reader-sdk)和[沉浸式阅读器 SDK 参考](./reference.md)