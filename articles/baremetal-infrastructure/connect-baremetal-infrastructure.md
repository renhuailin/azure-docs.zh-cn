---
title: 在 Azure 中连接 BareMetal 基础结构实例
description: 了解如何通过 Azure 门户或 Azure CLI 确定 BareMetal 实例并与其交互。
ms.topic: how-to
ms.date: 07/13/2021
ms.openlocfilehash: b9f5de92ed213d987c7dfac5b3e48f9b565bfff5
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113767145"
---
# <a name="connect-baremetal-infrastructure-instances-in-azure"></a>在 Azure 中连接 BareMetal 基础结构实例

本文将介绍在 [Azure 门户](https://portal.azure.com/)中使用已部署的 BareMetal 基础结构实例可以执行哪些操作。 
 
## <a name="register-the-resource-provider"></a>注册资源提供程序
使用用于 BareMetal 实例的 Azure 资源提供程序可以查看 Azure 门户中的实例。 默认情况下，用于 BareMetal 实例部署的 Azure 订阅将注册 BareMetalInfrastructure 资源提供程序。 如果你看不到已部署的 BareMetal 实例，请将资源提供程序注册到订阅。 

可以使用 Azure 门户或 Azure 命令行界面 (CLI) 来注册 BareMetal 实例资源提供程序。

### <a name="portal"></a>[Portal](#tab/azure-portal)
 
需要在 Azure 门户中列出你的订阅，然后双击用于部署 BareMetal 实例的订阅。
 
1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户菜单上，选择“所有服务”。

1. 在“所有服务”  框中，输入“订阅”  ，然后选择“订阅”  。

1. 从订阅列表中选择订阅。

1. 选择“资源提供程序”并在搜索中输入“BareMetalInfrastructure”。  资源提供程序应处于“已注册”状态，如图所示。
 
>[!NOTE]
>如果未注册资源提供程序，请选择“注册”。
 
:::image type="content" source="media/connect-baremetal-infrastructure/register-resource-provider-azure-portal.png" alt-text="屏幕截图显示已注册的 BareMetal 实例。":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

开始使用 Azure CLI：

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../includes/azure-cli-prepare-your-environment-no-header.md)]

