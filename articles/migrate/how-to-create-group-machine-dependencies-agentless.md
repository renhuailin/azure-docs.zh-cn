---
title: 在 Azure Migrate 服务器评估中设置无代理依赖项分析
description: 在 Azure Migrate 服务器评估中设置无代理依赖项分析。
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: c3aa2aea764af8469152b007e60427724fea398a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045847"
---
# <a name="analyze-server-dependencies-agentless"></a>分析服务器依赖关系 (无代理) 

本文介绍如何使用 Azure Migrate：服务器评估设置无代理依赖项分析。 [依赖关系分析](concepts-dependency-visualization.md) 有助于识别并了解服务器之间的依赖关系，以便进行评估和迁移到 Azure。

> [!IMPORTANT]
> 对于 VMware 环境中运行的服务器，无代理依赖项分析目前处于预览状态，可通过 Azure Migrate： Server 评估工具发现。
> 此预览版涵盖客户支持，可用于生产工作负荷。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="current-limitations"></a>当前限制

- 在 "依赖关系分析" 视图中，当前无法在组中添加或删除服务器。
- 一组服务器的依赖关系图当前不可用。
- 在 Azure Migrate 项目中，可以同时为1000服务器启用依赖关系数据收集。 可以通过按1000批次服务器的顺序对更多服务器进行分析。

## <a name="before-you-start"></a>准备工作

