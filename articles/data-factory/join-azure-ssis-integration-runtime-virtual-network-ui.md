---
title: 使用 UI 将 Azure-SSIS Integration Runtime 加入虚拟网络
description: 了解如何使用 Azure 数据工厂工作室 UI 将 Azure-SSIS Integration Runtime 加入 Azure 虚拟网络。
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 07/16/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d02a5d1025e470d8751d87816cbcc6395be41593
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2021
ms.locfileid: "129403119"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network-with-azure-data-factory-studio-ui"></a>使用 Azure 数据工厂工作室 UI 将 Azure-SSIS Integration Runtime 加入虚拟网络

本部分介绍如何使用 Azure 门户和 Azure 数据工厂工作室 UI 将现有的 Azure-SSIS IR 加入虚拟网络（经典或 Azure 资源管理器）。 

在将 Azure-SSIS IR 加入虚拟网络之前，需要正确配置虚拟网络。 请遵循适用于你的虚拟网络类型（经典或 Azure 资源管理器）的部分中的步骤操作。 然后，遵循第三部分中的步骤将 Azure SSIS IR 加入虚拟网络。 

## <a name="configure-an-azure-resource-manager-virtual-network"></a>配置 Azure 资源管理器虚拟网络

使用门户配置一个 Azure 资源管理器虚拟网络，然后尝试将 Azure-SSIS IR 加入其中。

1. 启动 Microsoft Edge 或 Google Chrome。 目前只有这些 Web 浏览器支持数据工厂 UI。 

