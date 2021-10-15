---
title: Azure 客户使用情况归因
description: 大致了解如何在商业市场以及合作伙伴开发的其他可部署 IP 上跟踪 Azure 应用程序的客户使用情况。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/04/2021
ms.custom: devx-track-terraform, devx-track-azurepowershell
ms.openlocfilehash: 4c6eaf47035d78a283b4e27bf86e1550ad7279d1
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129457043"
---
# <a name="azure-customer-usage-attribution"></a>Azure 客户使用情况归因

客户使用情况归因可将部署你的 IP 时创建的客户订阅中 Azure 资源的使用情况与你（作为合作伙伴）关联起来。 在内部 Microsoft 系统中形成这些关联可以更好地了解运行软件的 Azure 内存占用情况。 对于[商业市场中的 Azure 应用程序产品/服务](#commercial-marketplace-azure-apps)，此跟踪功能可帮助你与 Microsoft 销售团队保持一致，并获得 Microsoft 合作伙伴计划的信用额度。 客户使用情况归因不适用于[商业市场中的 Azure 虚拟机产品/服务](marketplace-virtual-machines.md)。 对于虚拟机产品/服务，市场发布者无需执行任何操作，即可确保在最终客户订阅中跟踪其 Azure 使用量。

客户使用情况归因支持三个部署选项：

1. Azure 资源管理器模板（Azure 应用的常见基础，在商业市场中也称为“解决方案模板”或“托管应用”）：合作伙伴创建资源管理器模板来定义其 Azure 解决方案的基础结构和配置。 利用资源管理器模板，客户能以一致且可重复的状态部署解决方案的资源。
1. Azure 资源管理器 API：合作伙伴可以调用资源管理器 API 来部署资源管理器模板，或直接预配 Azure 服务。
1. Terraform：合作伙伴可以使用 Terraform 部署资源管理器模板或直接部署 Azure 服务。

[本文后面部分](#other-use-cases)介绍了客户使用情况归因在商业市场之外的次要用例。

>[!IMPORTANT]
>- 客户使用情况归因不是为了跟踪系统集成商、托管服务提供商或主要用于部署和管理 Azure 服务的工具的工作。
>- 客户使用情况归因适用于新部署，不支持跟踪已部署的资源。
>- 并非所有 Azure 服务都与客户使用归属兼容。 Azure Kubernetes 服务 (AKS)、VM 规模集和 Azure Batch 存在导致低报使用量的已知问题。

## <a name="commercial-marketplace-azure-apps"></a>商业市场 Azure 应用

从发布到商业市场的 Azure 应用中跟踪 Azure 使用情况主要是自动进行的。 在[市场 Azure 应用计划的技术配置](./azure-app-solution.md#define-the-technical-configuration)中上传资源管理器模板时，合作伙伴中心将添加 Azure 资源管理器可读的跟踪 ID。

>[!NOTE]
>若要确保在系统中正确归因应用程序的使用情况，请执行以下操作：
>1. 如果在资源类型 Microsoft.Resources/deployment 中使用变量定义跟踪 ID，请在计划的“技术配置”页中将该变量替换为“合作伙伴中心”显示的跟踪 ID（请参阅下面的[向资源管理器模板添加 GUID](#add-a-guid-to-a-resource-manager-template)）。
>2. 如果资源管理器模板使用类型为 Microsoft.Resources/deployments 的资源不是为了进行客户使用情况归因，Microsoft 将无法代表你添加客户使用情况归因跟踪 ID。 请在计划的“技术配置”页中添加类型为 Microsoft.Resources/deployments 的新资源，并添加在“合作伙伴中心”显示的跟踪 ID（请参阅下面的[向资源管理器模板添加 GUID](#add-a-guid-to-a-resource-manager-template)）。

如果使用 Azure 资源管理器 API，将需要根据[以下说明](#use-resource-manager-apis)添加跟踪 ID，以在代码部署资源时将其传递给 Azure 资源管理器。 此 ID 在“合作伙伴中心”中你的计划的“技术配置”页上显示。 

> [!NOTE]
> 对于现有 Azure 应用，一次性迁移从 2021 年 3 月开始，以更新每个计划的技术配置中的跟踪 ID。 这些产品/服务的过去部署中的使用情况将继续在 Microsoft 系统中进行跟踪。
>
>更新产品/服务时，不再需要在主模板文件中添加“Microsoft.Resources/deployments”资源类型。

## <a name="other-use-cases"></a>其他用例

可以使用客户使用情况归因来跟踪商业市场中未提供的解决方案的 Azure 使用情况。 这些解决方案通常位于快速入门存储库和专用 GitHub 存储库中，或者来自可创建持久 IP 的 1:1 客户约定（例如可部署的可缩放应用）。

需要执行几个手动步骤：

1. 创建一个或多个 GUID 以用作跟踪 ID。
1. 在合作伙伴中心注册这些 GUID。
1. 将已注册的 GUID 添加到 Azure 应用和/或用户代理字符串中。

### <a name="create-guids"></a>创建 GUID

与合作伙伴中心代表你为商业市场中的 Azure 应用创建的跟踪 ID 不同，CUA 的其他用例要求创建一个 GUID 来用作跟踪 ID。 GUID 是由 32 位十六进制数字组成的唯一参考标识符。 若要创建用于跟踪的 GUID，应使用 GUID 生成器（例如，通过 PowerShell）：

```powershell
[guid]::NewGuid()
```

应为每个产品和分销渠道创建唯一的 GUID。 如果不希望拆分报告，则可以选择对产品的多个分销渠道使用一个 GUID。 报告通过 Microsoft 合作伙伴网络 ID 和 GUID 进行。

### <a name="register-guids"></a>注册 GUID

接下来必须在合作伙伴中心注册 GUID，以便它们可以与你（作为合作伙伴）关联起来：

1. 登录到[合作伙伴中心](https://go.microsoft.com/fwlink/?linkid=2165507)。

1. 注册为[商业市场发布者](https://go.microsoft.com/fwlink/?linkid=2165614)。

1. 在右上角选择“设置”（齿轮图标），然后选择“帐户设置” 。

1. 选择“组织配置文件” > “标识符” > “添加跟踪 GUID”  。

1. 在“GUID”框中，输入跟踪 GUID。 只输入 GUID，不要包括 `pid-` 前缀。 在“描述”框中，输入解决方案名称或描述。

1. 若要注册多个 GUID，请再次选择“添加跟踪 GUID”。 页面上会显示其他框。

1. 选择“保存”。

### <a name="add-a-guid-to-a-resource-manager-template"></a>向资源管理器模板添加 GUID

若要将已注册的 GUID 添加到资源管理器模板，请对主模板文件执行一项修改：

1. 打开资源管理器模板。

1. 在主模板文件中添加 [Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments) 类型的新资源。 资源只需位于 mainTemplate.json 或 azuredeploy.json 文件中，不需要位于任何嵌套的或链接的模板中 。

1. 在 `pid-` 前缀后输入 GUID 值作为资源名称。 例如，如果 GUID 为 eb7927c8-dd66-43e1-b0cf-c346a422063，则资源名称将为 pid-eb7927c8-dd66-43e1-b0cf-c346a422063。 示例：
 
```json
{ // add this resource to the resources section in the mainTemplate.json
    "apiVersion": "2020-06-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your generated GUID here
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "resources": []
        }
    }
} // remove all comments from the file when complete
```
4. 检查模板是否存在错误。

1. 在相应存储库中重新发布模板。

1. [在模板部署中验证 GUID 是否成功](#verify-deployments-tracked-with-a-guid)。

> [!TIP]
> 有关创建和发布资源管理器模板的详细信息，请参阅：[创建和部署第一个资源管理器模板](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)。

### <a name="verify-deployments-tracked-with-a-guid"></a>验证使用 GUID 跟踪的部署

修改模板并运行测试部署后，使用以下 PowerShell 脚本检索已部署和标记的资源。

可以使用该脚本来验证 GUID 是否已成功添加到资源管理器模板。 该脚本不适用于资源管理器 API 或 Terraform 部署。

登录 Azure。 选择包含要在运行脚本之前验证的部署的订阅。 在部署的订阅上下文中运行该脚本。

部署的 GUID（下称“deploymentName”）和 resourceGroupName 名称是必需的参数 。

可在 GitHub 上获取[原始脚本](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1)。

```powershell
Param(
    [string][Parameter(Mandatory=$true)]$deploymentName, # the full name of the deployment, e.g. pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the named deployment
$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name "$deploymentName").correlationId

# Find all deployments with that correlationId
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in all deployments with that correlationId as PowerShell doesn't surface outputResources on the deployment or correlationId on the deploymentOperation

foreach ($deployment in $deployments){
    # Get deploymentOperations by deploymentName
    # then the resourceIds for each resource
    ($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.targetResource -notlike "*Microsoft.Resources/deployments*"}).TargetResource
}
```

### <a name="notify-your-customers"></a>通知客户

合作伙伴应告知客户有关使用客户使用情况归因的部署情况。 Microsoft 将向合作伙伴报告与这些部署关联的 Azure 使用情况。 以下示例包含可用于向客户告知这些部署的内容。 请将示例中的 \<PARTNER> 替换为你的公司名称。 合作伙伴应确保通知与其数据隐私和收集政策保持一致，包括供客户选择将自己从跟踪中排除的选项。

#### <a name="notification-for-resource-manager-template-deployments"></a>有关资源管理器模板部署的通知

部署此模板时，Microsoft 可根据部署的 Azure 资源识别 \<PARTNER> 软件的安装。 Microsoft 可关联用于支持该软件的这些 Azure 资源。 Microsoft 收集此信息的目的在于提供其产品的最佳体验及运营其业务。 此数据将根据 Microsoft 的隐私政策进行收集和管理，隐私政策位于 [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter)。

#### <a name="notification-for-sdk-or-api-deployments"></a>有关 SDK 或 API 部署的通知

部署 \<PARTNER> 软件时，Microsoft 可根据部署的 Azure 资源识别 \<PARTNER> 软件的安装。 Microsoft 可关联用于支持该软件的这些 Azure 资源。 Microsoft 收集此信息的目的在于提供其产品的最佳体验及运营其业务。 此数据将根据 Microsoft 的隐私政策进行收集和管理，隐私政策位于 [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter)。

## <a name="use-resource-manager-apis"></a>使用资源管理器 API

在某些情况下，你可能直接调用资源管理器 REST API 来部署 Azure 服务。 [Azure 支持使用多个 SDK](../index.yml?pivot=sdkstools) 来启用这些调用。 你可以使用其中一个 SDK，也可以直接调用 REST API 来部署资源。

要启用客户使用情况归因，在设计 API 调用时，请在请求的用户代理标头中包含跟踪 ID。 将字符串的格式设置为采用 `pid-` 前缀。 示例：

```xml
//Commercial Marketplace Azure app
pid-contoso-myoffer-partnercenter //copy the tracking ID exactly as it appears in Partner Center

//Other use cases
pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4 //enter your GUID after "pid-"
```
> [!IMPORTANT]
> 如果将资源管理器 API 与商业市场中的 Azure 应用一起使用，请使用合作伙伴中心提供的跟踪 ID。 请勿使用 GUID。

各种 SDK 以不同方式与资源管理器 API 交互，并且所需代码有一些差异。 下面的示例介绍了使用 GUID 的非商业市场方法，还介绍了多种更常用的 Azure SDK。

#### <a name="example-python-sdk"></a>示例：Python SDK

对于 Python，请使用 **config** 属性。 只能将该属性添加到 UserAgent。 示例：

```python
client = azure.mgmt.servicebus.ServiceBusManagementClient(**parameters)
client.config.add_user_agent("pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4")
```
> [!IMPORTANT]
> 将属性添加每个客户端。 没有全局的静态配置。 可以标记一个客户端工厂来确保每个客户端都在跟踪。 有关详细信息，请参阅 [GitHub 上的此客户端工厂示例](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79)。

#### <a name="example-net-sdk"></a>示例：.NET SDK

对于 .NET，请确保设置用户代理。 通过以下代码（以在 C# 中为例）使用 [Microsoft.Azure.Management.Fluent](/dotnet/api/microsoft.azure.management.fluent) 库设置用户代理：

```csharp
var azure = Microsoft.Azure.Management.Fluent.Azure
    .Configure()
    // Add your pid in the user agent header
    .WithUserAgent("pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", String.Empty) 
    .Authenticate(/* Credentials created via Microsoft.Azure.Management.ResourceManager.Fluent.SdkContext.AzureCredentialsFactory */)
    .WithSubscription("<subscription ID>");
```

#### <a name="example-azure-powershell"></a>示例：Azure PowerShell

如果通过 Azure PowerShell 部署资源，请使用此方法追加 GUID：

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="example-azure-cli"></a>示例：Azure CLI

使用 Azure CLI 追加 GUID 时，请在脚本作用域内设置 AZURE_HTTP_USER_AGENT 环境变量。 还可以针对 shell 全局范围设置变量：

```powershell
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

有关详细信息，请参阅 [Azure SDK for Go](/azure/developer/go/)。

## <a name="use-terraform"></a>使用 Terraform

对 Terraform 的支持通过 Azure 提供程序 1.21.0 版本提供：[https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019)。 这适用于通过 Terraform 部署解决方案的所有合作伙伴，以及由 Azure 提供程序（版本 1.21.0 或更高版本）部署和按流量计费的所有资源。

适用于 Terraform 的 Azure 提供程序添加了一个新的可选字段 [partner_id](https://www.terraform.io/docs/providers/azurerm/#partner_id)，可指定用于解决方案的跟踪 GUID。 此字段的值还可来源于 *ARM_PARTNER_ID* 环境变量。

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
将 partner_id 的值设置为已注册的 GUID。 不要在 GUID 前面加上“pid-”前缀，将其设置为实际 GUID 即可。

> [!IMPORTANT]
> 如果将 Terraform 与商业市场中的 Azure 应用一起使用，请使用合作伙伴中心提供的整个跟踪 ID。 请勿使用 GUID。

## <a name="get-support"></a>获取支持

若要了解商业市场中的支持选项，请参阅[合作伙伴中心中商业市场计划的支持](support.md)。

### <a name="how-to-submit-a-technical-consultation-request"></a>如何提交技术咨询请求

1. 访问[合作伙伴技术服务](https://aka.ms/TechnicalJourney)。
1. 选择“云基础设施和管理”，让你一览技术之旅。
1. 选择“部署服务” > “提交请求” 。
1. 使用 MSA（MPN 帐户）或 AAD（合作伙伴仪表板帐户）登录。
1. 完成/查看打开的表单上的联系信息。 可以预先填充咨询详细信息，也可以使用下拉选项。
1. 输入问题的标题和详细说明。
1. 选择“提交”。

通过[使用技术预售和部署服务](/partner-center/technical-benefits)上的屏幕截图了解分步说明。

Microsoft 合作伙伴技术顾问将电话联系你，确定你的需求。

## <a name="report"></a>报表
通过客户使用情况归因跟踪的 Azure 使用情况的报告目前对 ISV 合作伙伴不可用。 将报告添加到合作伙伴中心内的商业市场计划以涵盖客户使用情况归因的功能计划在 2022 年下半年推出。

## <a name="faq"></a>常见问题解答

#### <a name="after-a-tracking-id-is-added-can-it-be-changed"></a>添加跟踪 ID 后是否可对其进行更改？

商业市场中的 Azure 应用的跟踪 ID 由合作伙伴中心自动管理。 不过，客户可以下载模板，还可更改或删除跟踪 ID。 合作伙伴应主动向其客户介绍跟踪 ID 的作用，以防删除或编辑。 更改跟踪 ID 只会影响新的部署和资源，不会影响现有部署和资源。

#### <a name="can-i-track-templates-deployed-from-a-non-microsoft-repository-like-github"></a>我是否可以跟踪通过非 Microsoft 存储库（如 GitHub）部署的模板？

可以，只要部署模板时存在跟踪 ID，就会跟踪使用情况。 若要维持你（作为发行商）与从非 Microsoft 存储库部署的模板之间的关联，请首先从 Azure 门户中你的产品/服务的商业市场列表中下载已发布模板的副本，其中将包含跟踪 ID。 将该版本发布到 GitHub 或其他非 Microsoft 存储库。

如果该模板未在商业市场中列出，并且包含已注册的 GUID，请确保在发布到 GitHub 或其他非 Microsoft 存储库的版本中存在 GUID。

#### <a name="does-the-customer-receive-reporting-as-well"></a>客户是否也会收到报告？

否。 客户可在 Azure 门户中跟踪所有资源或资源组的使用情况。 客户看不到按 CUA 跟踪 ID 细分的使用情况。

#### <a name="is-customer-usage-attribution-similar-to-the-digital-partner-of-record-dpor-or-partner-admin-link-pal"></a>客户使用情况是否类似于记录合作伙伴 (DPOR) 或合作伙伴管理链接 (PAL)？

客户使用归因是一种机制，用于将 Azure 使用情况与合作伙伴的可重复、可部署 IP 进行关联 - 在部署时形成关联。 在咨询（系统集成商）或管理（托管服务提供商）合作伙伴与客户互动时，DPOR 和 PAL 旨在将该合作伙伴与客户的相关 Azure 占用情况进行关联。
