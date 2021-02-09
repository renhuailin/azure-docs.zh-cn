---
title: 创建连接监视器 - Azure 门户
titleSuffix: Azure Network Watcher
description: 本文介绍如何使用 Azure 门户在连接监视器中创建监视器。
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: vinigam
ms.openlocfilehash: bd13712d137ec5a1fdfa6dec8e6f6d1e0a7432cb
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833161"
---
# <a name="create-a-monitor-in-connection-monitor-by-using-the-azure-portal"></a>使用 Azure 门户在连接监视器中创建监视器

> [!IMPORTANT]
> 自2021年7月1日起，你将无法在现有工作区中添加新测试，也无法在网络性能监视器中启用新的工作区。 你还将无法在 (经典) 的连接监视器中添加新的连接监视器。 你可以继续使用在2021年7月1日之前创建的测试和连接监视器。 若要最大程度地减少对当前工作负荷的服务中断，请在2024年2月29日之前，将 [测试从网络性能监视器 ](migrate-to-connection-monitor-from-network-performance-monitor.md) 或  [从连接监视器迁移 (经典) ](migrate-to-connection-monitor-from-connection-monitor-classic.md) 升级到 Azure 网络观察程序中的新连接监视器。

了解如何使用连接监视器监视资源之间的通信。 本文介绍了如何使用 Azure 门户创建监视器。 连接监视器支持混合部署和 Azure 云部署。


## <a name="before-you-begin"></a>准备阶段 

在使用连接监视器创建的连接监视器中，可以将本地计算机和 Azure VM 添加为源。 这些连接监视器还可以监视与终结点的连接。 终结点可以位于 Azure 上，也可以位于任何其他 URL 或 IP 上。

以下是一些可助你入门的定义：

* **连接监视器资源**。 区域特定的 Azure 资源。 以下所有实体都是连接监视器资源的属性。
* **Endpoint**。 参与连接性检查的源或目标。 终结点的示例包括：
    * Azure VM。
    * Azure 虚拟网络。
    * Azure 子网。
    * 本地代理。
    * 本地子网。
    * 包括多个子网的本地自定义网络。
    * URL 和 IP。
* **测试配置**。 针对测试的特定于协议的配置。 可以根据所选协议来定义端口、阈值、测试频率和其他参数。
* **测试组**。 包含源终结点、目标终结点和测试配置的组。 连接监视器可包含多个测试组。
* **Test**。 将源终结点、目标终结点和测试配置组合在一起。 测试是可用于监视数据的最精细级别。 监视数据包括检查失败的百分比和往返时间 (RTT)。

:::image type="content" source="./media/connection-monitor-2-preview/cm-tg-2.png" alt-text="显示了连接监视器的示意图，其中定义了测试组和测试之间的关系。":::


## <a name="create-a-connection-monitor"></a>创建连接监视器

若要使用 Azure 门户在连接监视器中创建监视器，请执行以下操作：

1. 在 Azure 门户主页上，转到“网络观察程序”。
1. 在左侧窗格的“监视”部分，选择“连接监视器” 。

   你会看到在连接监视器中创建的所有连接监视器。 若要查看在经典连接监视器中创建的连接监视器，请转到“连接监视器”选项卡。

   :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="该屏幕截图显示了在连接监视器中创建的连接监视器。":::
   
    
1. 在“连接监视器”仪表板的左上角选择“创建” 。

   

1. 在“基本信息”选项卡上，为连接监视器输入信息： 
   * **连接监视器名称**：输入你的连接监视器的名称。 使用针对 Azure 资源的标准命名规则。
   * **订阅**：为连接监视器选择一个订阅。
   * **区域**：为连接监视器选择一个区域。 只能选择在此区域创建的源 VM。
   * **工作区配置**：选择一个自定义工作区或默认工作区。 你的工作区包含监视数据。
       * 若要使用默认工作区，请选择此复选框。 
       * 若要选择自定义工作区，请清除此复选框。 然后为自定义工作区选择订阅和区域。 

   :::image type="content" source="./media/connection-monitor-2-preview/create-cm-basics.png" alt-text="屏幕截图显示了连接监视器中的“基本信息”选项卡。":::
   
