---
title: 通过 Webhook 启动 Azure 自动化 Runbook
description: 本文介绍如何使用 Webhook 通过 HTTP 调用在 Azure 自动化中启动 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 07/21/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3ccdbb04942e946d251008187693ee948b960178
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124836922"
---
# <a name="start-a-runbook-from-a-webhook"></a>从 Webhook 启动 Runbook

使用 Webhook 可使外部服务在 Azure 自动化中通过单个 HTTP 请求启动特定的 Runbook。 外部服务包括 Azure DevOps Services、GitHub、Azure Monitor 日志和自定义应用程序。 此类服务可以使用 Webhook 启动 Runbook，而无需实现完整的 Azure 自动化 API。 可以将 Webhook 与[在 Azure 自动化中启动 Runbook](./start-runbooks.md) 中其他启动 Runbook 的方法进行比较。

> [!NOTE]
> 不支持使用 Webhook 来启动 Python Runbook。

![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

若要了解具有 Webhook 的 TLS 1.2 的客户端要求，请参阅[针对 Azure 自动化的 TLS 1.2](automation-managing-data.md#tls-12-for-azure-automation)。

## <a name="webhook-properties"></a>Webhook 属性

下表介绍了必须为 webhook 配置的属性。

| 属性 | 说明 |
|:--- |:--- |
| 名称 |Webhook 的名称。 可以提供任何名称，因为该名称不会公开给客户端。 它只用来标识 Azure 自动化中的 Runbook。 最好是为 Webhook 提供一个名称，该名称需要与使用它的客户端相关。 |
| URL |Webhook 的 URL。 这是客户端通过 HTTP POST 来调用的唯一地址，用于启动链接到 Webhook 的 Runbook。 它是在创建 Webhook 时自动生成的。 无法指定自定义 URL。 <br> <br> URL 包含一个允许第三方系统调用 Runbook 的安全令牌，不需要进一步进行身份验证。 因此，应该将 URL 视为密码。 出于安全原因，只能在创建 Webhook 时通过 Azure 门户查看该 URL。 请将保存在安全位置的 URL 记下来，供将来使用。 |
| 到期日期 | Webhook 的到期日期，该日期之后不能再使用它。 创建 Webhook 后，只要它没有过期，就可以修改到期日期。 |
| 已启用 | 指示 Webhook 是否在创建后默认启用的设置。 如果将此属性设置为“禁用”，则任何客户端都无法使用 Webhook。 可以在创建 Webhook 或 Webhook 创建后的任何其他时间设置此属性。 |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Webhook 启动 Runbook 时使用的参数

Webhook 可以定义 Runbook 参数的值，当 Runbook 启动时会用到这些值。 Webhook 必须包含 Runbook 的任何必需参数的值，可以包含可选参数的值。 即使在创建 Webhook 后，也可以修改为 Webhook 配置的参数值。 链接到单个 Runbook 的多个 Webhook 可以使用不同的 Runbook 参数值。 客户端在使用 Webhook 启动 Runbook 时，无法重写在 Webhook 中定义的参数值。

若要从客户端接收数据，Runbook 支持一个名为 `WebhookData` 的参数。 此参数定义包含客户端在 POST 请求中包含的数据的对象。

![WebhookData 属性](media/automation-webhooks/webhook-data-properties.png)

`WebhookData` 参数具有以下属性：

| 属性 | 说明 |
|:--- |:--- |
| WebhookName | Webhook 的名称。 |
| RequestHeader | 包含传入 POST 请求标头的哈希表。 |
| RequestBody | 传入 POST 请求的主体。 此正文保留任何数据格式，例如字符串、JSON、XML，或表单编码格式。 编写的 Runbook 必须能够与预期的数据格式配合工作。 |

无需配置 Webhook 即可支持 `WebhookData` 参数，也不需要 Runbook 来接受它。 如果 Runbook 没有定义该参数，则会忽略从客户端发送的请求的任何详细信息。

> [!NOTE]
> 调用 Webhook 时，客户端应始终存储任何参数值，以防调用失败。 如果存在网络中断或连接问题，应用程序无法检索失败的 Webhook 调用。

如果在创建 Webhook 时为 `WebhookData` 指定了值，则会在 Webhook 使用客户端 POST 请求中的数据启动 Runbook 时重写该值。 即使应用程序未在请求正文中包含任何数据，也会发生这种情况。

如果启动使用 Webhook 以外的机制定义 `WebhookData` 的 Runbook，则可以为 Runbook 识别的 `WebhookData` 提供值。 此值应该是与 `WebhookData` 参数具有相同[属性](#webhook-properties)的对象，这样 Runbook 就可以使用它，如同使用 Webhook 所传递的实际 `WebhookData` 对象一样。

例如，如果你从 Azure 门户启动以下 Runbook，并想要传递一些示例 Webhook 数据进行测试，则必须在用户界面中传递 JSON 格式的数据。

![UI 中的 WebhookData 参数](media/automation-webhooks/WebhookData-parameter-from-UI.png)

在下一个 Runbook 示例中，让我们为 `WebhookData` 定义以下属性：

* **WebhookName**：MyWebhook
* **RequestBody**: `*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

现在我们在 UI 中为 `WebhookData` 参数传递以下 JSON 对象。 下面的示例带有回车符和换行符，与从 Webhook 中传递的格式相匹配。

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![UI 中的启动 WebhookData 参数](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Azure 自动化通过 Runbook 作业记录所有输入参数的值。 因此，客户端在 Webhook 请求中提供的任何输入都将记录下来，并可供有权访问自动化作业的任何人使用。 因此，在 Webhook 调用中包括敏感信息时，应该特别小心。

## <a name="webhook-security"></a>Webhook 安全性

Webhook 的安全性取决于其 URL 的私密性，可以通过 URL 中包含的安全令牌来调用 Webhook。 只要请求是向正确的 URL 发出的，Azure 自动化就不会对此请求执行任何身份验证。 因此，客户端不应针对执行高度敏感操作的 runbook 使用 Webhook，而不使用验证请求的替代方法。

请考虑以下策略：

* 可在 runbook 中包含逻辑，以确定它是否由 Webhook 调用。 让 Runbook 检查 `WebhookData` 参数的 `WebhookName` 属性。 Runbook 还可以执行进一步的验证，只需在 `RequestHeader` 和 `RequestBody` 属性中查找特定信息即可。

* 让 Runbook 在收到 Webhook 请求时对外部条件执行某种验证。 例如，当有新的内容提交到 GitHub 存储库时，可通过 GitHub 调用 Runbook。 Runbook 在继续之前，可能会连接到 GitHub 来验证是否有新的提交内容。

* Azure 自动化支持 Azure 虚拟网络服务标记，尤其是 [GuestAndHybridManagement](../virtual-network/service-tags-overview.md)。 可以在[网络安全组](../virtual-network/network-security-groups-overview.md#security-rules)或 [Azure 防火墙](../firewall/service-tags.md)中使用服务标记来定义网络访问控制并触发 Webhook。 创建安全规则时，可以使用服务标记代替特定 IP 地址。 在规则的相应源或目标字段中指定服务标记名称（例如 GuestAndHybridManagement），可以允许或拒绝自动化服务的流量。 此服务标记不支持通过将 IP 范围限制到特定区域来实现更精细控制的方法。

## <a name="create-a-webhook"></a>创建 Webhook

Webhook 需要已发布的 runbook。 本演练使用了从[创建 Azure 自动化 runbook](./learn/powershell-runbook-managed-identity.md) 中创建的 runbook 的修改版本。 要继续本演练，请使用以下代码编辑 PowerShell runbook：

```powershell
param
(
    [Parameter(Mandatory=$false)]
    [object] $WebhookData
)

if ($WebhookData.RequestBody) { 
    $names = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        foreach ($x in $names)
        {
            $name = $x.Name
            Write-Output "Hello $name"
        }
}
else {
    Write-Output "Hello World!"
}
```

然后保存并发布修改后的 runbook。 以下示例展示了如何使用 Azure 门户、PowerShell 和 REST 创建 Webhook。

### <a name="from-the-portal"></a>从门户

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 在 Azure 门户中，导航到自动化帐户。

1. 在“流程自动化”下选择“Runbook”，打开“Runbook”页面  。

1. 从列表中选择你的 Runbook，打开“Runbook 概述”页面。

1. 选择“添加 Webhook”，打开“添加 Webhook”页面 。

   :::image type="content" source="media/automation-webhooks/add-webhook-icon.png" alt-text="突出显示“添加 Webhook”的“Runbook 概述”页面。":::

1. 在“添加 Webhook”页上，选择“创建新的 Webhook” 。

   :::image type="content" source="media/automation-webhooks/add-webhook-page-create.png" alt-text="突出显示“创建”的“添加 Webhook”页面。":::

1. 输入 Webhook 的名称。 “到期日期”字段的到期日期默认为自当前日期起的一年后。

1. 单击“复制”图标，或按 <kbd>Ctrl+C</kbd> 复制 Webhook 的 URL。 然后将 URL 保存到安全位置。

    :::image type="content" source="media/automation-webhooks/create-new-webhook.png" alt-text="突出显示“URL”的“创建 Webhook”页面。":::

    > [!IMPORTANT]
    > 一旦创建 Webhook，就不能再次检索该 URL。 请务必按上面所述对其进行复制并记录。

1. 选择“确定”，返回到“添加 Webhook”页面 。

1. 在“添加 Webhook”页上，选择“配置参数和运行设置”，打开“参数”页面  。

   :::image type="content" source="media/automation-webhooks/add-webhook-page-parameters.png" alt-text="突出显示“参数”的“添加 Webhook”页面。":::

1. 查看“参数”页面。 无需对本文中使用的示例 runbook 进行任何更改。 选择“确定”，返回到“添加 Webhook”页面 。

1. 在“添加 Webhook”页上，选择“创建” 。 Webhook 已创建，返回到“Runbook 概述”页面。

### <a name="using-powershell"></a>使用 PowerShell

1. 验证是否安装了最新版本的 PowerShell [Az 模块](/powershell/azure/new-azureps-module-az)。

1. 使用 [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) cmdlet 以交互方式登录到 Azure，并按照说明进行操作。

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    ```

1. 使用 [New-AzAutomationWebhook](/powershell/module/az.automation/new-azautomationwebhook) cmdlet 创建用于自动化 Runbook 的 Webhook。 为变量提供适当的值，然后执行脚本。

    ```powershell
    # Initialize variables with your relevant values
    $resourceGroup = "resourceGroupName"
    $automationAccount = "automationAccountName"
    $runbook = "runbookName"
    $psWebhook = "webhookName"
    
    # Create webhook
    $newWebhook = New-AzAutomationWebhook `
        -ResourceGroup $resourceGroup `
        -AutomationAccountName $automationAccount `
        -Name $psWebhook `
        -RunbookName $runbook `
        -IsEnabled $True `
        -ExpiryTime "12/31/2022" `
        -Force
    
    # Store URL in variable; reveal variable
    $uri = $newWebhook.WebhookURI
    $uri
    ```

   输出将为类似于以下格式的 URL：`https://ad7f1818-7ea9-4567-b43a.webhook.wus.azure-automation.net/webhooks?token=uTi69VZ4RCa42zfKHCeHmJa2W9fd`

1. 还可使用 PowerShell cmdlet [Get-AzAutomationWebhook](/powershell/module/az.automation/get-azautomationwebhook) 来验证 Webhook。

    ```powershell
    Get-AzAutomationWebhook `
        -ResourceGroup $resourceGroup `
        -AutomationAccountName $automationAccount `
        -Name $psWebhook
    ```

### <a name="using-rest"></a>使用 REST

PUT 命令存档在 [Webhook - 创建或更新](/rest/api/automation/webhook/create-or-update)中。 此示例使用 PowerShell cmdlet [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) 发送 PUT 请求。

1. 创建名为 `webhook.json` 的文件，然后将以下代码粘贴到其中：

    ```json
    {
      "name": "RestWebhook",
      "properties": {
        "isEnabled": true,
        "expiryTime": "2022-03-29T22:18:13.7002872Z",
        "runbook": {
          "name": "runbookName"
        }
      }
    }
    ```

   在运行代码前，将“runbook:name”属性的值修改为 runbook 的实际名称。 查看 [Webhook 属性](#webhook-properties)，获取有关这些属性的详细信息。

1. 验证是否安装了最新版本的 PowerShell [Az 模块](/powershell/azure/new-azureps-module-az)。

1. 使用 [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) cmdlet 以交互方式登录到 Azure，并按照说明进行操作。

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    ```

1. 为变量提供适当的值，然后执行脚本。

    ```powershell
    # Initialize variables
    $subscription = "subscriptionID"
    $resourceGroup = "resourceGroup"
    $automationAccount = "automationAccount"
    $runbook = "runbookName"
    $restWebhook = "webhookName"
    $file = "path\webhook.json"

    # consume file
    $body = Get-Content $file
    
    # Craft Uri
    $restURI = "https://management.azure.com/subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Automation/automationAccounts/$automationAccount/webhooks/$restWebhook`?api-version=2015-10-31"
    ```

1. 运行以下脚本以获取访问令牌。 如果访问令牌已过期，则需返回脚本。

    ```powershell
    # Obtain access token
    $azContext = Get-AzContext
    $azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
    $profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
    $token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
    $authHeader = @{
        'Content-Type'='application/json'
        'Authorization'='Bearer ' + $token.AccessToken
    }
    ```

1. 运行以下脚本，使用 REST API 创建 Webhook。

    ```powershell
    # Invoke the REST API
    # Store URL in variable; reveal variable
    $response = Invoke-RestMethod -Uri $restURI -Method Put -Headers $authHeader -Body $body
    $webhookURI = $response.properties.uri
    $webhookURI
    ```

   输出是类似于以下格式的 URL：`https://ad7f1818-7ea9-4567-b43a.webhook.wus.azure-automation.net/webhooks?token=uTi69VZ4RCa42zfKHCeHmJa2W9fd`

1. 还可使用 [Webhook - 获取](/rest/api/automation/webhook/get)来检索由 Webhook 名称标识的 Webhook。 可运行以下 PowerShell 命令：

    ```powershell
    $response = Invoke-RestMethod -Uri $restURI -Method GET -Headers $authHeader
    $response | ConvertTo-Json
    ```

## <a name="use-a-webhook"></a>使用 Webhook

此示例使用 PowerShell cmdlet [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) 将 POST 请求发送到新的 Webhook。

1. 准备将传递给 runbook 的值作为 Webhook 调用的主体。 对于相对简单的值，可按以下方式编写脚本中的值：

    ```powershell
    $Names  = @(
                @{ Name="Hawaii"},
                @{ Name="Seattle"},
                @{ Name="Florida"}
            )
    
    $body = ConvertTo-Json -InputObject $Names
    ```

1. 对于较大的集，建议使用文件。 创建名为 `names.json` 的文件，然后将以下代码粘贴到其中：

    ```json
    [
        { "Name": "Hawaii" },
        { "Name": "Florida" },
        { "Name": "Seattle" }
    ]
    ```

    在运行以下 PowerShell 命令前，将变量 `$file` 的值更改为 json 文件的实际路径。

    ```powershell
    # Revise file path with actual path
    $file = "path\names.json"
    $bodyFile = Get-Content -Path $file 
    ```

1. 运行以下 PowerShell 命令，使用 REST API 调用 Webhook。

    ```powershell
    $response = Invoke-WebRequest -Method Post -Uri $webhookURI -Body $body -UseBasicParsing
    $response
    
    $responseFile = Invoke-WebRequest -Method Post -Uri $webhookURI -Body $bodyFile -UseBasicParsing
    $responseFile
    ```

   为方便说明，对生成主体的两种不同方法进行了两次调用。 而对于生产，只使用一种方法。  输出应如下所示（只显示一个输出）：

   :::image type="content" source="media/automation-webhooks/webhook-post-output.png" alt-text="Webhook 调用的输出。":::

    客户端收到从 `POST` 请求返回的以下代码之一。

    | 代码 | 文本 | 说明 |
    |:--- |:--- |:--- |
    | 202 |已接受 |已接受该请求，并已成功将 Runbook 排队。 |
    | 400 |错误的请求 |出于以下原因之一，未接受该请求： <ul> <li>Webhook 已过期。</li> <li>Webhook 已禁用。</li> <li>URL 中的令牌无效。</li>  </ul> |
    | 404 |未找到 |出于以下原因之一，未接受该请求： <ul> <li>找不到 Webhook。</li> <li>找不到 runbook。</li> <li>找不到帐户。</li>  </ul> |
    | 500 |内部服务器错误 |URL 有效，但出现了错误。 重新提交请求。 |

    假设请求成功，Webhook 响应将包含 JSON 格式的作业 ID，如下所示。 它包含单个作业 ID，但 JSON 格式允许将来可能的增强功能。

    ```json
    {"JobIds":["<JobId>"]}
    ```

1. 使用 PowerShell cmdlet [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) 获取输出。 也可使用 [Azure 自动化 API](/rest/api/automation/job) 获取。

    ```powershell
    #isolate job ID
    $jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
    
    # Get output
    Get-AzAutomationJobOutput `
        -AutomationAccountName $automationAccount `
        -Id $jobid `
        -ResourceGroupName $resourceGroup `
        -Stream Output
    ```

   输出应如下所示：

   :::image type="content" source="media/automation-webhooks/webhook-job-output.png" alt-text="Webhook 作业的输出。":::

## <a name="update-a-webhook"></a>更新 Webhook

创建 Webhook 后，其有效期为 10 年，此期限过后，它会自动过期。 Webhook 过期后，你无法重新激活它。 只能删除并重新创建它。 对于尚未到期的 Webhook，可将其延期。 若要延长 Webhook 的有效期，请执行以下步骤。

1. 请导航到包含 Webhook 的 Runbook。
1. 在“资源”下，选择“Webhook”，然后选择需要延期的 Webhook 。
1. 在“Webhook”页中，选择新的过期日期和时间，然后选择“保存” 。

查看 API 调用 [Webhook - 更新](/rest/api/automation/webhook/update)和 PowerShell cmdlet [Set-AzAutomationWebhook](/powershell/module/az.automation/set-azautomationwebhook)，了解其他可能的修改。

## <a name="clean-up-resources"></a>清理资源

以下是一些从自动化 runbook 中删除 Webhook 的示例。

- 使用 PowerShell，可按如下所示的方式使用 [Remove-AzAutomationWebhook](/powershell/module/az.automation/remove-azautomationwebhook) cmdlet。 不返回任何输出。

    ```powershell
    Remove-AzAutomationWebhook `
        -ResourceGroup $resourceGroup `
        -AutomationAccountName $automationAccount `
        -Name $psWebhook
    ```

- 使用 REST，可按如下所示的方式使用 REST [Webhook - 删除](/rest/api/automation/webhook/delete) API。

    ```powershell
    Invoke-WebRequest -Method Delete -Uri $restURI -Headers $authHeader
    ```

   输出为 `StatusCode        : 200` 表明删除成功。

## <a name="create-runbook-and-webhook-with-arm-template"></a>使用 ARM 模板创建 runbook 和 Webhook

也可使用 [Azure 资源管理器](../azure-resource-manager/templates/overview.md)模板创建自动化 Webhook。 此示例模板会创建一个自动化帐户、四个 runbook 和一个用于指定 runbook 的 Webhook。

1. 创建名为 `webhook_deploy.json` 的文件，然后将以下代码粘贴到其中：

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "automationAccountName": {
                "type": "String",
                "metadata": {
                    "description": "Automation account name"
                }
            },
            "webhookName": {
                "type": "String",
                "metadata": {
                    "description": "Webhook Name"
                }
            },
            "runbookName": {
                "type": "String",
                "metadata": {
                    "description": "Runbook Name for which webhook will be created"
                }
            },
            "WebhookExpiryTime": {
                "type": "String",
                "metadata": {
                    "description": "Webhook Expiry time"
                }
            },
            "_artifactsLocation": {
                "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/",
                "type": "String",
                "metadata": {
                    "description": "URI to artifacts location"
                }
            }
        },
        "resources": [
            {
                "type": "Microsoft.Automation/automationAccounts",
                "apiVersion": "2020-01-13-preview",
                "name": "[parameters('automationAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "sku": {
                        "name": "Free"
                    }
                },
                "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('runbookName')]",
                        "location": "[resourceGroup().location]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "Sample Runbook",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), 'scripts/AzureAutomationTutorialPython2.py')]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "webhooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('webhookName')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]",
                            "[parameters('runbookName')]"
                        ],
                        "properties": {
                            "isEnabled": true,
                            "expiryTime": "[parameters('WebhookExpiryTime')]",
                            "runbook": {
                                "name": "[parameters('runbookName')]"
                            }
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "webhookUri": {
                "type": "String",
                "value": "[reference(parameters('webhookName')).uri]"
            }
        }
    }
    ```

1. 以下 PowerShell 代码示例从计算机部署模板。 为变量提供适当的值，然后执行脚本。

    ```powershell
    $resourceGroup = "resourceGroup"
    $templateFile = "path\webhook_deploy.json"
    $armAutomationAccount = "automationAccount"
    $armRunbook = "ARMrunbookName"
    $armWebhook = "webhookName"
    $webhookExpiryTime = "12-31-2022"
    
    New-AzResourceGroupDeployment `
        -Name "testDeployment" `
        -ResourceGroupName $resourceGroup `
        -TemplateFile $templateFile `
        -automationAccountName $armAutomationAccount `
        -runbookName $armRunbook `
        -webhookName $armWebhook `
        -WebhookExpiryTime $webhookExpiryTime
    ```

   > [!NOTE]
   > 出于安全原因，仅在首次部署模板时才返回 URI。

## <a name="next-steps"></a>后续步骤

* 若要使用警报触发 Runbook，请参阅[使用警报触发 Azure 自动化 Runbook](automation-create-alert-triggered-runbook.md)。