通过 Azure CLI 登录到用于 BareMetal 实例部署的 Azure 订阅。 使用 [az provider register](/cli/azure/provider#az_provider_register) 命令注册 `BareMetalInfrastructure` 资源提供程序：

```azurecli
az provider register --namespace Microsoft.BareMetalInfrastructure
```

可以使用 [az provider list](/cli/azure/provider#az_provider_list) 命令查看所有可用的提供程序。

---

有关资源提供程序的详细信息，请参阅 [Azure 资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md)。  

## <a name="baremetal-instances-in-the-azure-portal"></a>Azure 门户中的 BareMetal 实例
 
提交 BareMetal 实例部署请求时，请指定要连接到 BareMetal 实例的 Azure 订阅。 使用你用于部署适用于 BareMetal 实例的应用程序层的同一订阅。
 
在部署 BareMetal 实例时，将在你已在部署请求中使用过的 Azure 订阅中创建新的 [Azure 资源组](../azure-resource-manager/management/manage-resources-portal.md)。 这个新资源组会列出你已在该订阅中部署的所有 BareMetal 实例。

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. 在 Azure 门户的 BareMetal 订阅中，选择“资源组”。
 
   :::image type="content" source="media/connect-baremetal-infrastructure/view-baremetal-instances-azure-portal.png" alt-text="屏幕截图显示资源组列表。":::

1. 在列表中，找到新的资源组。
 
   :::image type="content" source="media/connect-baremetal-infrastructure/filter-resource-groups.png" alt-text="屏幕截图显示经过筛选的资源组列表中的 BareMetal 实例" lightbox="media/connect-baremetal-infrastructure/filter-resource-groups.png":::
   
   >[!TIP]
   >可以筛选用于部署 BareMetal 实例的订阅。 筛选到适当的订阅后，可能会有一个较长的资源组列表。 查找带有后缀“-Txxx”的资源组，其中 xxx 是三个数字，如“-T250”。 

1. 选择新资源组以查看其详细信息。 此图显示了一个已部署的 BareMetal 实例。
   
   >[!NOTE]
   >如果在同一 Azure 订阅下部署了几个 BareMetal 实例租户，则会看到多个 Azure 资源组。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要查看所有 BareMetal 实例，请对资源组运行 [az baremetalinstance list](/cli/azure/baremetalinstance#az_baremetalinstance_list) 命令：

```azurecli
az baremetalinstance list --resource-group DSM05A-T550 –output table
```

> [!TIP]
> `--output` 参数是全局参数，适用于所有命令。 table 值以友好格式显示输出。 有关详细信息，请参阅 [Azure CLI 命令的输出格式](/cli/azure/format-output-azure-cli)。

---

## <a name="view-the-attributes-of-a-single-instance"></a>查看单个实例的属性

你可以查看单个实例的详细信息。

### <a name="portal"></a>[门户](#tab/azure-portal)

在 BareMetal 实例的列表中，选择要查看的单个实例。
 
:::image type="content" source="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png" alt-text="屏幕截图显示单个实例的 BareMetal 实例属性。" lightbox="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png":::
 
映像中的属性与 Azure 虚拟机 (VM) 属性看起来没什么不同。 在左侧，将看到资源组、Azure 区域和订阅名称及 ID。 如果分配了标记，则也会在此处看到标记。 默认情况下，BareMetal 实例没有分配标记。
 
在右侧，将看到 BareMetal 实例的名称、操作系统 (OS)、IP 地址和显示 CPU 线程数和内存的 SKU。 还会看到电源状态和硬件版本（BareMetal 实例标记的修订）。 电源状态指示硬件设备是开机还是关机。 但是，操作系统详细信息并不指明它是否已启动并正在运行。
 
可能的硬件修订包括：

* 修订版 3 (Rev 3)

* 修订版 4 (Rev 4)
 
* 修订版 4.2 (Rev 4.2)
 
>[!NOTE]
>Rev 4.2 是使用现有 Rev 4 体系结构的最新更名 BareMetal 基础结构。 Rev 4 更接近于 Azure 虚拟机 (VM) 主机。 它大大降低了 Azure VM 与 SAP HANA 实例之间的网络延迟。 可以通过 Azure 门户访问和管理 BareMetal 实例。 有关详细信息，请参阅 [Azure 上的 BareMetal 基础结构](concepts-baremetal-infrastructure-overview.md)。

 
此外，在右侧，还会发现 [Azure 邻近放置组](../virtual-machines/co-location.md)的名称。 该放置组名称是自动为每个已部署的 BareMetal 实例创建的。 在部署用于托管应用程序层的 Azure VM 时，引用邻近放置组。 使用与 BareMetal 实例关联的邻近放置组可确保将 Azure VM 部署到靠近 BareMetal 实例的位置。
 
>[!TIP]
>要在与修订版 4.x 相同的 Azure 数据中心中查找应用程序层，请参阅[用于最大程度地降低网络延迟的 Azure 邻近放置组](../virtual-machines/workloads/sap/sap-proximity-placement-scenarios.md)。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要查看 BareMetal 实例的详细信息，请运行 [az baremetalinstance show](/cli/azure/baremetalinstance#az_baremetalinstance_show) 命令：

```azurecli
az baremetalinstance show --resource-group DSM05A-T550 --instance-name orcllabdsm01
```

如果不确定实例名称，请运行上述 `az baremetalinstance list` 命令。

---
 
## <a name="check-activities-of-a-single-instance"></a>检查单个实例的活动
 
你可以检查单个 BareMetal 实例的活动。 记录的主要活动之一是重启该实例。 列出的数据包括活动状态、触发活动的时间戳、订阅 ID 以及触发活动的 Azure 用户。
 
:::image type="content" source="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png" alt-text="屏幕截图显示 BareMetal 实例活动。" lightbox="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png":::
 
在 Azure 中对实例的元数据进行的更改也会记录在活动日志中。 除了重启之外，还可以看到“写入 BareMetalInstances”活动。 此活动不会对 BareMetal 实例本身进行任何更改，但是会在 Azure 中记录对单元元数据的更改。
 
记录的另一个活动是向实例添加或删除[标记](../azure-resource-manager/management/tag-resources.md)。
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>向实例添加和删除 Azure 标记

### <a name="portal"></a>[门户](#tab/azure-portal)
 
可以将 Azure 标记添加到 BareMetal 实例，也可以将其删除。 标记的分配方式就像为 VM 分配标记一样。 和 VM 一样，标记存在于 Azure 元数据中。 标记对 BareMetal 实例的限制与对 VM 的限制相同。
 
删除标记的方式也与 VM 相同。 应用和删除标记的操作都会在 BareMetal 实例的活动日志中列出。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

将标记分配到 BareMetal 实例的方式与为虚拟机分配标记相同。 和 VM 一样，标记存在于 Azure 元数据中。 标记对 BareMetal 实例的限制与对 VM 的限制相同。

要将标记添加到 BareMetal 实例，请运行 [az baremetalinstance update](/cli/azure/baremetalinstance#az_baremetalinstance_update) 命令：

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --set tags.Dept=Finance tags.Status=Normal
```

使用相同的命令删除标记：

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --remove tags.Dept
```

---

## <a name="check-properties-of-an-instance"></a>检查实例的属性
 
获取实例时，可以通过“属性”部分查看收集的有关实例的数据。 收集的数据包括：
- Azure 连接性
- 存储后端
- ExpressRoute 线路 ID
- 唯一的资源 ID
- 订阅 ID。 

你将在支持请求或设置存储快照配置时使用此信息。
 
你将看到的另一条关键信息是存储 NFS IP 地址。 它将你的存储隔离到 BareMetal 实例堆栈中的“租户”。 编辑[配置 Azure 应用程序一致性快照工具](../azure-netapp-files/azacsnap-cmd-ref-configure.md)时，将使用此 IP 地址。
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-properties.png" alt-text="屏幕截图显示 BareMetal 实例的属性设置。" lightbox="media/connect-baremetal-infrastructure/baremetal-instance-properties.png":::
 
## <a name="restart-a-baremetal-instance-through-the-azure-portal"></a>通过 Azure 门户重启 BareMetal 实例

在操作系统无法完成重启的各种情况下，就需要对 BareMetal 实例进行电源重启。

### <a name="portal"></a>[门户](#tab/azure-portal)

可以从 Azure 门户中直接执行实例的电源重启：
 
选择“重启”，然后选择“是”以确认重启。
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-restart.png" alt-text="屏幕截图显示如何重启 BareMetal 实例。":::
 
重启 BareMetal 实例时，将会出现延迟。 在此延迟期间，电源状态将从“正在启动”变为“已启动”，这意味着操作系统已完全启动。  因此，在重启后，一旦状态切换为“已启动”，你只能登录到该设备一次。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要重启 BareMetal 实例，请使用 [az baremetalinstance restart](/cli/azure/baremetalinstance#az_baremetalinstance_restart) 命令：

```azurecli
az baremetalinstance restart --resource-group DSM05a-T550 --instance-name orcllabdsm01
```

---

>[!IMPORTANT]
>根据 BareMetal 实例中的内存量，重启和重新引导硬件和操作系统可能需要一小时。
 
## <a name="open-a-support-request-for-baremetal-instances"></a>为 BareMetal 实例提交支持请求
 
可以为 BareMetal 实例专门提交支持请求。
1. 在 Azure 门户中的“帮助 + 支持”下，创建 **[新支持请求](https://rc.portal.azure.com/#create/Microsoft.Support)** 并为票证提供以下信息：
 
   - 问题类型：选择问题类型。
 
   - 订阅：选择自己的订阅。
 
   - **服务：** BareMetal 基础结构
 
   - 资源：提供实例的名称。
 
   - 摘要：提供请求摘要。
 
   - 问题类型：选择问题类型。
 
   - 问题子类型：选择问题的子类型。

1. 选择“解决方案”选项卡，查找问题的解决方案。 如果找不到解决方案，请执行下一步。

1. 选择“详细信息”选项卡，并选择问题是与 VM 还是 BareMetal 实例有关。 此信息有助于将支持请求转给正确的专家。

1. 指示问题的开始时间，并选择实例区域。

1. 提供有关请求的更多详细信息并上传文件（如果需要）。

1. 选择“预览 + 创建”提交请求。
 
支持代表需要 5 个工作日确认你的请求。

## <a name="next-steps"></a>后续步骤

详细了解 BareMetal 基础结构的工作负荷。

> [!div class="nextstepaction"]
> [什么是 Azure 上的 SAP HANA（大型实例）？](../virtual-machines/workloads/sap/hana-overview-architecture.md)
