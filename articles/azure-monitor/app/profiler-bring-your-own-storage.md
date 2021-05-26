---
title: 为 Profiler 和 Snapshot Debugger 配置 BYOS（自带存储）
description: 为 Profiler 和 Snapshot Debugger 配置 BYOS（自带存储）
ms.topic: conceptual
author: renatosalas
ms.author: regutier
ms.date: 01/14/2021
ms.reviewer: mbullwin
ms.openlocfilehash: 830e0904d6aa905a621b245adae2b2d94b46e243
ms.sourcegitcommit: 42ac9d148cc3e9a1c0d771bc5eea632d8c70b92a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/13/2021
ms.locfileid: "109847159"
---
# <a name="configure-bring-your-own-storage-byos-for-application-insights-profiler-and-snapshot-debugger"></a>为 Application Insights Profiler 和 Snapshot Debugger 配置自带存储 (BYOS)

## <a name="what-is-bring-your-own-storage-byos-and-why-might-i-need-it"></a>什么是自带存储 (BYOS)，为何需要使用它？ 
当你使用 Application Insights Profiler 或 Snapshot Debugger 时，应用程序生成的项目将通过公共 Internet 上传到 Azure 存储帐户。 这些帐户由 Microsoft 付费并控制，以用于处理和分析。 Microsoft 将控制这些项目的静态加密和生存期管理策略。

如果使用自带存储，这些项目将上传到由你控制的存储帐户。 这意味着，静态加密策略、生存期管理策略和网络访问将由你控制。 但需支付与该存储帐户相关的费用。

> [!NOTE]
> 如果你正在启用专用链接，则必须使用自带存储。 有关 Application Insights 专用链接的详细信息，请[参阅文档](../logs/private-link-security.md)。
>
> 如果你正在启用客户管理的密钥，则必须使用自带存储。 有关 Application Insights 客户管理的密钥的详细信息，请[参阅文档](../logs/customer-managed-keys.md)。

## <a name="how-will-my-storage-account-be-accessed"></a>如何访问我的存储帐户？
1. 在虚拟机或应用服务中运行的代理会将项目（配置文件、快照和符号）上传到你的帐户中的 Blob 容器。 此过程涉及到联系 Application Insights Profiler 或 Snapshot Debugger 服务，以获取一个 SAS（共享访问签名）令牌用于访问存储帐户中的新 Blob。
1. Application Insights Profiler 或 Snapshot Debugger 服务将分析传入的 Blob，并将分析结果和日志文件写回 Blob 存储。 根据可用的计算容量，此过程可能在上传后随时发生。
1. 查看 Profiler 跟踪或 Snapshot Debugger 分析时，服务将从 Blob 存储提取分析结果。

## <a name="prerequisites"></a>先决条件
* 请确保在 Application Insights 资源所在的同一位置创建存储帐户。 例如： 如果 Application Insights 资源位于“美国西部 2”区域，则你的存储帐户也必须位于“美国西部 2”区域。 
* 通过“访问控制(IAM)”UI，在存储帐户中向 AAD 应用程序“Diagnostic Services Trusted Storage Access”授予“存储 Blob 数据参与者”角色。
* 如果已启用专用链接，请配置附加设置以允许从你的虚拟网络连接到我们的受信任 Microsoft 服务。 

## <a name="how-to-enable-byos"></a>如何启用 BYOS

### <a name="create-storage-account"></a>创建存储帐户
在 Application Insights 资源所在的同一位置创建全新的存储帐户（如果没有的话）。
如果 Application Insights 资源位于 `West US 2`，则你的存储帐户也必须位于 `West US 2`。

### <a name="grant-access-to-diagnostic-services-to-your-storage-account"></a>向诊断服务授予对存储帐户的访问权限
BYOS 存储帐户将链接到 Application Insights 资源。 每个 Application Insights 资源只能有一个存储帐户，且两者必须位于同一位置。 可以将同一个存储帐户用于多个 Application Insights 资源。

首先，需要向 Application Insights Profiler 和 Snapshot Debugger 服务授予对存储帐户的访问权限。 若要授予访问权限，请如图 1.0 所示，通过存储帐户中的“访问控制(IAM)”页将角色 `Storage Blob Data Contributor` 添加到名为 `Diagnostic Services Trusted Storage Access` 的 AAD 应用程序。 

步骤： 
1. 在“添加角色分配”部分单击“添加”按钮 
1. 选择“存储 Blob 数据参与者”角色 
1. 在“分配访问权限至”部分，选择“Azure AD 用户、组或服务主体” 
1. 搜索并选择“Diagnostic Services Trusted Storage Access”应用 
1. 保存更改

_![图 1.0](media/profiler-bring-your-own-storage/figure-10.png)_
_图 1.0_ 

