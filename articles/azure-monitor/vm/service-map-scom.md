---
title: VM 见解映射与 Operations Manager 集成 | Microsoft Docs
description: VM 见解会自动发现 Windows 和 Linux 系统上的应用程序组件，并映射服务之间的通信。 本文介绍如何使用映射功能在 Operations Manager 中自动创建分布式应用程序关系图。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/12/2019
ms.openlocfilehash: 3a7d0d49313cb524a5bf39add5c9a55862dcad47
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046884"
---
# <a name="integrate-system-center-operations-manager-with-vm-insights-map-feature"></a>将 System Center Operations Manager 与 VM 见解映射功能集成

在 VM 见解中，可以查看在 Azure 或实际环境中运行的 Windows 和 Linux 虚拟机 (VM) 上发现的应用程序组件。 通过映射功能与 System Center Operations Manager 的此集成，可以根据 VM 见解中的动态依赖关系映射，在 Operations Manager 中自动创建分布式应用程序关系图。 本文介绍如何配置 System Center Operations Manager 管理组以支持此功能。

>[!NOTE]
>如果已部署服务映射，则可在 VM 见解中查看映射，其中包括用于监视 VM 运行状况和性能的其他功能。 VM 见解的映射功能旨在替代独立的服务映射解决方案。 若要详细了解，请参阅 [VM 见解概述](../vm/vminsights-overview.md)。

## <a name="prerequisites"></a>先决条件

* System Center Operations Manager 管理组（2012 R2 或更高版本）。
* 配置为支持 VM 见解的 Log Analytics 工作区。
* 一个或多个受 Operations Manager 监视并将数据发送到 Log Analytics 工作区的 Windows 和 Linux 虚拟机或物理计算机。 需将向 Operations Manager 管理组报告的 Linux 服务器配置为直接连接到 Azure Monitor。 有关详细信息，请参阅[使用 Log Analytics 代理收集日志数据](../agents/log-analytics-agent.md)中的概述。
* 对与 Log Analytics 工作区关联的 Azure 订阅具有访问权限的服务主体。 有关详细信息，请参阅[创建服务主体](#create-a-service-principal)。

## <a name="install-the-service-map-management-pack"></a>安装服务映射管理包

导入 Microsoft.SystemCenter.ServiceMap 管理捆绑包 (Microsoft.SystemCenter.ServiceMap.mpb) 可以启用 Operations Manager 与映射功能之间的集成。 可以从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=55763)下载管理捆绑包。 该捆绑包包含以下管理包：

* Microsoft Service Map Application Views
* Microsoft System Center Service Map Internal
* Microsoft System Center Service Map Overrides
* Microsoft System Center Service Map

## <a name="configure-integration"></a>配置集成

安装服务映射管理包后，将在 Operations Manager Operations 控制台的“管理”窗格中的“Operations Management Suite”下显示新节点“服务映射”。

