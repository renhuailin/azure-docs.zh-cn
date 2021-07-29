---
title: 新建 Azure Application Insights 资源 | Microsoft Docs
description: 为新的实时应用程序手动设置 Application Insights 监视。
ms.topic: conceptual
ms.date: 02/10/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 37f090f44099dc45d6c258e10b09d164277fcb47
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110077169"
---
# <a name="create-an-application-insights-resource"></a>创建 Application Insights 资源

Azure Application Insights 在 Microsoft Azure *资源* 中显示有关应用程序的数据。 因此，创建新资源属于[设置 Application Insights 来监视新应用程序][start]的一个环节。 创建新资源后，可以获取其检测密钥并使用它来配置 Application Insights SDK。 检测密钥会将遥测链接到资源。

> [!IMPORTANT]
> [经典 Application Insights 已弃用](https://azure.microsoft.com/updates/we-re-retiring-classic-application-insights-on-29-february-2024/)。 请按照以下[关于如何升级到基于工作区的 Application Insights 的说明](convert-classic-resource.md)操作。

## <a name="sign-in-to-microsoft-azure"></a>登录到 Microsoft Azure

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="create-an-application-insights-resource"></a>创建 Application Insights 资源

登录 [Azure 门户](https://portal.azure.com)，并创建 Application Insights 资源：

![单击左上角的“+”号。 选择开发人员工具，然后选择“Application Insights”](./media/create-new-resource/new-app-insights.png)

   | 设置        |  值           | 描述  |
   | ------------- |:-------------|:-----|
   | **名称**      | `Unique value` | 名称，用于标识要监视的应用。 |
   | **资源组**     | `myResourceGroup`      | 用于托管 App Insights 数据的新资源组或现有资源组的名称。 |
   | **区域** | `East US` | 选择离你近的位置或离托管应用的位置近的位置。 |
   | **资源模式** | `Classic` 或 `Workspace-based` | 基于工作区的资源可用于将 Application Insights 遥测发送到常用 Log Analytics 工作区。 有关详细信息，请参阅[有关基于工作区的资源的文章](create-workspace-resource.md)。

> [!NOTE]
> 虽然可以在不同资源组中使用相同的资源名称，但使用全局唯一名称会有好处。 如果打算[执行跨资源查询](../logs/cross-workspace-query.md#identifying-an-application)，这将很有用，因为它可以简化所需的语法。

在必填字段中输入适当的值，然后选择“查看 + 创建”。

> [!div class="mx-imgBorder"]
> ![在必填字段中输入值，然后选择“查看 + 创建”。](./media/create-new-resource/review-create.png)

创建应用后，将打开一个新窗格。 可以在此窗格中查看有关受监视应用程序的性能和使用情况数据。 

## <a name="copy-the-instrumentation-key"></a>复制检测密钥

检测密钥用于标识要与遥测数据关联的资源。 你需要复制检测密钥并将其添加到应用程序的代码中。

> [!IMPORTANT]
> 建议使用[连接字符串](./sdk-connection-string.md)替代检测密钥。 新的 Azure 区域要求使用连接字符串而不是检测密钥。 连接字符串用于标识要与遥测数据关联的资源。 它还允许你修改可供你的资源将其用作遥测目标的终结点。 你需要复制连接字符串，并将其添加到应用程序的代码或环境变量中。

## <a name="install-the-sdk-in-your-app"></a>在应用中安装 SDK

在应用中安装 Application Insights SDK。 此步骤在很大程度上依赖于应用程序的类型。

使用检测密钥来配置[在应用程序中安装的 SDK][start]。

SDK 包含无需编写任何其他代码即可发送遥测数据的标准模块。 若要跟踪用户操作或更细致地诊断问题，请[使用 API][api] 发送自己的遥测数据。

## <a name="creating-a-resource-automatically"></a>自动创建资源

### <a name="powershell"></a>PowerShell

新建 Application Insights 资源

```powershell
New-AzApplicationInsights [-ResourceGroupName] <String> [-Name] <String> [-Location] <String> [-Kind <String>]
 [-Tag <Hashtable>] [-DefaultProfile <IAzureContextContainer>] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="example"></a>示例

```powershell
New-AzApplicationInsights -Kind java -ResourceGroupName testgroup -Name test1027 -location eastus
```
#### <a name="results"></a>结果

```powershell
Id                 : /subscriptions/{subid}/resourceGroups/testgroup/providers/microsoft.insights/components/test1027
ResourceGroupName  : testgroup
Name               : test1027
Kind               : web
Location           : eastus
Type               : microsoft.insights/components
AppId              : 8323fb13-32aa-46af-b467-8355cf4f8f98
ApplicationType    : web
Tags               : {}
CreationDate       : 10/27/2017 4:56:40 PM
FlowType           :
HockeyAppId        :
HockeyAppToken     :
InstrumentationKey : 00000000-aaaa-bbbb-cccc-dddddddddddd
ProvisioningState  : Succeeded
RequestSource      : AzurePowerShell
SamplingPercentage :
TenantId           : {subid}
```

有关此 cmdlet 的完整 PowerShell 文档，以及若要了解如何检索检测密钥，请参阅 [Azure PowerShell 文档](/powershell/module/az.applicationinsights/new-azapplicationinsights)。

### <a name="azure-cli-preview"></a>Azure CLI（预览版）

若要访问预览版 Application Insights Azure CLI 命令，首先需要运行以下命令：

```azurecli
 az extension add -n application-insights
```

如果不运行 `az extension add` 命令，则会看到一条错误消息，指出：`az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

现在，可以运行以下命令来创建 Application Insights 资源：

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--kind]
                                         [--tags]
```

#### <a name="example"></a>示例

```azurecli
az monitor app-insights component create --app demoApp --location westus2 --kind web -g demoRg --application-type web
```

#### <a name="results"></a>结果

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g demoApp  --application-type web
{
  "appId": "87ba512c-e8c9-48d7-b6eb-118d4aee2697",
  "applicationId": "demoApp",
  "applicationType": "web",
  "creationDate": "2019-08-16T18:15:59.740014+00:00",
  "etag": "\"0300edb9-0000-0100-0000-5d56f2e00000\"",
  "flowType": "Bluefield",
  "hockeyAppId": null,
  "hockeyAppToken": null,
  "id": "/subscriptions/{subid}/resourceGroups/demoApp/providers/microsoft.insights/components/demoApp",
  "instrumentationKey": "00000000-aaaa-bbbb-cccc-dddddddddddd",
  "kind": "web",
  "location": "eastus",
  "name": "demoApp",
  "provisioningState": "Succeeded",
  "requestSource": "rest",
  "resourceGroup": "demoApp",
  "samplingPercentage": null,
  "tags": {},
  "tenantId": {tenantID},
  "type": "microsoft.insights/components"
}
```

有关此命令的完整 Azure CLI 文档，以及若要了解如何检索检测密钥，请参阅 [Azure CLI 文档](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_create)。

## <a name="next-steps"></a>后续步骤
* [诊断搜索](./diagnostic-search.md)
* [探索指标](../essentials/metrics-charts.md)
* [编写分析查询](../logs/log-query-overview.md)

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[diagnostic]: ./diagnostic-search.md
[metrics]: ../essentials/metrics-charts.md
[start]: ./app-insights-overview.md