添加角色后，该角色将显示在“角色分配”部分下，如下面的图 1.1 所示。 
_![图 1.1](media/profiler-bring-your-own-storage/figure-11.png)_
_图 1.1_ 

如果你还使用了专用链接，则需要额外完成一项配置，以允许从你的虚拟网络连接到我们的受信任 Microsoft 服务。 请参阅[存储网络安全文档](../../storage/common/storage-network-security.md#trusted-microsoft-services)。

### <a name="link-your-storage-account-with-your-application-insights-resource"></a>将存储帐户链接到 Application Insights 资源
若要为代码级诊断 (Profiler/Debugger) 配置 BYOS，可采用三种方法：

* 使用 Azure PowerShell cmdlet
* 使用 Azure 命令行接口 (CLI)
* 使用 Azure 资源管理器模板

#### <a name="configure-using-azure-powershell-cmdlets"></a>使用 Azure PowerShell Cmdlet 进行配置

1. 确保已安装 Az PowerShell 4.2.0 或更高版本。

    若要安装 Azure PowerShell，请参阅[官方的 Azure PowerShell 文档](/powershell/azure/install-az-ps)。

1. 安装 Application Insights PowerShell 扩展。
    ```powershell
    Install-Module -Name Az.ApplicationInsights -Force
    ```

1. 使用你的 Azure 帐户登录
    ```powershell
    Connect-AzAccount -Subscription "{subscription_id}"
    ```

    有关如何登录的详细信息，请参阅 [Connect-AzAccount 文档](/powershell/module/az.accounts/connect-azaccount)。

1. 删除已链接到 Application Insights 资源的旧存储帐户。

    模式：
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{application_insights_name}"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

    示例：
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

1. 将你的存储帐户连接到 Application Insights 资源。
    
    模式：
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "{resource_group_name}" -Name "{storage_account_name}"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{application_insights_name}"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

    示例：
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "byos-test" -Name "byosteststoragewestus2"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

#### <a name="configure-using-azure-cli"></a>使用 Azure CLI 进行配置

1. 确保已安装 Azure CLI。

    若要安装 Azure CLI，请参阅[官方的 Azure CLI 文档](/cli/azure/install-azure-cli)。

1. 安装 Application Insights CLI 扩展。
    ```azurecli
    az extension add -n application-insights
    ```

1. 将你的存储帐户连接到 Application Insights 资源。

    模式：
    ```azurecli
    az monitor app-insights component linked-storage link --resource-group "{resource_group_name}" --app "{application_insights_name}" --storage-account "{storage_account_name}"
    ```
    
    示例：
    ```azurecli
    az monitor app-insights component linked-storage link --resource-group "byos-test" --app "byos-test-westus2-ai" --storage-account "byosteststoragewestus2"
    ```
    
    预期输出：
    ```powershell
    {
      "id": "/subscriptions/{subscription}/resourcegroups/byos-test/providers/microsoft.insights/components/byos-test-westus2-ai/linkedstorageaccounts/serviceprofiler",
      "linkedStorageAccount": "/subscriptions/{subscription}/resourceGroups/byos-test/providers/Microsoft.Storage/storageAccounts/byosteststoragewestus2",
      "name": "serviceprofiler",
      "resourceGroup": "byos-test",
      "type": "microsoft.insights/components/linkedstorageaccounts"
    }
    ```

    > [!NOTE]
    > 若要对链接到 Application Insights 资源的存储帐户执行更新，请参阅 [Application Insights CLI 文档](/cli/azure/monitor/app-insights/component/linked-storage)。

#### <a name="configure-using-azure-resource-manager-template"></a>使用 Azure 资源管理器模板进行配置

1. 创建包含以下内容的 Azure 资源管理器模板文件 (byos.template.json)。
    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "applicationinsights_name": {
          "type": "String"
        },
        "storageaccount_name": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [
        {
          "name": "[concat(parameters('applicationinsights_name'), '/serviceprofiler')]",
          "type": "Microsoft.Insights/components/linkedStorageAccounts",
          "apiVersion": "2020-03-01-preview",
          "properties": {
            "linkedStorageAccount": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageaccount_name'))]"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. 运行以下 PowerShell 命令以部署上述模板（创建链接的存储帐户）。

    模式：
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "{your_resource_name}" -TemplateFile "{local_path_to_arm_template}"
    ```

    示例：
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "byos-test" -TemplateFile "D:\Docs\byos.template.json"
    ```

1. PowerShell 控制台中出现提示时，请提供以下参数：
    
    |           参数           |                                描述                               |
    |-------------------------------|--------------------------------------------------------------------------|
    | application_insights_name     | 要启用 BYOS 的 Application Insights 资源的名称。            |
    | storage_account_name          | 要用作 BYOS 的存储帐户资源的名称。 |
    
    预期输出：
    ```powershell
    Supply values for the following parameters:
    (Type !? for Help.)
    application_insights_name: byos-test-westus2-ai
    storage_account_name: byosteststoragewestus2
    
    DeploymentName          : byos.template
    ResourceGroupName       : byos-test
    ProvisioningState       : Succeeded
    Timestamp               : 4/16/2020 1:24:57 AM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name                            Type                       Value
                              ==============================  =========================  ==========
                              application_insights_name        String                     byos-test-westus2-ai
                              storage_account_name             String                     byosteststoragewestus2
                              
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

1. 通过 Azure 门户在所需的工作负载上启用代码级诊断 (Profiler/Debugger)。 （“应用服务”>“Application Insights”） _![图 2.0](media/profiler-bring-your-own-storage/figure-20.png)_
_图 2.0_

## <a name="troubleshooting"></a>故障排除
### <a name="template-schema-schema_uri-isnt-supported"></a>不支持模板架构 '{schema_uri}'。
* 确保模板的 `$schema` 属性有效。 它必须遵循以下模式：`https://schema.management.azure.com/schemas/{schema_version}/deploymentTemplate.json#`
* 确保模板的 `schema_version` 在有效值的范围内：`2014-04-01-preview, 2015-01-01, 2018-05-01, 2019-04-01, 2019-08-01`。
    错误消息：
    ```powershell
    New-AzResourceGroupDeployment : 11:53:49 AM - Error: Code=InvalidTemplate; Message=Deployment template validation failed: 'Template schema
    'https://schema.management.azure.com/schemas/2020-01-01/deploymentTemplate.json#' is not supported. Supported versions are
    '2014-04-01-preview,2015-01-01,2018-05-01,2019-04-01,2019-08-01'. Please see https://aka.ms/arm-template for usage details.'.
    ```

### <a name="no-registered-resource-provider-found-for-location-location"></a>在位置 '{location}' 处找不到任何已注册的资源提供程序。
* 确保资源 `microsoft.insights/components` 的 `apiVersion` 为 `2015-05-01`。
* 确保资源 `linkedStorageAccount` 的 `apiVersion` 为 `2020-03-01-preview`。
    错误消息：
    ```powershell
    New-AzResourceGroupDeployment : 6:18:03 PM - Resource microsoft.insights/components 'byos-test-westus2-ai' failed with message '{
      "error": {
        "code": "NoRegisteredProviderFound",
        "message": "No registered resource provider found for location 'westus2' and API version '2020-03-01-preview' for type 'components'. The supported api-versions are '2014-04-01,
    2014-08-01, 2014-12-01-preview, 2015-05-01, 2018-05-01-preview'. The supported locations are ', eastus, southcentralus, northeurope, westeurope, southeastasia, westus2, uksouth,
    canadacentral, centralindia, japaneast, australiaeast, koreacentral, francecentral, centralus, eastus2, eastasia, westus, southafricanorth, northcentralus, brazilsouth, switzerlandnorth,
    australiasoutheast'."
      }
    }'
    ```
### <a name="storage-account-location-should-match-ai-component-location"></a>存储帐户位置应与 AI 组件位置匹配。
* 确保 Application Insights 资源的位置与存储帐户的位置相同。
    错误消息：
    ```powershell
    New-AzResourceGroupDeployment : 1:01:12 PM - Resource microsoft.insights/components/linkedStorageAccounts 'byos-test-centralus-ai/serviceprofiler' failed with message '{
      "error": {
        "code": "BadRequest",
        "message": "Storage account location should match AI component location",
        "innererror": {
          "trace": [
            "System.ArgumentException"
          ]
        }
      }
    }'
    ```

有关 Profiler 的一般故障排除方法，请参阅 [Profiler 故障排除文档](profiler-troubleshooting.md)。

有关 Snapshot Debugger 的一般故障排除方法，请参阅 [Snapshot Debugger 故障排除文档](snapshot-debugger-troubleshoot.md)。 

## <a name="faqs"></a>常见问题
* 如果我启用了 Profiler 或 Snapshot Debugger，然后又启用了 BYOS，那么我的数据是否会迁移到我的存储帐户中？
    不会。

* BYOS 是否可与静态加密和客户管理的密钥配合使用？
    是的，确切地说，必须使用 BYOS 才能在 Profiler/Debugger 中启用客户管理的密钥。

* BYOS 是否可在与 Internet 隔离的环境中工作？
    是的。事实上，在隔离网络方案中必须使用 BYOS。

* 客户管理的密钥和专用链接都已启用时，BYOS 是否可正常工作？ 
    是的，可以。

* 如果我启用了 BYOS，可以重新使用诊断服务存储帐户来存储我收集的数据？ 
    可以，但目前不支持从 BYOS 迁移数据。

* 启用 BYOS 后，我是否要承担所有与之相关的费用（即存储和网络费用）？ 
    _是_