>[!NOTE]
>[Operations Management Suite 是一组服务](../terminology.md#april-2018---retirement-of-operations-management-suite-brand)，其中包括 Log Analytics（现在是 [Azure Monitor](../overview.md) 的一部分）。

若要配置 VM 见解映射集成，请执行以下操作：

1. 若要打开配置向导，请在“服务映射概述”窗格中单击“添加工作区”。   

    ![“服务映射概述”窗格](media/service-map-scom/scom-configuration.png)

2. 在“连接配置”窗口中，输入服务主体的租户名称或 ID、应用程序 ID（也称为用户名或 clientID）和密码，并单击“下一步”。  有关详细信息，请参阅“创建服务主体”。

    ![“连接配置”窗口](media/service-map-scom/scom-config-spn.png)

3. 在“订阅选择”窗口中，选择 Azure 订阅、Azure 资源组（包含 Log Analytics 工作区的资源组）和 Log Analytics 工作区，然后单击“下一步”。 

    ![Operations Manager 配置工作区](media/service-map-scom/scom-config-workspace.png)

4. 在“计算机组选择”窗口中，你可以选择要同步到 Operations Manager 的服务映射计算机组。 单击“添加/删除计算机组”，从“可用计算机组”列表中选择组，然后单击“添加”。  选择组后，单击“确定”完成。

    ![Operations Manager 配置计算机组](media/service-map-scom/scom-config-machine-groups.png)

5. 在“服务器选择”窗口中，配置包含要在 Operations Manager 与映射功能之间同步的服务器的服务映射服务器组。 单击“添加/删除服务器”。

    若要在集成中为某个服务器构建分布式应用程序关系图，该服务器必须：

   * 受 Operations Manager 监视
   * 配置为向配置了 VM 见解的 Log Analytics 工作区报告
   * 已列在服务映射服务器组中

     ![Operations Manager 配置组](media/service-map-scom/scom-config-group.png)

6. 可选：选择要与 Log Analytics 通信的所有管理服务器资源池，然后单击“添加工作区”。

    ![“添加 Microsoft Operations Management Suite 工作区”中“服务器池”屏幕的屏幕截图，其中“所有管理服务器资源池”处于选中状态。](media/service-map-scom/scom-config-pool.png)

    配置和注册 Log Analytics 工作区可能需要一分钟时间。 完成配置后，Operations Manager 将启动首次映射同步。

    ![“添加 Microsoft Operations Management Suite 工作区”中“完成”屏幕的屏幕截图，确认已经添加了工作区。](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>监视集成

连接 Log Analytics 工作区后，Operations Manager 操作控制台的“监视”窗格中会显示新文件夹“Service Map”。

![Operations Manager 的“监视”窗格](media/service-map-scom/scom-monitoring.png)

“服务映射”文件夹包含四个节点：

* **活动警报**：列出有关 Operations Manager 与 Azure Monitor 之间通信的所有活动警报。  

  >[!NOTE]
  >这些警报不是与 Operations Manager 同步的 Log Analytics 警报，而是根据服务映射管理包中定义的工作流在管理组中生成的警报。

* 服务器：列出配置为从用于 VM 见解映射功能同步的受监视的服务器。

    ![Operations Manager 的“监视服务器”窗格](media/service-map-scom/scom-monitoring-servers.png)

* **计算机组依赖项视图**：列出从映射功能同步的所有计算机组。 可以单击任意组来查看其分布式应用程序关系图。

    ![服务映射中的屏幕截图，其中显示的关系图中有每个计算机组的图像和指示其间依赖关系的线条。](media/service-map-scom/scom-group-dad.png)

* **服务器依赖项视图**：列出从映射功能同步的所有服务器。 可以单击任一服务器来查看其分布式应用程序关系图。

    ![服务映射中的屏幕截图，其中显示的关系图中有每个服务器的图像和指示其间依赖关系的线条。](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>编辑或删除工作区

可以通过“服务映射概述”窗格编辑或删除配置的工作区（“管理”窗格 >“Operations Management Suite” > “服务映射”）。   

> [!NOTE]
> [Operations Management Suite 是一组服务](../terminology.md#april-2018---retirement-of-operations-management-suite-brand)，其中包括 Log Analytics，后者现在是 [Azure Monitor](../overview.md) 的一部分。

在当前这个版本中，只能配置一个 Log Analytics 工作区。

![Operations Manager 的“编辑工作区”窗格](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>配置规则和重写

一项规则 (Microsoft.SystemCenter.ServiceMapImport.Rule)，用于定期从 VM 见解映射功能提取信息。 若要修改同步时间间隔，可以重写此规则并修改参数 **IntervalMinutes** 的值。

![Operations Manager 的“重写属性”窗口](media/service-map-scom/scom-overrides.png)

* **已启用**：启用或禁用自动更新。
* **IntervalMinutes**：指定更新间隔时间。 默认间隔为 1 小时。 若要更频繁地同步映射，可以更改此值。
* **TimeoutSeconds**：指定请求超时前的时长。
* **TimeWindowMinutes**：指定查询数据的时间范围。 默认为 60 分钟，这是允许的最大时间间隔。

## <a name="known-issues-and-limitations"></a>已知问题和限制

当前设计存在以下问题和限制：

* 只能连接到单个 Log Analytics 工作区。
* 尽管可通过“创作”窗格手动将服务器添加到服务映射服务器组，但不会立即同步这些服务器的映射。 这些映射将会在下一个同步周期内从 VM 见解映射功能同步。
* 如果你对通过管理包创建的分散式应用程序关系图进行了任何更改，那么，在下次与 VM 见解同步时，这些更改可能会被覆盖。

## <a name="create-a-service-principal"></a>创建服务主体

有关创建服务主体的 Azure 正式文档，请参阅：

* [使用 PowerShell 创建服务主体](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [使用 Azure CLI 创建服务主体](/cli/azure/create-an-azure-service-principal-azure-cli)
* [使用 Azure 门户创建服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="suggestions"></a>建议

关于与 VM 见解映射功能集成，或者关于本文档，你有什么反馈吗？ 请访问[用户之声页面](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)，可在此处推荐功能或对现有建议投票。

