---
title: Azure 中的 BareMetal 实例单元
description: 了解如何通过 Azure 门户确定 BareMetal 实例单位并与其交互。
ms.topic: how-to
ms.date: 1/4/2021
ms.openlocfilehash: b089b45c35ff05f10ae59f8ce793645361be1e9b
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733257"
---
# <a name="manage-baremetal-instances-through-the-azure-portal"></a>通过 Azure 门户管理 BareMetal 实例
 
本文说明 [Azure 门户](https://portal.azure.com/) 如何显示 [BareMetal 实例](baremetal-overview-architecture.md)。 本文还介绍了可以在 Azure 门户中通过已部署的 BareMetal 实例单元执行的活动。 
 
## <a name="register-the-resource-provider"></a>注册资源提供程序
用于 BareMetal 实例的 Azure 资源提供程序提供了 Azure 门户中的实例的可见性，当前为公共预览版。 默认情况下，用于 BareMetal 实例部署的 Azure 订阅将注册 *BareMetalInfrastructure* 资源提供程序。 如果看不到部署的 BareMetal 实例单位，则必须将资源提供程序注册到订阅。 

注册 BareMetal 实例资源提供程序有两种方法：
 
* [Azure CLI](#azure-cli)
 
* [Azure 门户](#azure-portal)
 
### <a name="azure-cli"></a>Azure CLI
 
通过 Azure CLI 登录到用于 BareMetal 实例部署的 Azure 订阅。 可以向注册 BareMetalInfrastructure 资源提供程序：

```azurecli-interactive
az provider register --namespace Microsoft.BareMetalInfrastructure
```
 
有关详细信息，请参阅 [Azure 资源提供程序和类型](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)一文。
 
### <a name="azure-portal"></a>Azure 门户
 
可以通过 Azure 门户注册 BareMetalInfrastructure 资源提供程序。
 
你需要在 Azure 门户中列出你的订阅，然后双击用于部署 BareMetal 实例单位的订阅。
 
1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户菜单上，选择“所有服务”。

1. 在“所有服务”  框中，输入“订阅”  ，然后选择“订阅”  。

1. 从订阅列表中选择订阅进行查看。

1. 选择 " **资源提供程序** "，并在搜索中输入 **BareMetalInfrastructure** 。 应 **注册** 资源提供程序，如图所示。
 
>[!NOTE]
>如果未注册资源提供程序，请选择“注册”。
 
:::image type="content" source="media/baremetal-infrastructure-portal/register-resource-provider-azure-portal.png" alt-text="显示已注册的 BareMetal 实例单元的屏幕截图":::
 
## <a name="baremetal-instance-units-in-the-azure-portal"></a>Azure 门户中的 BareMetal 实例单位
 
提交 BareMetal 实例部署请求时，需要指定要连接到 BareMetal 实例的 Azure 订阅。 使用用于部署适用于 BareMetal 实例单位的应用程序层的订阅。
 
在 BareMetal 实例的部署过程中，将在部署请求中使用的 Azure 订阅中创建新的 [azure 资源组](../../../azure-resource-manager/management/manage-resources-portal.md) 。 此新资源组列出你在特定订阅中部署的所有 BareMetal 实例单元。

1. 在 BareMetal 订阅的 Azure 门户中，选择 " **资源组**"。
 
   :::image type="content" source="media/baremetal-infrastructure-portal/view-baremetal-instance-units-azure-portal.png" alt-text="显示资源组列表的屏幕截图":::

1. 在列表中，找到新的资源组。
 
   :::image type="content" source="media/baremetal-infrastructure-portal/filter-resource-groups.png" alt-text="在筛选的资源组列表中显示 BareMetal 实例单元的屏幕截图" lightbox="media/baremetal-infrastructure-portal/filter-resource-groups.png":::
   
   >[!TIP]
   >可以筛选用于部署 BareMetal 实例的订阅。 筛选到适当的订阅后，可能会有一个较长的资源组列表。 查找 **Txxx** 的修复后的，其中 xxx 是三个数字，如 **-T250**。

1. 选择新资源组以显示其详细信息。 此图显示了一个已部署的 BareMetal 实例单元。
   
   >[!NOTE]
   >如果在同一 Azure 订阅下部署了多个 BareMetal 实例租户，则会看到多个 Azure 资源组。
 
## <a name="view-the-attributes-of-a-single-instance"></a>查看单个实例的属性
 
可以查看单个单元的详细信息。 在 BareMetal 实例的列表中，选择要查看的单个实例。
 
:::image type="content" source="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png" alt-text="显示单个实例的 BareMetal 实例单元属性的屏幕截图" lightbox="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png":::
 
映像中的属性与 Azure 虚拟机 (VM) 属性看起来并不大。 在左侧，你将看到资源组、Azure 区域和订阅名称和 ID。 如果分配了标记，则还会在此处看到它们。 默认情况下，BareMetal 实例单位没有分配标记。
 
在右侧，可以看到设备的名称、操作系统 (OS) 、IP 地址和显示 CPU 线程数和内存的 SKU。 还会看到 BareMetal 实例标记) 的电源状态和硬件版本 (修订版本。 电源状态指示硬件设备是开机还是关机。 但操作系统详细信息并不指明它是否已启动并正在运行。
 
可能的硬件修订包括：

* 修订版 3 (Rev 3) 

* 修订版 4 (Rev 4) 
 
* 修订版 4.2 (Rev 4.2) 
 
>[!NOTE]
>修订版4.2 是使用现有 Rev 4 体系结构的最新更名 BareMetal 基础结构。 修订版4更接近于 Azure 虚拟机 (VM) 主机。 它显著改进了在修订版4戳记或 rows 中部署的 Azure Vm 和 BareMetal 实例单位之间的网络延迟。 可以通过 Azure 门户访问和管理 BareMetal 实例。 有关详细信息，请参阅 [Azure 上的 BareMetal 基础结构](baremetal-overview-architecture.md)。
 
此外，在右侧，你会发现 [Azure 邻近性放置组的](../../../virtual-machines/co-location.md) 名称，该名称是自动为每个已部署的 BareMetal 实例单元创建的。 部署承载应用程序层的 Azure Vm 时，引用邻近位置组。 使用与 BareMetal 实例单元关联的邻近性放置组时，请确保将 Azure Vm 部署到靠近 BareMetal 实例单元的位置。
 
>[!TIP]
>若要在与修订版4.x 相同的 Azure 数据中心中查找应用程序层，请参阅 [azure 邻近性放置组以获得最佳网络延迟](../../../virtual-machines/workloads/sap/sap-proximity-placement-scenarios.md)。
 
## <a name="check-activities-of-a-single-instance"></a>检查单个实例的活动
 
可以检查单个单元的活动。 记录的主要活动之一是重新启动该单元。 列出的数据包括活动状态、触发活动的时间戳、订阅 ID 以及触发活动的 Azure 用户。
 
:::image type="content" source="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png" alt-text="显示 BareMetal 实例单元活动的屏幕截图" lightbox="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png":::
 
在 Azure 中对单位的元数据进行的更改也会记录在活动日志中。 除了启动重新启动之外，你还可以看到 **Write BareMetallnstances** 的活动。 此活动不会对 BareMetal 实例单元本身进行任何更改，但会在 Azure 中记录对单元元数据的更改。
 
记录的另一个活动是向实例添加标记或删除 [标记](../../../azure-resource-manager/management/tag-resources.md) 。
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>向实例添加和删除 Azure 标记
 
可以将 Azure 标记添加到 BareMetal 实例单元，也可以将其删除。 分配标记的方式与将标记分配给 Vm 的方式不同。 与 Vm 一样，标记存在于 Azure 元数据中，对于 BareMetal 实例，它们与 Vm 的标记具有相同的限制。
 
删除标记的工作方式与使用 Vm 的方式相同。 应用和删除标记在 BareMetal 实例单元的活动日志中列出。
 
## <a name="check-properties-of-an-instance"></a>检查实例的属性
 
获取实例时，可以通过 "属性" 部分查看收集的有关实例的数据。 收集的数据包括 Azure 连接、存储后端、ExpressRoute 线路 ID、唯一的资源 ID 和订阅 ID。 你将在支持请求或设置存储快照配置时使用此信息。
 
你将看到的另一项关键信息是存储 NFS IP 地址。 它将你的存储隔离到 BareMetal 实例堆栈中的 **租户** 。 在编辑 [存储快照备份的配置文件](../../../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots)时，将使用此 IP 地址。
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-properties.png" alt-text="显示 BareMetal 实例属性设置的屏幕截图" lightbox="media/baremetal-infrastructure-portal/baremetal-instance-properties.png":::
 
## <a name="restart-a-unit-through-the-azure-portal"></a>通过 Azure 门户重启单元
 
在各种情况下，操作系统无法完成重新启动，这需要重新启动 BareMetal 实例单元。 你可以从 Azure 门户中直接执行设备的电源重新启动：
 
选择 " **重新启动** "，然后选择 **"是"** 以确认重新启动设备。
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-restart.png" alt-text="显示如何重新启动 BareMetal 实例单元的屏幕截图":::
 
重新启动 BareMetal 实例单元时，将会出现延迟。 在此延迟期间，电源状态将从 **开始** 变为 **已启动**，这意味着操作系统已完全启动。 因此，在重新启动后，一旦状态切换为 " **已启动**"，就不能登录到该设备。
 
>[!IMPORTANT]
>根据 BareMetal 实例单位中的内存量，重新启动和重新启动硬件和操作系统最多可能需要一小时。
 
## <a name="open-a-support-request-for-baremetal-instances"></a>为 BareMetal 实例打开支持请求
 
你可以为 BareMetal 实例单元专门提交支持请求。
1. 在 Azure 门户中，在 " **帮助 + 支持**" 下创建 **[新的支持请求](https://rc.portal.azure.com/#create/Microsoft.Support)** ，并为票证提供以下信息：
 
   - **问题类型：** 选择问题类型
 
   - **订阅：** 选择你的订阅
 
   - **服务：** BareMetal 基础结构
 
   - **资源：** 提供实例的名称
 
   - **摘要：** 提供请求摘要
 
   - **问题类型：** 选择问题类型
 
   - **问题子类型：** 选择问题的子类型

1. 选择 " **解决方案** " 选项卡，查找问题的解决方案。 如果找不到解决方案，请执行下一步。

1. 选择 " **详细信息** " 选项卡，并选择问题是与 vm 一起还是 BareMetal 实例单元。 此信息有助于将支持请求定向到正确的专家。

1. 指示问题的开始时间，并选择实例区域。

1. 提供有关请求的更多详细信息并上传文件（如果需要）。

1. 选择 " **查看 + 创建** " 以提交请求。
 
支持代表需要5个工作日内确认你的请求。

## <a name="next-steps"></a>后续步骤

如果要了解有关工作负荷的详细信息，请参阅 [BareMetal 工作负荷类型](../../../virtual-machines/workloads/sap/get-started.md)。