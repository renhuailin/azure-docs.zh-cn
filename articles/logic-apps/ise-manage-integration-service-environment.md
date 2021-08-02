---
title: 在 Azure 逻辑应用中管理集成服务环境
description: 在适用于 Azure 逻辑应用的集成服务环境 (ISE) 中查看网络运行状况并管理逻辑应用、连接、自定义连接器和集成帐户
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 10/25/2020
ms.openlocfilehash: f4cc844cc12383d5ccc3a3a12f48431d34b4872d
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111984817"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>在 Azure 逻辑应用中管理集成服务环境 (ISE)

本文介绍如何为[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 执行管理任务，例如：

* 管理 ISE 中的资源，如基于多租户的逻辑应用、连接、集成帐户和连接器。

* 检查 ISE 的网络运行状况。

* 若要添加容量、重启 ISE 或删除 ISE，请按本主题中的步骤操作。 若要将这些项目添加到 ISE，请参阅[将项目添加到集成服务环境](../logic-apps/add-artifacts-integration-service-environment-ise.md)。

## <a name="view-your-ise"></a>查看 ISE

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在门户的搜索框中，输入“集成服务环境”，然后选择“集成服务环境”。

   ![查找集成服务环境](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. 从结果列表中，选择你的集成服务环境。

   ![选择集成服务环境](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. 继续进行到下一部分，以查找 ISE 中的逻辑应用、连接、连接器或集成帐户。

<a name="check-network-health"></a>

## <a name="check-network-health"></a>检查网络运行状况

在 ISE 菜单上的“设置”下，选择“网络运行状况” 。 此窗格显示子网的运行状况和其他服务的出站依赖关系。

![检查网络运行状况](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

> [!CAUTION]
> 如果 ISE 的网络变得不正常，则 ISE 使用的内部应用服务环境 (ASE) 也可能会变得不正常。 如果 ASE 运行状态不正常的时间超过7天，则会挂起该 ASE。 若要解决此状态问题，请检查虚拟网络设置。 解决发现的任何问题，然后重启 ISE。 否则，在 90 天后，挂起的 ASE 会被删除，而你的 ISE 将不可用。 因此，请确保 ISE 始终处于正常状态，以允许必要的流量。
> 
> 有关详细信息，请参阅以下主题：
>
> * [Azure 应用服务诊断概述](../app-service/overview-diagnostics.md)
> * [Azure 应用服务环境的消息日志记录](../app-service/environment/using-an-ase.md#logging)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>管理逻辑应用

可以查看和管理 ISE 中的逻辑应用。

1. 在 ISE 菜单的“设置”下，选择“逻辑应用” 。

   ![查看逻辑应用](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. 若要删除 ISE 中不再需要的逻辑应用，请选择这些逻辑应用，然后选择“删除”。 选择“是”以确认要删除。

> [!NOTE]
> 如果删除并重新创建了子逻辑应用，则必须重新保存父逻辑应用。 重新创建的子应用将具有不同的元数据。
> 如果在重新创建父逻辑应用的子逻辑应用后未重新保存父逻辑应用，则对子逻辑应用的调用将失败，并出现“未授权”错误。 此行为适用于父子逻辑应用，例如，那些使用集成帐户中的项目或调用 Azure 函数的逻辑应用。

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>管理 API 连接

可以查看和管理在 ISE 中运行的逻辑应用创建的连接。

1. 在 ISE 菜单的“设置”下，选择“API 连接” 。

   ![查看 API 连接](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. 若要删除 ISE 中不再需要的连接，请选择这些连接，然后选择“删除”。 选择“是”以确认要删除。

<a name="manage-api-connectors"></a>

## <a name="manage-ise-connectors"></a>管理 ISE 连接器

可以查看和管理部署到 ISE 的 API 连接器。

1. 在 ISE 菜单的“设置”下，选择“托管连接器” 。

   ![查看托管连接器](./media/ise-manage-integration-service-environment/ise-view-managed-connectors.png)

1. 若要删除你不希望在 ISE 中可用的连接器，请选择这些连接器，然后选择“删除”。 选择“是”以确认要删除。

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>管理自定义连接器

可以查看和管理部署到 ISE 的自定义连接器。

1. 在 ISE 菜单的“设置”下，选择“自定义连接器” 。

   ![查找自定义连接器](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. 若要删除 ISE 中不再需要的自定义连接器，请选择这些连接器，然后选择“删除”。 选择“是”以确认要删除。

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>管理集成帐户

1. 在 ISE 菜单的“设置”下，选择“集成帐户” 。

   ![查找集成帐户](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. 若要删除 ISE 中不再需要的集成帐户，请选择这些集成帐户，然后选择“删除”。

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>添加 ISE 容量

高级 ISE 基本单元有固定容量，因此，如果需要更多的吞吐量，可在创建期间或之后添加更多缩放单元。 开发人员 SKU 不包括添加缩放单元的功能。

1. 在 [Azure 门户](https://portal.azure.com)中，转到你的 ISE。

1. 若要查看 ISE 的使用情况和性能指标，请在 ISE 菜单中选择“概述”。

   ![查看 ISE 的使用情况](./media/ise-manage-integration-service-environment/integration-service-environment-usage.png)

1. 在“设置”下，选择“横向扩展” 。在“配置”窗格中，从以下选项中选择：

   * [手动缩放](#manual-scale)：根据要使用的处理单元数进行缩放。
   * [自定义自动缩放](#custom-autoscale)：通过从各种条件中进行选择并指定满足条件的阈值条件，基于性能指标进行缩放。

   ![显示“横向扩展”页的屏幕截图，其中“手动缩放”处于选中状态。](./media/ise-manage-integration-service-environment/select-scale-out-options.png)

<a name="manual-scale"></a>

### <a name="manual-scale"></a>手动缩放

1. 选择“手动缩放”后，在“其他容量”中选择要使用的缩放单元数 。

   ![选择所需缩放类型](./media/ise-manage-integration-service-environment/select-manual-scale-out-units.png)

1. 完成后，选择“保存”。

<a name="custom-autoscale"></a>

### <a name="custom-autoscale"></a>自定义自动缩放

1. 选择“自定义自动缩放”后，为“自动缩放设置名称”提供名称，并且可以根据需要选择设置所属的 Azure 资源组 。

   ![提供自动缩放设置的名称并选择资源组](./media/ise-manage-integration-service-environment/select-custom-autoscale.png)

1. 对于“默认”条件，请选择“基于指标缩放”或“缩放到特定实例计数”  。

   * 如果选择基于实例，请输入处理单元的数量，该值介于 0 到 10 之间。

   * 如果选择基于指标，请执行以下步骤：

     1. 在“规则”部分，单击“添加规则” 。

     1. 在“缩放规则”窗格上，设置在触发规则时要执行的条件和操作。

     1. 对于“实例限制”，请指定以下值：

        * 最小值：要使用的处理单元的最小数量
        * 最大值：要使用的处理单元的最大数量
        * 默认值：如果在读取资源指标时发生任何问题，且当前容量低于默认容量，则自动缩放会横向扩展到默认的处理单元数。 但是，如果当前容量超出了默认容量，则自动缩放不会横向缩减。

1. 若要添加其他条件，请选择“添加缩放条件”。

1. 在完成自动缩放设置后，请保存所做更改。

<a name="restart-ISE"></a>

## <a name="restart-ise"></a>重启 ISE

如果更改 DNS 服务器或 DNS 服务器设置，则必须重启 ISE，使 ISE 可以接收这些更改。 重启高级 SKU ISE 不会导致停机，因为有冗余，并且组件在回收过程中一次重启一个。 但是，开发人员 SKU ISE 会经历停机，因为没有冗余存在。 有关详细信息，请参阅 [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)。

1. 在 [Azure 门户](https://portal.azure.com)中，转到你的 ISE。

1. 在 ISE 菜单上选择“概述”。 在“概述”工具栏上，选择“重启”。

   ![重启集成服务环境](./media/connect-virtual-network-vnet-isolated-environment/restart-integration-service-environment.png)

<a name="delete-ise"></a>

## <a name="delete-ise"></a>删除 ISE

在删除不再需要的 ISE 或包含 ISE 的 Azure 资源组之前，请先检查是否在包含这些资源的 Azure 资源组或 Azure 虚拟网络上没有策略或锁定，因为这些项可能会阻止删除。

在删除 ISE 之后，可能最多会需要等待 9 小时，然后再尝试删除 Azure 虚拟网络或子网。

## <a name="next-steps"></a>后续步骤

* [向集成服务环境添加资源](../logic-apps/add-artifacts-integration-service-environment-ise.md)