1. 在选项卡底部，选择“下一步：测试组”。

1. 在测试组中添加源、目标和测试配置。 若要了解如何设置测试组，请参阅[在连接监视器中创建测试组](#create-test-groups-in-a-connection-monitor)。 

   :::image type="content" source="./media/connection-monitor-2-preview/create-tg.png" alt-text="屏幕截图显示了连接监视器中的“测试组”选项卡。":::

1. 在选项卡底部，选择“下一步：创建警报”。 若要了解如何创建警报，请参阅[在连接监视器中创建警报](#create-alerts-in-connection-monitor)。

   :::image type="content" source="./media/connection-monitor-2-preview/create-alert.png" alt-text="屏幕截图显示了“创建警报”选项卡。":::

1. 在选项卡底部，选择“下一步：查看 + 创建”。

1. 先在“查看 + 创建”选项卡上查看基本信息和测试组，再创建连接监视器。 如果需要编辑连接监视器，可以返回到相应的选项卡来执行此操作。 
   :::image type="content" source="./media/connection-monitor-2-preview/review-create-cm.png" alt-text="屏幕截图显示了连接监视器中的“查看 + 创建”选项卡。":::
   > [!NOTE] 
   > “查看 + 创建”选项卡显示“连接监视器”阶段每月的成本。 目前，“当前成本/月”列未显示任何费用。 连接监视器公开上市之后，此列才会显示每月费用。 
   > 
   > 即使在“连接监视器”阶段，也仍然需要支付 Log Analytics 引入费用。

1. 准备好创建连接监视器后，在“查看 + 创建”选项卡的底部，选择“创建” 。

连接监视器在后台创建连接监视器资源。

## <a name="create-test-groups-in-a-connection-monitor"></a>在连接监视器中创建测试组

每个连接监视器中的测试组都包括按网络参数进行测试的源和目标。 它们测试的是失败检查的百分比和基于测试配置的 RTT。

在 Azure 门户中，若要在连接监视器中创建测试组，请为以下字段指定值：

* **禁用测试组**：可以选中此复选框，为测试组指定的所有源和目标禁用监视。 默认情况下，此选择处于未选中状态。
* **名称**：为测试组命名。
* **来源**：如果在 Azure VM 和本地计算机上安装了代理，则可以将前二者均指定为源。 若要了解如何为源安装代理，请参阅[安装监视代理](./connection-monitor-overview.md#install-monitoring-agents)。
   * 若要选择 Azure 代理，请选择“Azure 终结点”选项卡。此处只能看到绑定到创建连接监视器时指定的区域的 VM。 默认情况下，会将 VM 分组到它们所属的订阅中。 已折叠这些组。 
   
       可以从“订阅”级别向下钻取到层次结构中的其他级别：

      订阅 > 资源组 > VNET > 子网 > 具有代理的 VM    

      还可更改“分组依据”选择器，以便从任何其他级别启动树。 例如，如果按虚拟网络分组，则会看到层次结构（VNET > 子网 > 具有代理的 VM）中具有代理的 VM  。

       选择 VNET、子网或单个 VM 时，会将相应的资源 ID 设置为终结点。 默认情况下，具有 Azure 网络观察程序扩展的选定 VNET 或子网中的所有 VM 都参与监视。 若要减小范围，请选择特定子网或代理或更改范围属性的值。 

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-sources.png" alt-text="屏幕截图显示了连接监视器中的“添加源”窗格和“Azure 终结点”选项卡。":::

   * 若要选择本地代理，请选择 " **非 Azure 终结点** " 选项卡。默认情况下，代理按区域分组到工作区中。 所有这些工作区都配置了网络性能监视器。 
   
       如果需要将网络性能监视器添加到工作区，请通过 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview)获取。 有关如何添加网络性能监视器的信息，请参阅 [Azure Monitor 中的监视解决方案](../azure-monitor/insights/solutions.md)。 
   
       在“创建连接监视器”下的“基本信息”选项卡上，已选择默认区域。  如果更改了区域，则可以在新区域的工作区中选择代理。 可选择一个或多个代理或子网。 在“子网”视图中，可以选择要监视的特定 IP。 如果你添加多个子网，则会创建一个自定义的本地网络“OnPremises_Network_1”。 还可以通过更改“分组依据”选择器来按代理分组。

      :::image type="content" source="./media/connection-monitor-2-preview/add-non-azure-sources.png" alt-text="屏幕截图显示了连接监视器中的“添加源”窗格和“非 Azure 终结点”选项卡。":::

   * 若要选择最近使用的终结点，可以使用“最近的终结点”选项卡 
   
   * 设置好源之后，在选项卡底部选择“完成”。仍可通过在“创建测试组”视图中选择终结点来编辑基本属性（例如终结点名称）。 

* **目标**：可以通过将 Azure VM、本地计算机或任何终结点（公共 IP、URL 或 FQDN）指定为目标来监视其连接情况。 在单个测试组中，可添加 Azure VM、本地计算机、Office 365 URL、Dynamics 365 URL 和自定义终结点。

    * 若要选择 Azure VM 作为目标，请选择“Azure 终结点”选项卡。默认情况下，Azure VM 会分组到某个区域中的订阅层次结构，该区域是在“基本信息”选项卡上的“创建连接监视器”下选择的。 你可以更改区域，并从新区域中选择 Azure VM。 然后，可从订阅级别向下钻取到层次结构中的其他级别，就像设置源 Azure 终结点时可做的操作一样。

      你可以选择 VNET、子网或单个 VM，就像设置源 Azure 终结点时可做的操作一样。 选择 VNET、子网或单个 VM 时，会将相应的资源 ID 设置为终结点。 默认情况下，具有网络观察程序扩展的选定 VNET 或子网中的所有 VM 都参与监视。 若要减小范围，请选择特定子网或代理或更改范围属性的值。 

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-dests1.png" alt-text="<屏幕截图显示了“添加目标”窗格和“Azure 终结点”选项卡。>":::

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-dests2.png" alt-text="<屏幕截图显示了订阅级别的“添加目标”窗格。>":::
       
    
    * 若要选择非 Azure 代理作为目标，请选择“非 Azure 终结点”选项卡。默认情况下，代理按区域分组到工作区中。 所有这些工作区都配置了网络性能监视器。 
    
      如果需要将网络性能监视器添加到工作区，请通过 Azure 市场获取。 有关如何添加网络性能监视器的信息，请参阅 [Azure Monitor 中的监视解决方案](../azure-monitor/insights/solutions.md)。 

      在“创建连接监视器”下的“基本信息”选项卡上，已选择默认区域。 ****  ****   如果更改了区域，则可以在新区域的工作区中选择代理。 可选择一个或多个代理或子网。 在“子网”视图中，可以选择要监视的特定 IP。 如果你添加多个子网，则会创建一个自定义的本地网络“OnPremises_Network_1”。  

      :::image type="content" source="./media/connection-monitor-2-preview/add-non-azure-dest.png" alt-text="屏幕截图显示了“添加目标”窗格和“非 Azure 终结点”选项卡。":::
    
    * 若要选择公共终结点作为目标，请选择“外部地址”选项卡。终结点列表包含 Office 365 测试 URL 和 Dynamics 365 测试 URL（按名称分组）。 你还可以选择在同一连接监视器的其他测试组中创建的终结点。 
    
        若要添加终结点，请在右上角选择“添加终结点”。 然后提供终结点名称和 URL、IP 或 FQDN。

       :::image type="content" source="./media/connection-monitor-2-preview/add-endpoints.png" alt-text="屏幕截图显示了在连接监视器中的何处将公共终结点添加为目标。":::

    * 若要选择最近使用的终结点，请转至“最近使用的终结点”选项卡 ****  。
    * 选择完目标后，选择“完成”。 仍可通过在“创建测试组”视图中选择终结点来编辑基本属性（例如终结点名称）。 

* **测试配置**：可以将一个或多个测试配置添加到测试组。 使用“新配置”选项卡创建新的测试配置。或者通过“选择现有项”选项卡添加同一连接监视器的另一测试组中的一个测试配置。

    * **测试配置名称**：为测试配置命名。
    * **协议**：选择“TCP”、“ICMP”或“HTTP”。 若要将 HTTP 更改为 HTTPS，请选择“HTTP”作为协议，然后选择“443”作为端口 。
        * **创建 TCP 测试配置**：只有在“协议”列表中选择“HTTP”时，才会显示此复选框 。 选择此复选框以创建另一个测试配置，该配置使用你在配置中的其他地方指定的相同源和目标。 新的测试配置命名为 **\<name of test configuration>_networkTestConfig**。
        * **禁用跟踪路由**：当协议为 TCP 或 ICMP 时，此复选框适用。 选择此框以阻止源发现拓扑和逐跳 RTT。
    * **目标端口**：你可以提供你选择的目标端口。
        * **侦听端口**：当协议为 TCP 时，此复选框适用。 选中此复选框以打开选择的 TCP 端口（如果尚未打开）。 
    * **测试频率**：在此列表中，指定源对指定协议和端口上的目标执行 ping 操作的频率。 可以选择 30 秒、1 分钟、5 分钟、15 分钟或 30 分钟。 选择“自定义”，输入 30 秒到 30 分钟之间的其他频率。 源将根据所选的值来测试与目标的连接。 例如，如果选择 30 秒，则源每 30 秒就会至少检查一次与目标的连接情况。
    * **成功阈值**：可以在以下网络参数上设置阈值：
       * **检查失败**：设置在源使用指定条件检查到目标的连接情况时可能检查失败的百分比。 对于 TCP 或 ICMP 协议，检查失败的百分比可能会与数据包丢失的百分比相同。 对于 HTTP 协议，此值表示未接收到响应的 HTTP 请求的百分比。
       * **往返时间**：设置 RTT（以毫秒为单位），用于确定源按测试配置连接到目标所需的时间。
       
   :::image type="content" source="./media/connection-monitor-2-preview/add-test-config.png" alt-text="屏幕截图显示了在连接监视器中设置测试配置的位置。":::
       
## <a name="create-alerts-in-connection-monitor"></a>在连接监视器中创建警报

可根据测试配置中设置的阈值为失败的测试设置警报。

在 Azure 门户中，若要为连接监视器创建警报，请为以下字段指定值： 

- **创建警报**：可选中此复选框，在 Azure Monitor 中创建指标警报。 选中此复选框时，其他字段将可供编辑。 警报将根据[警报定价](https://azure.microsoft.com/pricing/details/monitor/)额外收费。 

-   “范围” > “资源” > “层次结构”：这些值根据“基本信息”选项卡上指定的值自动填充。

- **条件名称**：警报是基于 `Test Result(preview)` 指标创建的。 连接监视器测试结果失败时，警报规则将触发。 

- **操作组名称**：你可以直接输入电子邮件，也可以通过操作组创建警报。 如果直接输入电子邮件，则会创建名为“NPM Email ActionGroup”的操作组。 电子邮件 ID 将添加到该操作组中。 如果选择使用操作组，则需要选择一个以前创建的操作组。 若要了解如何创建操作组，请参阅[在 Azure 门户中创建操作组](../azure-monitor/platform/action-groups.md)。 创建警报后，你可以[管理警报](../azure-monitor/platform/alerts-metric.md#view-and-manage-with-azure-portal)。 

- **警报规则名称**：连接监视器的名称。

- **创建时启用规则**：选中此复选框可以根据条件启用警报规则。 如果要创建规则但不启用它，请禁用此复选框。 

:::image type="content" source="./media/connection-monitor-2-preview/create-alert-filled.png" alt-text="屏幕截图显示了连接监视器中的“创建警报”选项卡。":::

## <a name="scale-limits"></a>规模限制

连接监视器具有以下规模限制：

* 每个区域每个订阅的最大连接监视器数：100
* 每个连接监视器的最大测试组：20 个
* 每个连接监视器的最大源和目标：100
* 每个连接监视器的最大测试组：2 个通过 Azure 门户

## <a name="next-steps"></a>后续步骤

* 了解[如何分析监视数据并设置警报](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts)。
* 了解[如何诊断网络中的问题](./connection-monitor-overview.md#diagnose-issues-in-your-network)。