1. 登录到 [Azure 门户](https://portal.azure.com)。 

1. 选择“更多服务”。 筛选并选择“虚拟网络”。 

1. 在列表中筛选并选择自己的虚拟网络。 

1. 在“虚拟网络”页中选择“属性”。  

1. 选择“资源 ID”对应的复制按钮，将虚拟网络的资源 ID 复制到剪贴板。 将剪贴板中的 ID 保存到 OneNote 或某个文件中。 

1. 在左侧菜单中选择“子网”。 确保可用地址数大于 Azure-SSIS IR 中的节点数。 

1. 验证是否已将 Azure Batch 提供程序注册到包含虚拟网络的 Azure 订阅中。 或者注册 Azure Batch 提供程序。 如果订阅中已包含 Azure Batch 帐户，则已经为 Azure Batch 注册了订阅。 （如果在数据工厂门户中创建 Azure-SSIS IR，将自动注册 Azure Batch 提供程序。） 

   1. 在 Azure 门户的左侧菜单中选择“订阅”。 

   1. 选择订阅。 

   1. 在左侧选择“资源提供程序”，确认 **Microsoft.Batch** 是注册的提供程序。 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png" alt-text="确认“已注册”状态":::&quot;&quot;

   如果列表中未出现 **Microsoft.Batch**，若要注册该提供程序，请在订阅中 [创建一个空的 Azure Batch 帐户](../batch/batch-account-create-portal.md)。 稍后可以删除该帐户。 

## <a name="configure-a-classic-virtual-network"></a>配置经典虚拟网络

使用门户配置一个经典虚拟网络，然后尝试将 Azure-SSIS IR 加入其中。 

1. 启动 Microsoft Edge 或 Google Chrome。 目前只有这些 Web 浏览器支持数据工厂 UI。 

1. 登录到 [Azure 门户](https://portal.azure.com)。 

1. 选择“更多服务”。 筛选并选择“虚拟网络(经典)”。 

1. 在列表中筛选并选择自己的虚拟网络。 

1. 在“虚拟网络(经典)”页中选择“属性”。  

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png" alt-text="经典虚拟网络资源 ID":::

1. 选择“资源 ID”对应的复制按钮，将经典网络的资源 ID 复制到剪贴板。 将剪贴板中的 ID 保存到 OneNote 或某个文件中。 

1. 在左侧菜单中选择“子网”。 确保可用地址数大于 Azure-SSIS IR 中的节点数。 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png" alt-text="虚拟网络中的可用地址数":::

1. 将 **MicrosoftAzureBatch** 加入虚拟网络的“经典虚拟机参与者”角色。 

   1. 在左侧菜单中选择“访问控制(IAM)”，然后选择“角色分配”选项卡。  

       :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png" alt-text="“访问控制”和“添加”按钮":::&quot;&quot;&quot;&quot;

   1. 选择“添加角色分配”。

   1. 在“添加角色分配”页中，为“角色”选择“经典虚拟机参与者”。   在“选择”框中粘贴 **ddbf3205-c6bd-46ae-8127-60eb93363864**，然后从搜索结果列表中选择“Microsoft Azure Batch”。  

       :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png" alt-text="“添加角色分配”页上的搜索结果":::&quot;&quot;

   1. 选择“保存”以保存设置并关闭页面。 

       :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png" alt-text="保存访问设置":::

   1. 确认参与者列表中出现了“Microsoft Azure Batch”。 

       :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png" alt-text="确认 Azure Batch 访问权限":::

1. 验证是否已将 Azure Batch 提供程序注册到包含虚拟网络的 Azure 订阅中。 或者注册 Azure Batch 提供程序。 如果订阅中已包含 Azure Batch 帐户，则已经为 Azure Batch 注册了订阅。 （如果在数据工厂门户中创建 Azure-SSIS IR，将自动注册 Azure Batch 提供程序。） 

   1. 在 Azure 门户的左侧菜单中选择“订阅”。 

   1. 选择订阅。 

   1. 在左侧选择“资源提供程序”，确认 **Microsoft.Batch** 是注册的提供程序。 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png" alt-text="确认“已注册”状态":::&quot;&quot;

   如果列表中未出现 **Microsoft.Batch**，若要注册该提供程序，请在订阅中 [创建一个空的 Azure Batch 帐户](../batch/batch-account-create-portal.md)。 稍后可以删除该帐户。 

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>将 Azure-SSIS IR 加入虚拟网络

配置 Azure 资源管理器虚拟网络或经典虚拟网络后，可将 Azure-SSIS IR 加入该虚拟网络：

1. 启动 Microsoft Edge 或 Google Chrome。 目前只有这些 Web 浏览器支持数据工厂 UI。 

1. 在 [Azure 门户](https://portal.azure.com)的左侧菜单中选择“数据工厂”。 如果菜单中未显示“数据工厂”，请选择“更多服务”，然后在“智能 + 分析”部分选择“数据工厂”。    

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png" alt-text="数据工厂列表":::

1. 在列表中选择包含 Azure-SSIS IR 的数据工厂。 随后会显示该数据工厂的主页。 选择“创作和监视”磁贴。 单独的选项卡中会显示数据工厂 UI。 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png" alt-text="数据工厂主页":::

1. 在数据工厂 UI 中切换到“编辑”选项卡，选择“连接”，然后切换到“集成运行时”选项卡。   

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png" alt-text="“集成运行时”选项卡":::&quot;&quot;

1. 如果 Azure SSIS IR 正在运行，请在“集成运行时”列表中的“操作”列内，选择 Azure SSIS IR 对应的“停止”按钮。   只有在停止 Azure-SSIS IR 之后才能对其进行编辑。 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png" alt-text="停止 IR":::

1. 在“集成运行时”列表中的“操作”列内，选择 Azure SSIS IR 对应的“编辑”按钮。   

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png" alt-text="编辑集成运行时":::

1. 在集成运行时设置面板上，选择“下一步”按钮进入“常规设置”和“SQL 设置”部分。   

1. 在“高级设置”部分： 

   1. 选中“选择 Azure-SSIS Integration Runtime 要加入的 VNet，允许 ADF 创建特定网络资源，或者选择引入自己的静态公共 IP 地址”复选框。 

   1. 对于“订阅”，请选择包含你的虚拟网络的 Azure 订阅。

   1. 对于“位置”，系统已选择集成运行时所在的位置。

   1. 对于“类型”，请选择虚拟网络的类型：“经典”或“Azure 资源管理器”。 我们建议选择 Azure 资源管理器虚拟网络，因为经典虚拟网络在不久后将被弃用。

   1. 对于“VNet 名称”，请选择虚拟网络的名称。 它应该与具有虚拟网络服务终结点的 SQL 数据库或具有专用终结点的 SQL 托管实例托管 SSISDB 所用的虚拟网络相同。 或者，它应该是连接到本地网络的同一个虚拟网络。 否则，它可以是为 Azure-SSIS IR 提供你自己的静态公共 IP 地址的任何虚拟网络。

   1. 对于“子网名称”，请选择虚拟网络的子网名称。 它应该与使用虚拟网络服务终结点来托管 SSISDB 的 SQL 数据库所用的相同。 或者，它应该与具有专用终结点的 SQL 托管实例托管 SSISDB 所用的子网不同。 否则，它可以是为 Azure-SSIS IR 提供你自己的静态公共 IP 地址的任何子网。

   1. 选中“为 Azure-SSIS Integration Runtime 提供静态公共 IP 地址”复选框，以选择是否要为 Azure-SSIS IR 提供自己的静态公共 IP 地址，以便可以在数据源的防火墙中允许这些地址。

      如果选中该复选框，请完成以下步骤。

      1. 对于“第一个静态公共 IP 地址”，请选择符合 Azure-SSIS IR 的[要求](azure-ssis-integration-runtime-virtual-network-configuration.md#publicIP)的第一个静态公共 IP 地址。 如果没有任何符合要求的 IP 地址，请单击“新建”链接以在 Azure 门户中创建静态公共 IP 地址，然后单击此处的刷新按钮，以便可以选择创建的地址。
      
      1. 对于“第二个静态公共 IP 地址”，请选择符合 Azure-SSIS IR 的[要求](azure-ssis-integration-runtime-virtual-network-configuration.md#publicIP)的第二个静态公共 IP 地址。 如果没有任何符合要求的 IP 地址，请单击“新建”链接以在 Azure 门户中创建静态公共 IP 地址，然后单击此处的刷新按钮，以便可以选择创建的地址。

   1. 选择“VNet 验证”。 如果验证成功，请选择“继续”。 

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png" alt-text="使用虚拟网络的高级设置":::

1. 在“摘要”部分，复查 Azure-SSIS IR 的所有设置。 然后选择“更新”。

1. 在 Azure-SSIS IR 的“操作”列中选择“启动”按钮以启动 Azure-SSIS IR。  启动要加入虚拟网络的 Azure-SSIS IR 需要花费大约 20 到 30 分钟。 

## <a name="next-steps"></a>后续步骤
- [将 Azure-SSIS Integration Runtime 加入虚拟网络 - 概述](join-azure-ssis-integration-runtime-virtual-network.md)
- [Azure-SSIS Integration Runtime 虚拟网络配置详细信息](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [使用 Azure PowerShell 将 Azure-SSIS Integration Runtime 加入虚拟网络](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

有关 Azure-SSIS IR 的详细信息，请参阅以下文章： 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 此文提供有关 IR（包括 Azure-SSIS IR）的一般概念性信息。 
- [教程：将 SSIS 包部署到 Azure](./tutorial-deploy-ssis-packages-azure.md) 中的分步说明创建一个。 此教程提供有关创建 Azure-SSIS IR 的分步说明。 它使用 Azure SQL 数据库来托管 SSIS 目录。 
- [创建 Azure-SSIS IR](create-azure-ssis-integration-runtime.md)。 此文对本教程的内容做了扩充。 其中说明了如何使用具有虚拟网络服务终结点的 Azure SQL 数据库或虚拟网络中的 SQL 托管实例来托管 SSIS 目录。 此外，它介绍了如何将 Azure-SSIS IR 加入虚拟网络。 
- [监视 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime)。 此文介绍如何获取有关 Azure-SSIS IR 的信息。 其中提供了返回的信息的状态说明。 
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md)。 此文介绍如何停止、启动或删除 Azure-SSIS IR。 此外，介绍如何通过添加节点来扩展 Azure-SSIS IR。