- 请确保已 [创建了一个 Azure Migrate 项目](./create-manage-projects.md) ，并在其中添加了 Azure Migrate： Server 评估工具。
- 查看 [VMware 要求](migrate-support-matrix-vmware.md#vmware-requirements) 以执行依赖关系分析。
- 在设置设备之前，请查看 [设备要求](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) 。
- 在服务器上启用依赖关系分析之前，请[查看依赖关系分析要求](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)。

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>部署和配置 Azure Migrate 设备

1. [查看](migrate-appliance.md#appliance---vmware) 部署 Azure Migrate 设备的要求。
2. 查看设备需要在 [公共](migrate-appliance.md#public-cloud-urls) 和 [政府云中](migrate-appliance.md#government-cloud-urls)访问的 Azure url。
3. [查看](migrate-appliance.md#collected-data---vmware)设备在发现和评估期间收集的数据。
4. [注意](migrate-support-matrix-vmware.md#port-access-requirements)设备的端口访问要求。
5. [部署 Azure Migrate 设备](how-to-set-up-appliance-vmware.md) 以启动发现。 若要部署设备，请下载 .OVA 模板并将其导入 VMware 以创建在 vCenter Server 中运行的服务器。 部署设备后，需要将其注册到 Azure Migrate 项目，并将其配置为启动发现。
6. 配置设备时，需要在设备配置管理器中指定以下各项：
    - 要连接到的 vCenter Server 的详细信息。
    - vCenter Server 用于发现 VMware 环境中的服务器的凭据。
    - 可以是域/Windows (非域) /Linux (非域) 凭据的服务器凭据。 [了解](add-server-credentials.md) 有关如何提供凭据的详细信息以及如何处理凭据的详细信息。

## <a name="verify-permissions"></a>验证权限

- 你需要 [创建一个 vCenter Server 只读帐户](./tutorial-discover-vmware.md#prepare-vmware) 来进行发现和评估。 只读帐户需要为 **虚拟机**  >  **来宾操作** 启用特权，以便与服务器进行交互以收集依赖关系数据。
- 你需要一个用户帐户，以便服务器评估可以访问服务器来收集依赖关系数据。 [了解](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) Windows 和 Linux 服务器的帐户要求。

### <a name="add-credentials-and-initiate-discovery"></a>添加凭据并启动发现

1. 打开设备配置管理器，完成装置的先决条件检查和注册。
2. 导航到 " **管理凭据和发现源** " 面板。
1.  在 **步骤1：提供 vCenter Server 凭据** 中，单击 " **添加凭据** " 为设备将用于发现在 vCenter Server 上运行的服务器的 vCenter Server 帐户提供凭据。
1. 在 **步骤2：提供 vCenter Server 的详细信息** 中，单击 " **添加发现源** " 以从下拉选择 "凭据的友好名称"，在 "设备配置管理器" 上指定 vCenter Server 实例面板3的 **IP 地址/FQDN** :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="以获取 vCenter Server 详细信息":::
1. 在 **步骤3：提供服务器凭据以执行软件清单、无代理依赖项分析和 SQL Server 实例和数据库的发现**，请单击 " **添加凭据** " 以提供多个服务器凭据以启动软件清单。
1. 单击 " **启动发现" 开始** vCenter Server 发现。

 VCenter Server 发现完成后，设备会 (软件清单) 开始发现已安装的应用程序、角色和功能。在软件清单过程中，将根据服务器循环访问已添加的服务器凭据，并验证是否有无代理依赖项分析。可以从门户为服务器启用无代理依赖项分析。 只能选择成功进行验证的服务器来启用无代理依赖项分析。

## <a name="start-dependency-discovery"></a>启动依赖项发现

选择要启用依赖关系发现的服务器。

1. 在 **Azure Migrate：服务器评估** 中，单击 " **发现的服务器**"。
2. 选择要查看其发现的 **设备名称** 。
1. 你可以在 " **依赖关系" (无代理 ")** 列中查看服务器的验证状态。
1. 单击 " **依赖关系分析** " 下拉。
1. 单击 " **添加服务器**"。
1. 在 " **添加服务器** " 页中，选择要在其中启用依赖关系分析的服务器。 只能在验证成功的服务器上启用依赖项映射。 下一个验证周期将在上一次验证时间戳之后24小时运行。
1. 选择服务器后，单击 " **添加服务器**"。

:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png" alt-text="启动依赖项分析":::

在服务器上启用依赖项分析后，你可以在六个小时内可视化依赖项。 如果要同时启用多个服务器进行依赖项分析，则可以使用 [PowerShell](#start-or-stop-dependency-analysis-using-powershell) 执行此操作。

## <a name="visualize-dependencies"></a>可视化依赖项

1. 在 **Azure Migrate：服务器评估** 中，单击 " **发现的服务器**"。
1. 选择要查看其发现的 **设备名称** 。
1. 搜索要查看其依赖项的服务器。
1. 在 "**依赖关系 (无代理)** " 列中，单击 "**查看依赖项**"
1. 使用 " **持续时间** " 下拉列表更改要查看其地图的时间段。
1. 展开 **客户端** 组以列出依赖于所选服务器的服务器。
1. 展开 **端口** 组以列出与所选服务器具有依赖关系的服务器。
1. 若要导航到任何从属服务器的映射视图，请单击服务器名称 > "**加载服务器映射**" " 
 :::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png" alt-text="展开服务器端口&quot; 组和 &quot;加载服务器映射":::"
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png" alt-text="展开客户端组":::

8. 展开所选服务器可查看每个依赖关系的进程级详细信息。
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png" alt-text="展开 &quot;服务器&quot; 以显示进程":::

> [!NOTE]
> 依赖关系的进程信息并非始终可用。 如果不可用，则会用标记为 "未知进程" 的进程来描述依赖关系。

## <a name="export-dependency-data"></a>导出依赖关系数据

1. 在 **Azure Migrate：服务器评估** 中，单击 " **发现的服务器**"。
2. 单击 " **依赖关系分析** " 下拉。
3. 单击 " **导出应用程序依赖项**"。
4. 在 " **导出应用程序依赖关系** " 页上，选择发现所需服务器的设备名称。
5. 选择开始时间和结束时间。 请注意，只能下载过去30天内的数据。
6. 单击 " **导出依赖项**"。

依赖关系数据以 CSV 格式导出和下载。 下载的文件包含为进行依赖关系分析而启用的所有服务器之间的依赖关系数据。 
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/export.png" alt-text="导出依赖项":::

### <a name="dependency-information"></a>依赖关系信息

导出的 CSV 中的每一行对应于在指定时间段内观察到的依赖项。

下表汇总了导出的 CSV 中的字段。 请注意，仅为启用了无代理依赖关系分析的服务器填充服务器名称、应用程序和进程字段。

**字段名称** | **详细信息**
--- | --- 
Timeslot | 观察依赖项的 timeslot。 <br/> 在当前6小时的槽上捕获依赖关系数据。
源服务器名称 | 源服务器的名称 
源应用程序 | 源服务器上的应用程序名称  
源进程 | 源服务器上的进程的名称  
目标服务器名称 | 目标服务器的名称
目标 IP | 目标服务器的 IP 地址
目标应用程序 | 目标服务器上的应用程序名称
目标进程 | 目标服务器上进程的名称  
目标端口 | 目标服务器上的端口号

## <a name="stop-dependency-discovery"></a>停止依赖项发现

选择要停止依赖关系发现的服务器。

1. 在 **Azure Migrate：服务器评估** 中，单击 " **发现的服务器**"。
1. 选择要查看其发现的 **设备名称** 。
1. 单击 " **依赖关系分析** " 下拉。
1. 单击 " **删除服务器**"。
1. 在 " **删除服务器** " 页中，选择要停止进行依赖关系分析的服务器。
1. 选择服务器后，单击 " **删除服务器**"。

如果要在多个服务器上同时停止依赖项，可以使用 [PowerShell](#start-or-stop-dependency-analysis-using-powershell) 执行此操作。

## <a name="start-or-stop-dependency-analysis-using-powershell"></a>使用 PowerShell 启动或停止依赖关系分析

从 GitHub 上的 [Azure PowerShell 示例](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) 存储库下载 PowerShell 模块。

### <a name="log-in-to-azure"></a>登录 Azure

1. 使用 Connect-AzAccount cmdlet 登录到 Azure 订阅。

    ```PowerShell
    Connect-AzAccount
    ```
    如果使用 Azure 政府版，请使用以下命令。
    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

2. 选择已在其中创建 Azure Migrate 项目的订阅 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. 导入下载的 AzMig_Dependencies PowerShell 模块

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

### <a name="enable-or-disable-dependency-data-collection"></a>启用或禁用依赖项数据收集

1. 使用以下命令获取 Azure Migrate 项目中已发现服务器的列表。 在下面的示例中，项目名称为 FabrikamDemoProject，其所属的资源组为 FabrikamDemoRG。 服务器的列表将保存在 FabrikamDemo_VMs.csv

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    在该文件中，可以看到服务器显示名称、依赖关系集合的当前状态和所有已发现服务器的 ARM ID。 

2. 若要启用或禁用依赖关系，请创建一个输入 CSV 文件。 文件需要具有标头为 "ARM ID" 的列。 CSV 文件中的任何其他标头都将被忽略。 你可以使用上一步中生成的文件创建 CSV。 创建文件的副本，保留要启用或禁用依赖关系的服务器。 

    在下面的示例中，将对输入文件 FabrikamDemo_VMs_Enable.csv 中的服务器列表启用依赖项分析。

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    在下面的示例中，将对输入文件 FabrikamDemo_VMs_Disable.csv 中的服务器列表禁用依赖项分析。

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Disable.csv -Disable
    ```

## <a name="visualize-network-connections-in-power-bi"></a>可视化 Power BI 中的网络连接

Azure Migrate 提供了一个 Power BI 模板，你可以使用它来一次可视化多个服务器的网络连接，并按进程和服务器进行筛选。 若要可视化，请按照以下说明加载包含依赖数据的 Power BI。

1. 从 GitHub 上 [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) 存储库下载 PowerShell 模块和 Power BI 模板。

2. 使用以下说明登录到 Azure： 
    - 使用 Connect-AzAccount cmdlet 登录到 Azure 订阅。

        ```PowerShell
        Connect-AzAccount
        ```

    - 如果使用 Azure 政府版，请使用以下命令。

        ```PowerShell
        Connect-AzAccount -EnvironmentName AzureUSGovernment
        ```

    - 选择已在其中创建 Azure Migrate 项目的订阅

        ```PowerShell
        select-azsubscription -subscription "Fabrikam Demo Subscription"
        ```

3. 导入下载的 AzMig_Dependencies PowerShell 模块

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

4. 运行以下命令。 此命令将下载 CSV 中的依赖关系数据，并处理它以生成可用于 Power BI 中的可视化的独特依赖项的列表。 在下面的示例中，项目名称为 FabrikamDemoProject，其所属的资源组为 FabrikamDemoRG。 将为 FabrikamAppliance 发现的服务器下载依赖项。 唯一的依赖项将保存在 FabrikamDemo_Dependencies.csv

    ```PowerShell
    Get-AzMigDependenciesAgentless -ResourceGroup FabrikamDemoRG -Appliance FabrikamAppliance -ProjectName FabrikamDemoProject -OutputCsvFile "FabrikamDemo_Dependencies.csv"
    ```

5. 打开下载的 Power BI 模板

6. 将下载的依赖项数据加载到 Power BI 中。
    - 在 Power BI 中打开模板。
    - 单击工具栏上的 " **获取数据** "。 
    - 从公共数据源选择 **文本/CSV** 。
    - 选择已下载的依赖项文件。
    - 单击“加载”。
    - 将看到一个表，其中包含 CSV 文件的名称。 可以在右侧的 "字段" 栏中查看表。 将其重命名为 AzMig_Dependencies
    - 在工具栏中单击 "刷新"。

    "网络连接" 图表和 "源服务器名称"、"目标服务器名称"、"源进程名称"、"目标进程名称" 切片器应与导入的数据一起亮起。

7. 直观显示服务器和进程筛选的网络连接的地图。 保存文件。

## <a name="next-steps"></a>后续步骤

用于评估的[组服务器](how-to-create-a-group.md)。