---
title: 使用 Grafana 监视 Azure 服务和应用程序
description: 路由 Azure Monitor 和 Application Insights 数据，以便在 Grafana 中进行查看。
ms.topic: conceptual
ms.date: 11/06/2017
ms.openlocfilehash: 65bd221bd2f5574db33e6c164f75bb0760dabd3b
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129812049"
---
# <a name="monitor-your-azure-services-in-grafana"></a>在 Grafana 中监控 Azure 服务
你现在可以使用 [Grafana](https://grafana.com/) 及其随附的 [Azure Monitor 数据源插件](https://grafana.com/docs/grafana/latest/datasources/azuremonitor/)监控 Azure 服务和应用程序。 该插件从三个 Azure 服务检索数据：
- 来自 Azure 资源数据的数值时序数据的 Azure Monitor 指标。 
- Azure Monitor 日志中来自 Azure 资源的日志和性能数据（可以让你使用 Kusto 查询语言 (KQL) 进行查询）。
- Azure Resource Graph，以便跨订阅快速查询和识别 Azure 资源。

然后，可以在 Grafana 仪表板上显示此性能和可用性数据。

使用以下步骤设置 Grafana 服务器、使用即开即用的 Azure Monitor 仪表板，以及生成包含来自 Azure Monitor 的指标和日志的自定义仪表板。

## <a name="set-up-a-grafana-server"></a>设置 Grafana 服务器

### <a name="set-up-grafana-locally"></a>本地设置 Grafana
若要设置本地的 Grafana 服务器，请[在本地环境下载并安装 Grafana](https://grafana.com/grafana/download)。

### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>通过 Azure 市场在 Azure 上设置 Grafana
1. 转到 Azure 市场并选取 Grafana Labs 的 Grafana。

2. 填写名称和详细信息。 创建新的资源组。 记录为 VM 用户名、VM 密码和 Grafana 服务器管理员密码选择的值。  

3. 选择 VM 大小和存储帐户。

4. 配置网络配置设置。

5. 接受使用条款之后，查看摘要，然后选择“创建”。

6. 部署完成后，选择“转到资源组”。 你将看到新创建的资源列表。

    ![Grafana 资源组对象](media/grafana-plugin/grafana-resource-group.png)

    如果选择网络安全组（在本例中为 grafana nsg），你可以看到端口 3000 用于访问 Grafana 服务器。

7. 获取 Grafana 服务器的公共 IP 地址 - 返回到资源列表，然后选择“公共 IP 地址”。

## <a name="sign-in-to-grafana"></a>登录到 Grafana

> [!IMPORTANT]
> Internet Explorer 浏览器和旧版 Microsoft Edge 浏览器与 Grafana 不兼容，必须使用基于 chromium 的浏览器（包括 Microsoft Edge）。 请参阅 [Grafana 支持的浏览器](https://grafana.com/docs/grafana/latest/installation/requirements/#supported-web-browsers)。

1. 使用服务器的 IP 地址在浏览器中打开登录页 http://\<IP address\>:3000 或 \<DNSName>\:3000。 尽管默认端口为 3000，但请注意可能在设置期间选择了其他端口。 你应看到生成的 Grafana 服务器登录页。

    ![Grafana 登录屏幕](./media/grafana-plugin/login-screen.png)

2. 使用用户名 admin 和之前创建的 Grafana 服务器管理员密码登录。 如果使用本地安装程序，默认密码将为 admin，并要求你在首次登陆时更改密码。

## <a name="configure-data-source-plugin"></a>配置数据源插件

成功登录后，应该会看到用于添加第一个数据源的选项。

![添加数据源](./media/grafana-plugin/add-data-source.png)

1. 选择“添加数据源” **，** 按名称“Azure”进行筛选，然后选择 Azure Monitor 数据源。

![Azure Monitor 数据源](./media/grafana-plugin/azure-monitor-data-source-list.png)

2. 选择数据源的名称，并选择托管标识或应用注册进行身份验证。

如果 Grafana 实例托管在已启用托管标识的 Azure VM 上，则可以使用此方法进行身份验证。 但是，若 Grafana 实例未托管在 Azure 上，或者未启用托管标识，则需要一并使用应用注册与 Azure 服务主体来设置身份验证。

### <a name="use-managed-identity"></a>使用托管标识

1. 在 VM 上启用托管标识，并将 Grafana 服务器托管标识支持设置更改为 true。
    * 托管 VM 的托管标识需要为将在 Grafana 中可视化的订阅、资源组或资源分配[监视读取者角色](/azure/azure-monitor/roles-permissions-security)。
    * 此外，还需要更新 Grafana 服务器配置中的设置“managed_identity_enabled = true”。有关详细信息，请参阅 [Grafana 配置](https://grafana.com/docs/grafana/latest/administration/configuration/)。 完成这两个步骤后，即可保存并测试访问权限。

2. 选择“保存并测试”，然后 Grafana 将会测试凭据。 应看到类似于下面的消息：  
    
   ![已批准 Grafana 数据源托管标识配置](./media/grafana-plugin/managed-identity.png)

### <a name="or-use-app-registration"></a>或使用应用注册

1. 创建服务主体 - Grafana 使用 Azure Active Directory 服务主体连接到 Azure Monitor API 并收集数据。 必须创建新的或使用现有的服务主体，以管理对 Azure 资源的访问权限。
    * 请参阅[这些说明](/azure/active-directory/develop/howto-create-service-principal-portal)以创建服务主体。 复制并保存租户 ID（目录 ID）、客户端 ID（应用程序 ID）和客户端密码（应用程序密钥值）。
    * 请参阅[为应用程序分配角色](/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application)，以便为你想要监视的订阅、资源组或资源上的 Azure Active Directory 应用程序分配[监视读取者角色](/azure/azure-monitor/roles-permissions-security)。
  
2. 详细介绍你想要使用的连接。
    * 配置插件时，可以指示插件要监控哪个 Azure 云（公共、Azure 美国政府、Azure 德国或 Azure 中国）。
        > [!NOTE]
        > 某些数据源字段的命名方式不同于其关联的 Azure 设置：
        > * 租户 ID 是 Azure 目录 ID
        > * 客户端 ID 是 Azure Active Directory 应用程序 ID
        > * 客户端密钥是 Azure Active Directory 应用程序密钥值

3. 选择“保存并测试”，然后 Grafana 将会测试凭据。 应看到类似于下面的消息：  
    
   ![已批准 Grafana 数据源应用注册配置](./media/grafana-plugin/app-registration.png)

## <a name="use-azure-monitor-data-source-dashboards"></a>使用 Azure Monitor 数据源仪表板

此 Azure Monitor 插件包括几个开箱即用仪表板，你可以导入这些仪表板以开始使用。

1. 单击 Azure Monitor 插件的仪表板选项卡，以查看可用仪表板的清单。

   ![Azure Monitor 数据源仪表板](./media/grafana-plugin/azure-data-source-dashboards.png)

2. 单击“导入”以下载仪表板。

3. 单击导入仪表板的名称以打开它。

4. 使用仪表板顶部的下拉选择器选择感兴趣的订阅、资源组和资源。

   ![存储 Insights 仪表板](./media/grafana-plugin/storage-insights-dashboard.png)

## <a name="build-grafana-dashboards"></a>构建 Grafana 仪表板

1. 转到 Grafana 主页，并选择“建立首个仪表板”。

2. 在新的仪表板中，选择“添加空面板”。

3. 仪表板上会显示一个空的“时序”面板，其中包含如下所示的查询编辑器。 选择已配置的 Azure Monitor 数据源。
   * 收集 Azure Monitor 指标 - 在服务下拉列表中选择“Azure Monitor”。 随即将显示选择器列表，可在其中选择此图表中要监视的资源和指标。 若要收集 VM 的指标，请使用命名空间 Microsoft.Compute/VirtualMachines。 选择 VM 和指标后，即可开始在仪表板中查看其数据。
     ![适用于 Azure Monitor 指标的 Grafana 图形配置](./media/grafana-plugin/metrics-config.png)
   * 收集 Azure Monitor 日志数据 - 在服务下拉列表中选择“日志”。 选择要查询的资源或 Log Analytics 工作区，并设置查询文本。 请注意，Azure Monitor 插件允许你查询特定资源或来自 Log Analytics 工作区的日志。 可以在下面的查询编辑器中复制已有的任何日志查询，也可以创建新的日志查询。 在查询中键入时，IntelliSense 将显示并建议自动完成选项。 最后，选择可视化类型“时序”，并运行查询。
    
     > [!NOTE]
     >
     > 插件提供的默认查询使用两个宏："$__timeFilter() 和 $__interval。 
     > 这些宏允许 Grafana 在你放大某部分图表时动态计算时间范围和时间粒度。 可以删除这些宏并使用标准时间筛选器（如 TimeGenerated > ago(1h)），但这意味着未来图形将不支持放大功能。
    
     以下示例显示在 Application Insights 资源中查询所有请求的平均响应时间。

     ![适用于 Azure Log Analytics 的 Grafana 图形配置](./media/grafana-plugin/logs-config.png)

    * 除上面显示的指标和日志查询，Azure Monitor 插件还支持 [Azure Resource Graph ](/azure/governance/resource-graph/concepts/explore-resources)查询。

## <a name="advanced-grafana-features"></a>高级 Grafana 功能

### <a name="variables"></a>变量
仪表板用户可以通过 UI 下拉列表选择某些资源和查询值，并在资源或查询中进行更新。
以下列显示 Log Analytics 工作区使用情况的查询为例：
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

可以配置一个变量，该变量将列出所有可用的工作区，然后根据用户选择更新查询的资源。
若要创建新的变量，请单击右上角的仪表板“设置”按钮，选择“变量”，然后单击“新建”。
在变量页上，定义要运行的数据源和查询以获取值列表。

![Grafana 定义变量](./media/grafana-plugin/define-variable.png)

创建后，请调整查询资源以使用所选值，图表亦将做出相应响应：

![使用变量进行查询](./media/grafana-plugin/query-with-variable.png)

请参阅 Azure Monitor 插件中可用[模板变量](https://grafana.com/docs/grafana/latest/datasources/azuremonitor/template-variables/)的清单。

### <a name="create-dashboard-playlists"></a>创建仪表板播放列表

仪表板播放列表是 Grafana 众多有用功能中的其中一项。 你可以创建多个仪表板，将其添加到播放列表，并配置每个仪表板的显示间隔。 导航到仪表板菜单项，然后选择“播放列表”，以创建要循环访问的现有仪表板的播放列表。 建议在大型壁式监视器上显示这些仪表板，为你的小组提供状态板。

![Grafana 播放列表示例](./media/grafana-plugin/playlist.png)

## <a name="clean-up-resources"></a>清理资源

如果已在 Azure 上设置 Grafana 环境，则无论是否在使用 VM，一旦 VM 开始运行你都需要付费。 为了避免产生额外的费用，请对此文章中创建的资源组进行清理。

1. 在 Azure 门户的左侧菜单中，单击“资源组”，然后单击“Grafana”。
2. 在资源组页上，单击“删除”，在文本框中键入“Grafana”，然后单击“删除”。

## <a name="next-steps"></a>后续步骤
* [比较 Azure Monitor 指标与日志](../data-platform.md)
