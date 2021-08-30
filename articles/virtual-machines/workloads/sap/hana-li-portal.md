---
title: 通过 Azure 门户控制 Azure HANA 大型实例 | Microsoft Docs
description: 介绍如何通过门户识别 Azure HANA 大型实例并与其进行交互
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/01/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 30d16b2042e2bfc8e11824fe64cf709413fd05d8
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113218378"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>通过 Azure 门户控制 Azure HANA 大型实例

>[!NOTE]
>对于修订版 4.2，请按照[通过 Azure 门户管理 BareMetal 实例](../../../baremetal-infrastructure/connect-baremetal-infrastructure.md)主题中的说明进行操作。

本文档介绍 [HANA 大型实例](./hana-overview-architecture.md)在 [Azure 门户](https://portal.azure.com)中如何显示，以及可以通过 Azure 门户对为你部署的 HANA 大型实例单元开展哪些活动。 通过适用于 HANA 大型实例的 Azure 资源提供程序（目前为公共预览版）在 Azure 门户中提供 HANA 大型实例的可见性

## <a name="register-hana-large-instance-resource-provider"></a>注册 HANA 大型实例资源提供程序
通常，用于 HANA 大型实例部署的 Azure 订阅已注册 HANA 大型实例资源提供程序。 但是，如果看不到已部署的 HANA 大型实例单元，则应在 Azure 订阅中注册资源提供程序。 可通过两种方法注册 HANA 大型实例资源提供程序

### <a name="register-through-cli-interface"></a>通过 CLI 接口注册
需要通过 Azure CLI 接口登录到用于 HANA 大型实例部署的 Azure 订阅。 可以使用以下命令（重新）注册 HANA 大型实例提供程序：
    
```azurecli
az provider register --namespace Microsoft.HanaOnAzure
```

有关详细信息，请参阅 [Azure 资源提供程序和类型](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)一文


### <a name="register-through-azure-portal"></a>通过 Azure 门户注册
可以通过 Azure 门户（重新）注册 HANA 大型实例资源提供程序。 你需要在 Azure 门户中列出订阅，然后双击用于部署 HANA 大型实例单元的订阅。 进入订阅的概述页面后，选择“资源提供程序”（如下所示），然后在搜索窗口中键入“HANA”。 

![通过 Azure 门户注册 HLI RP](./media/hana-li-portal/portal-register-hli-rp.png)

在显示的屏幕截图中，资源提供程序已注册。 如果尚未注册资源提供程序，请按“重新注册”或“注册”。

有关详细信息，请参阅 [Azure 资源提供程序和类型](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)一文


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>在 Azure 门户中显示 HANA 大型实例单元
提交 HANA 大型实例部署请求时，系统还会要求指定要连接到 HANA 大型实例的 Azure 订阅。 建议使用与用于部署适用于 HANA 大型实例单元的 SAP 应用程序层相同的订阅。
部署第一个 HANA 大型实例时，将在你通过 HANA 大型实例部署请求提交的 Azure 订阅中创建新的 [Azure 资源组](../../../azure-resource-manager/management/manage-resources-portal.md)。  新的资源组将列出你在特定订阅中部署的所有 HANA 大型实例单元。

若要查找新的 Azure 资源组，可以通过浏览 Azure 门户的左侧导航窗格来列出订阅中的资源组

![该屏幕截图突出显示了“资源组”选项。](./media/hana-li-portal/portal-resource-group.png)

在资源组列表中，将为你列出相关项目，你可能需要筛选用于部署 HANA 大型实例的订阅

![在 Azure 门户中筛选资源组](./media/hana-li-portal/portal-filtering-subscription.png)

筛选出正确的订阅后，可能仍然会有很长的资源组列表。 查找后缀为 -Txxx 的资源组，其中 xxx 是三位数字，如 -T050。 

找到资源组后，列出其详细信息。 收到的列表可能如下所示：

![Azure 门户中的 HLI 列表](./media/hana-li-portal/portal-hli-units-list.png)

列出的所有单元都表示已在订阅中部署的单个 HANA 大型实例单元。 在本例中，将看到订阅中部署的八个不同 HANA 大型实例单元。

如果在同一 Azure 订阅下部署了多个 HANA 大型实例租户，则会找到多个 Azure 资源组 


## <a name="look-at-attributes-of-single-hli-unit"></a>查看单个 HLI 单元的属性
在 HANA 大型实例单元列表中，可以单击单个单元并获取单个 HANA 大型实例单元的详细信息。 

在“概述”屏幕中，单击“显示更多”后，将获得如下所示的单元呈现：

![显示 HLI 单元的概述页面](./media/hana-li-portal/portal-show-overview.png)

查看显示的不同属性，这些属性看起来与 Azure VM 属性几乎一样。 左侧标题显示资源组、Azure 区域、订阅名称和 ID 以及添加的一些标记。 默认情况下，HANA 大型实例单元未分配任何标记。 在标题的右侧，列出了部署完成后分配的单元名称。 显示了操作系统以及 IP 地址。 与 VM 一样，还会显示 HANA 大型实例单元类型以及 CPU 线程数和内存。 有关不同 HANA 大型实例单元的更多详细信息，请参阅：

- [对 HLI 可用的 SKU](./hana-available-skus.md)
- [SAP HANA（大型实例）存储体系结构](./hana-storage-architecture.md) 

右下方的其他数据显示 HANA 大型实例标记的修订版。 可能的值包括：

- 修订版 3
- 修订版 4

修订版 4 是 HANA 大型实例发布的最新体系结构，在修订版 4 标记或行中部署的 Azure VM 和 HANA 大型实例单元之间的网络延迟方面有重大改进。
概述页面右下角还有另一个非常重要的信息，即为每个部署的 HANA 大型实例单元自动创建的 Azure 邻近放置组的名称。 部署托管 SAP 应用程序层的 Azure VM 时，需要引用此邻近放置组。 通过使用与 HANA 大型实例单元关联的 [Azure 邻近放置组](../../co-location.md)，可确保在邻近 HANA 大型实例单元的位置部署 Azure VM。 [用于最大程度地降低 SAP 应用程序网络延迟的 Azure 邻近放置组](sap-proximity-placement-scenarios.md)中介绍了如何使用邻近放置组在与修订版 4 托管 HANA 大型实例单元相同的 Azure 数据中心内定位 SAP 应用程序层。

标题右侧列中的一个附加字段告知 HANA 大型实例单元的电源状态。

> [!NOTE]
> 电源状态描述硬件单元是处于开机状态还是关机状态。 它不提供有关正在启动并运行的操作系统的信息。 重启 HANA 大型实例单元时，会有一小段单元的状态变为“正在启动”，然后进入“已启动”状态的时间。 处于“已启动”状态表示 OS 正在启动或 OS 已完全启动。 因此，在单元重启后，只要状态切换到“已启动”，就无法立即登录到该单元。
> 

如果按“查看更多”，则会显示其他信息， 包括显示在其中部署单元的 HANA 大型实例标记的修订版。 有关 HANA 大型实例标记的不同修订版，请参阅[什么是 Azure SAP HANA（大型实例）](./hana-overview-architecture.md)一文

## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>检查单个 HANA 大型实例单元的活动 
除大致了解 HANA 大型实例单元外，还可以检查特定单元的活动。 活动日志可能如下所示：

![Azure 门户中的导航窗格](./media/hana-li-portal/portal-activity-list.png)

记录的主要活动之一是单元的重启活动。 列出的数据包括活动的状态、触发活动的时间戳、触发活动的订阅 ID 以及触发活动的 Azure 用户。 

要记录的另一个活动是 Azure 元数据中的单元更改。 除启动的重启外，还可以看到“写入 HANAInstances”活动。 这种类型的活动不会对 HANA 大型实例单元本身进行任何更改，而是记录 Azure 中对该单元的元数据所做的更改。 在列出的示例中，我们添加和删除了标记（请参阅下一部分）。

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>在 HANA 大型实例单元中添加和删除 Azure 标记
另一种可能是向 HANA 大型实例单元添加[标记](../../../azure-resource-manager/management/tag-resources.md)。 分配标记的方式与向 VM 分配标记没有什么不同。 与 VM 一样，标记存在于 Azure 元数据中，并且对于 HANA 大型实例，其标记具有与 VM 标记相同的限制。

删除标记的方式与 VM 中相同。 应用标记和删除标记这两个活动都将在特定 HANA 大型实例单元的活动日志中列出。

## <a name="check-properties-of-a-hana-large-instance-unit"></a>检查 HANA 大型实例单元的属性
“属性”部分包含将实例移交给你时获得的重要信息。 在此部分中，你可以获得在支持案例中可能需要的所有信息，或设置存储快照配置时可能需要的所有信息。 因此，此部分是有关实例、实例与 Azure 的连接以及存储后端的数据的集合。 该部分的顶部如下所示：


![Azure 门户中 HLI 属性的顶部部分](./media/hana-li-portal/portal-properties-top.png)

你已经在概述屏幕中看到过前几个数据项。 但是，数据的一个重要部分是 ExpressRoute 线路 ID，这是在移交首批已部署单元时获得的信息。 在某些支持案例中，可能会要求提供该数据。 屏幕截图底部显示了一个重要数据条目。 显示的数据为 NFS 存储头（将存储隔离到 HANA 大型实例堆栈中的租户）的 IP 地址。 编辑[配置 Azure 应用程序一致快照工具](../../../azure-netapp-files/azacsnap-cmd-ref-configure.md)时也需要此 IP 地址。 

在属性窗格中向下滚动可获得其他数据，例如 HANA 大型实例单元的唯一资源 ID 或分配给部署的订阅 ID。

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>通过 Azure 门户重启 HANA 大型实例单元
启动 Linux 操作系统的重启操作时，存在各种 OS 无法成功完成重启的情况。 若要强制重启，需要打开服务请求，让 Microsoft Operations 对 HANA 大型实例单元执行电源重启。 现在，HANA 大型实例单元的电源重启功能已集成到 Azure 门户中。 正如在 HANA 大型实例单元的概述部分中一样，你会在数据部分的顶部看到用于重启的按钮

![Azure 门户中的重启步骤 1](./media/hana-li-portal/portal-restart-first-step.png)

按“重启”按钮时，系统会询问是否确实要重启单元。 按“是”按钮确认后，单元将重启。

> [!NOTE]
> 在重启过程中，会有一小段单元的状态变为“正在启动”，然后进入“已启动”状态的时间。 处于“已启动”状态表示 OS 正在启动或 OS 已完全启动。 因此，在单元重启后，只要状态切换到“已启动”，就无法立即登录到该单元。

> [!IMPORTANT]
> 根据 HANA 大型实例单元中的内存量，重启和重新引导硬件和操作系统最多可能需要一小时


## <a name="open-a-support-request-for-hana-large-instances"></a>打开 HANA 大型实例支持请求
除了在 Azure 门户中显示 HANA 大型实例单元之外，还可以专门为 HANA 大型实例单元创建支持请求。 按照“新建支持请求”链接进行操作 

![Azure 门户中的启动服务请求步骤 1](./media/hana-li-portal/portal-initiate-support-request.png)

若要在下一个屏幕中列出 SAP HANA 大型实例的服务，可能需要选择“所有服务”，如下所示

![在 Azure 门户中选择“所有服务”](./media/hana-li-portal/portal-create-service-request.png)

在服务列表中，可以找到“SAP HANA 大型实例”服务。 选择该服务后，可以选择具体问题类型，如下所示：


![在 Azure 门户中选择问题分类](./media/hana-li-portal/portal-select-problem-class.png)

在每个不同的问题类型下，都会提供问题子类型选项，你需要选择这些选项来进一步描述问题。 选择子类型后，现在可以命名主题。 完成选择流程后，可以转到创建的下一步。 在“解决方案”部分中，将指向有关 HANA 大型实例的文档，这些文档可为解决问题提供线索。 如果在建议的文档中找不到问题的解决方案，请转到下一步。 在下一步中，将询问你问题在于虚拟机还是 HANA 大型实例单元。 此信息有助于将支持请求定向到正确的专家。 

![Azure 门户中的支持案例的详细信息](./media/hana-li-portal/portal-support-request-details.png)

回答问题和提供其他详细信息后，可以继续进行下一步，以查看并提交支持请求。

## <a name="next-steps"></a>后续步骤

- [如何监视 Azure 上的 SAP HANA（大型实例）](./troubleshooting-monitoring.md)
- [HANA 端的监视和故障排除](./hana-monitor-troubleshoot.md)