---
title: 配置专用链接
description: 配置专用链接
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 08/01/2021
ms.openlocfilehash: 9f0b1a3f51a5eae7b10ed74880c8abe1c92aae7a
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129278987"
---
# <a name="configure-your-private-link"></a>配置专用链接
配置专用链接需要几个步骤： 
* 使用资源创建专用链接范围
* 在网络上创建专用终结点，并将其连接到范围
* 在 Azure Monitor 资源上配置所需的访问权限。

本文介绍如何通过 Azure 门户完成此操作，并提供一个示例 Azure 资源管理器 (ARM) 模板，用于自动执行此过程。 

## <a name="create-a-private-link-connection-through-the-azure-portal"></a>通过 Azure 门户创建专用链接连接
在本部分，我们将通过 Azure 门户查看逐步设置专用链接的过程。 请参阅[使用 API 和命令行](#use-apis-and-command-line)，使用命令行或 Azure 资源管理器模板（ARM 模板）创建和管理专用链接。

### <a name="create-an-azure-monitor-private-link-scope"></a>创建 Azure Monitor 专用链接范围

1. 转到 Azure 门户中的“创建资源”，并搜索“Azure Monitor 专用链接范围” 。

   ![查找 Azure Monitor 专用链接范围](./media/private-link-security/ampls-find-1c.png)

2. 选择“创建”。
3. 选择一个订阅和资源组。
4. 为 AMPLS 指定名称。 最好使用有意义且清晰的名称，如“AppServerProdTelem”。
5. 选择“查看 + 创建”  。 

   ![创建 Azure Monitor 专用链接范围](./media/private-link-security/ampls-create-1d.png)

6. 让验证通过，然后选择“创建”。

### <a name="connect-azure-monitor-resources"></a>连接 Azure Monitor 资源

将 Azure Monitor 资源（Log Analytics 工作区和 Application Insights 组件）连接到 AMPLS。

1. 在 Azure Monitor 专用链接范围中，选择左侧菜单中的“Azure Monitor 资源”。 选择“添加”按钮。
2. 添加工作区或组件。 选择“添加”按钮将调出一个对话框，你可在这里选择 Azure Monitor 资源。 你可浏览订阅和资源组，也可键入其名称通过筛选找到它们。 选择工作区或组件，然后选择“应用”，让它们添加到范围中。

    ![显示选择范围 UX 的屏幕截图](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> 删除 Azure Monitor 资源需要首先取消它们与任何 AMPLS 对象之间的连接。 不能删除连接到 AMPLS 的资源。

### <a name="connect-to-a-private-endpoint"></a>连接到专用终结点

你现已将资源连接到 AMPLS，接下来可创建一个专用终结点来连接我们的网络。 可在 [Azure 门户专用链接中心](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)或 Azure Monitor 专用链接范围中执行此任务，如下例所示。

1. 在范围资源中，选择左侧资源菜单中的“专用终结点连接”。 选择“专用终结点”，启动终结点创建进程。 你还可批准在专用链接中心启动的连接，方式是将其选择并选择“批准”。

    ![专用终结点连接 UX 的屏幕截图](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. 选择订阅、资源组、终结点的名称及其应位于的区域。 区域需要与要连接到的 VNet 的所在区域相同。

3. 在完成时选择“下一步:资源”。 

4. 在“资源”屏幕中，

   a. 选择包含你的 Azure Monitor 专用范围资源的订阅。 

   b. 对于“资源类型”，选择“Microsoft.insights/privateLinkScopes” 。 

   c. 从“资源”下拉列表中，选择你之前创建的专用连接范围。 

   d. 选择“下一步: 配置>”。
      ![显示选择“创建专用终结点”的屏幕截图](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. 在“配置”窗格中，

   a.    选择要连接到 Azure Monitor 资源的虚拟网络和子网 。 
 
   b.    针对“与专用 DNS 区域集成”选择“是”，让它自动新建一个专用 DNS 区域 。 实际 DNS 区域可能不同于下面屏幕截图中显示的区域。 
   > [!NOTE]
   > 如果选择“否”，并且喜欢手动管理 DNS 记录，请首先完成专用链接设置 - 包括此专用终结点和 AMPLS 配置。 然后，根据 [Azure 专用终结点 DNS 配置](../../private-link/private-endpoint-dns.md)中的说明配置 DNS。 切勿在准备专用链接设置时创建空记录。 你创建的 DNS 记录会替代现有设置，并且会影响与 Azure Monitor 的连接。
 
   c.    选择“查看 + 创建”  。
 
   d.    让验证通过。 
 
   e.    选择“创建”  。 

    ![显示选择“专用终结点”详细信息的屏幕截图。](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

现在创建了一个连接到此 AMPLS 的新的专用终结点。


## <a name="configure-access-to-your-resources"></a>配置对资源的访问
到目前为止，你已了解网络的配置，但还应考虑如何配置对监视资源（Log Analytics 工作区和 Application Insights 组件）的网络访问。

转到 Azure 门户。 在资源菜单中，左侧有一个“网络隔离”菜单项。 此页面控制可以通过专用链接访问资源的网络，以及其他网络是否可以访问该资源。


> [!NOTE]
> 从 2021 年 9 月开始，将严格强制实施网络隔离。 如果资源设置为阻止来自公用网络的查询，并且未（通过 AMPLS）连接到任何专用网络，则将停止接受来自任何网络的查询。

![LA 网络隔离](./media/private-link-security/ampls-network-isolation.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>已连接的 Azure Monitor 专用链接范围
可在此处查看并配置资源与 Azure Monitor 专用链接范围的连接。 连接到范围 (AMPLS) 后，便可以使连接到每个 AMPLS 的虚拟网络的流量访问资源。 这种连接效果与我们在[连接 Azure Monitor 资源](#connect-azure-monitor-resources)中所做的从范围连接它的效果一样。 

若要添加新连接，请选择“添加”，然后选择 Azure Monitor专用链接范围。 选择“应用”进行连接。 资源可以连接到五个 AMPLS 对象，如[考虑使用 AMPLS 限制](./private-link-design.md#consider-ampls-limits)中所述。

### <a name="virtual-networks-access-configuration---managing-access-from-outside-of-private-links-scopes"></a>虚拟网络访问配置 - 从专用链接范围外管理访问权限
此页面底部的设置控制公用网络的访问权限，即未连接到列出的范围 (AMPLS) 的网络。

如果将“允许公用网络访问以便执行引入”设置为“否”，则已连接的范围之外的客户端（计算机、SDK 等）无法将数据上传到该资源或向该资源发送日志 。

如果将“允许公用网络访问以便执行查询”设置为“否”，则已连接的范围之外的客户端（计算机、SDK 等）无法查询该资源中的数据 。 该数据包括对以下资源的访问：日志、指标和实时指标流，以及根据工作簿、仪表板、基于查询 API 的客户端体验和 Azure 门户中的见解等构建的体验等。 对于 Azure 门户外部运行的体验，其查询 Log Analytics 数据也必须在专用链接的 VNET 中运行。


## <a name="use-apis-and-command-line"></a>使用 API 和命令行

可使用 Azure 资源管理器模板、REST、和命令行接口将前面描述的过程自动化。

### <a name="create-and-manage-azure-monitor-private-link-scopes-ampls"></a>创建和管理 Azure Monitor 专用链接范围 (AMPLS)
若要创建和管理专用链接范围，请使用 [REST API](/rest/api/monitor/privatelinkscopes(preview)/private%20link%20scoped%20resources%20(preview)) 或 [Azure CLI (az monitor private-link-scope)](/cli/azure/monitor/private-link-scope)。

#### <a name="create-ampls-with-open-access-modes---cli-example"></a>创建具有开放访问模式的 AMPLS - CLI 示例
以下 CLI 命令创建名为“my-scope”的新 AMPLS 资源，其查询和引入访问模式均设置为“打开”。
```
az resource create -g "my-resource-group" --name "my-scope" --api-version "2021-07-01-preview" --resource-type Microsoft.Insights/privateLinkScopes --properties "{\"accessModeSettings\":{\"queryAccessMode\":\"Open\", \"ingestionAccessMode\":\"Open\"}}"
```

#### <a name="create-ampls-with-mixed-access-modes---powershell-example"></a>创建具有混合访问模式的 AMPLS - PowerShell 示例
以下 PowerShell 脚本创建名为“my-scope”的新 AMPLS 资源，其查询访问模式设置为“打开”，但引入访问模式设置为“PrivateOnly”（意味着它将仅允许在 AMPLS 中引入资源）。

```
# scope details
$scopeSubscriptionId = "ab1800bd-ceac-48cd-...-..."
$scopeResourceGroup = "my-resource-group"
$scopeName = "my-scope"
$scopeProperties = @{
    accessModeSettings = @{
        queryAccessMode     = "Open"; 
        ingestionAccessMode = "PrivateOnly"
    } 
}

# login
Connect-AzAccount

# select subscription
Select-AzSubscription -SubscriptionId $scopeSubscriptionId

# create private link scope resource
$scope = New-AzResource -Location "Global" -Properties $scopeProperties -ResourceName $scopeName -ResourceType "Microsoft.Insights/privateLinkScopes" -ResourceGroupName $scopeResourceGroup -ApiVersion "2021-07-01-preview" -Force
```

#### <a name="create-ampls---azure-resource-manager-template-arm-template"></a>创建 AMPLS - Azure 资源管理器模板（ARM 模板）
以下 Azure 资源管理器模板创建以下内容：
* 一个名为“my-scope”的专用链接范围 (AMPLS)，其查询和引入访问模式设置为 Open。
* 名为“my-workspace”的 Log Analytics 工作区
* 将有范围的资源添加到名为“my-workspace-connection”的“my-scope”AMPLS

> [!NOTE]
> 确保使用新 API 版本（2021-07-01-preview 或更高版本）来创建专用链接范围对象（在下面键入“microsoft.insights/privatelinkscopes”）。 过去记录的 ARM 模板使用旧 API 版本，这会导致使用 QueryAccessMode="Open" 和 IngestionAccessMode="PrivateOnly" 设置 AMPLS。

```
{
    "$schema": https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#,
    "contentVersion": "1.0.0.0",
    "parameters": {
        "private_link_scope_name": {
            "defaultValue": "my-scope",
            "type": "String"
        },
        "workspace_name": {
            "defaultValue": "my-workspace",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.insights/privatelinkscopes",
            "apiVersion": "2021-07-01-preview",
            "name": "[parameters('private_link_scope_name')]",
            "location": "global",
            "properties": {
                "accessModeSettings":{
                    "queryAccessMode":"Open",
                    "ingestionAccessMode":"Open"
                }
            }
        },
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-10-01",
            "name": "[parameters('workspace_name')]",
            "location": "westeurope",
            "properties": {
                "sku": {
                    "name": "pergb2018"
                },
                "publicNetworkAccessForIngestion": "Enabled",
                "publicNetworkAccessForQuery": "Enabled"
            }
        },
        {
            "type": "microsoft.insights/privatelinkscopes/scopedresources",
            "apiVersion": "2019-10-17-preview",
            "name": "[concat(parameters('private_link_scope_name'), '/', concat(parameters('workspace_name'), '-connection'))]",
            "dependsOn": [
                "[resourceId('microsoft.insights/privatelinkscopes', parameters('private_link_scope_name'))]",
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspace_name'))]"
            ],
            "properties": {
                "linkedResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspace_name'))]"
            }
        }
    ]
}
```

### <a name="set-ampls-access-modes---powershell-example"></a>设置 AMPLS 访问模式 - PowerShell 示例
若要在 AMPLS 上设置访问模式标志，可以使用以下 PowerShell 脚本。 以下脚本将标志设置为“打开”。 若要使用“仅限专用”模式，请使用值“PrivateOnly”。

AMPLS 访问模式更新大约需要 10 分钟才能生效。

```
# scope details
$scopeSubscriptionId = "ab1800bd-ceac-48cd-...-..."
$scopeResourceGroup = "my-resource-group-name"
$scopeName = "my-scope"

# login
Connect-AzAccount

# select subscription
Select-AzSubscription -SubscriptionId $scopeSubscriptionId

# get private link scope resource
$scope = Get-AzResource -ResourceType Microsoft.Insights/privateLinkScopes -ResourceGroupName $scopeResourceGroup -ResourceName $scopeName -ApiVersion "2021-07-01-preview"

# set access mode settings
$scope.Properties.AccessModeSettings.QueryAccessMode = "Open";
$scope.Properties.AccessModeSettings.IngestionAccessMode = "Open";
$scope | Set-AzResource -Force
```

### <a name="set-resource-access-flags"></a>设置资源访问标志
若要管理工作区或组件访问标志，请在 [Log Analytics 工作区](/cli/azure/monitor/log-analytics/workspace)或 [Application Insights 组件](/cli/azure/ext/application-insights/monitor/app-insights/component)上使用 `[--ingestion-access {Disabled, Enabled}]` 和 `[--query-access {Disabled, Enabled}]` 标志。


## <a name="review-and-validate-your-private-link-setup"></a>查看并验证专用链接设置

### <a name="reviewing-your-endpoints-dns-settings"></a>查看终结点的 DNS 设置
你创建的专用终结点现在应配置了五个 DNS 区域：

* privatelink-monitor-azure-com
* privatelink-oms-opinsights-azure-com
* privatelink-ods-opinsights-azure-com
* privatelink-agentsvc-azure-automation-net
* privatelink-blob-core-windows-net

> [!NOTE]
> 其中每个区域将特定 Azure Monitor 终结点映射到 VNet IP 池中的专用 IP。 以下图像中显示的 IP 地址只是示例。 你的配置应显示自己网络中的专用 IP。

#### <a name="privatelink-monitor-azure-com"></a>Privatelink-monitor-azure-com
此区域涵盖 Azure Monitor 使用的全局终结点，这意味着这些终结点为考虑所有资源而不是特定资源的请求提供服务。 此区域应具有为以下终结点映射的终结点：
* `in.ai` - Application Insights 引入终结点（全局和区域条目）
* `api` - Application Insights 和 Log Analytics 终结点
* `live` - Application Insights 实时指标流终结点
* `profiler` - Application Insights 探查器终结点
* `snapshot` - Application Insights 快照终结点[![专用 DNS 区域 monitor-azure-com 的屏幕截图。](./media/private-link-security/dns-zone-privatelink-monitor-azure-com.png)](./media/private-link-security/dns-zone-privatelink-monitor-azure-com-expanded.png#lightbox)

#### <a name="privatelink-oms-opinsights-azure-com"></a>privatelink-oms-opinsights-azure-com
此区域涵盖特定工作区到 OMS 终结点的映射。 应该会看到链接到与此专用终结点连接的 AMPLS 的每个工作区的条目。
[![专用 DNS 区域 oms-opinsights-azure-com 的屏幕截图。](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-ods-opinsights-azure-com"></a>privatelink-ods-opinsights-azure-com
此区域涵盖特定工作区到 ODS 终结点的映射 - Log Analytics 的引入终结点。 应该会看到链接到与此专用终结点连接的 AMPLS 的每个工作区的条目。
[![专用 DNS 区域 ods-opinsights-azure-com 的屏幕截图。](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-agentsvc-azure-automation-net"></a>privatelink-agentsvc-azure-automation-net
此区域涵盖特定工作区到代理服务自动化终结点的映射。 应该会看到链接到与此专用终结点连接的 AMPLS 的每个工作区的条目。
[![专用 DNS 区域代理 svc-azure-automation-net 的屏幕截图。](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net.png)](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net-expanded.png#lightbox)

#### <a name="privatelink-blob-core-windows-net"></a>privatelink-blob-core-windows-net
此区域配置与全局代理的解决方案包存储帐户的连接。 通过它，代理可以下载新的或更新的解决方案包（也称为管理包）。 无论使用多少个工作区，都只需一个条目来处理 Log Analytics 代理。
[![专用 DNS 区域 blob-core-windows-net 的屏幕截图。](./media/private-link-security/dns-zone-privatelink-blob-core-windows-net.png)](./media/private-link-security/dns-zone-privatelink-blob-core-windows-net-expanded.png#lightbox)
> [!NOTE]
> 此条目仅添加到在 2021 年 4 月 19 日或之后（或从 2021 年 6 月开始在 Azure 主权云上）创建的专用链接设置。


### <a name="validating-you-are-communicating-over-a-private-link"></a>验证是否通过专用链接进行通信
* 若要验证你的请求现在是否是通过专用终结点发送的，可使用网络跟踪工具甚至浏览器来查看它们。 例如，尝试查询工作区或应用程序时，请确保将请求发送到映射到 API 终结点的专用 IP，此示例中为“172.17.0.9”。

    注意：一些浏览器可能会使用其他 DNS 设置（请参阅[浏览器 DNS 设置](./private-link-design.md#browser-dns-settings)）。 请确保应用 DNS 设置。

* 若要确保工作区或组件没有收到来自（未通过 AMPLS 连接的）公用网络的请求，请将资源的公共引入和查询标志设置为“否”，如[配置对资源的访问](#configure-access-to-your-resources)中所述。

* 在受保护网络的客户端上，使用 `nslookup` 到 DNS 区域中列出的任何终结点。 它应由 DNS 服务器解析为映射的专用 IP，而不是默认使用的公共 IP。


## <a name="next-steps"></a>后续步骤

- 了解[私有存储](private-storage.md)
- 了解[自动化的专用链接](../../automation/how-to/private-link-security.md)