---
title: 门户中的服务管理
titleSuffix: Azure Cognitive Search
description: 使用 Azure 门户管理 Azure 认知搜索服务（Microsoft Azure 上托管的云搜索服务）。
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: dbc82b67206f6453101b0d6f419718b928506f79
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113003847"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Azure 门户中 Azure 认知搜索服务管理

> [!div class="op_single_selector"]
>
> * [PowerShell](search-manage-powershell.md)
> * [Azure CLI](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure 认知搜索是一种完全托管的、基于云的搜索服务，用于在自定义应用中生成丰富的搜索体验。 本文介绍可在 [Azure 门户](https://portal.azure.com)中对已创建的搜索服务执行的管理任务。 使用门户，你可以执行与该服务相关的所有[管理任务](#management-tasks)，以及内容管理和浏览。 因此，通过门户，可以全面访问搜索服务操作的所有方面。

每项搜索服务都作为独立资源进行管理。 下图显示了名为“demo-search-svc”的单个免费搜索服务的门户页面。 尽管你可能已经习惯了使用 Azure PowerShell 或 Azure CLI 进行服务管理，但要熟悉门户页面提供的工具和功能，这一点非常有意义。 与使用代码相比，在门户中执行某些任务既轻松，又快速。 

## <a name="overview-home-page"></a>概述页面（主页）

概述页面是每个服务的主页。 下图中，用红色框括起来的屏幕区域指示你可能经常使用的任务、工具和磁贴，尤其是在你还不熟悉服务的情况下。

:::image type="content" source="media/search-manage/search-portal-overview-page.png" alt-text="搜索服务的门户页面" border="true":::

| 区域 | 说明 |
|------|-------------|
| 1  | “基本信息”部分显示了服务属性，包括设置连接时使用的终结点。 还会一目了然地显示层、副本和分区计数。 |
| 2 | 页面顶部是一系列用于调用交互式工具或管理服务的命令。 [导入数据](search-get-started-portal.md)和[搜索资源管理器](search-explorer.md)通常用于原型制作和浏览。 |
| 3 | “基本信息”部分下面是一系列选项卡式子页，可用于快速访问使用情况统计信息、服务运行状况指标，还可以访问服务中所有现有索引、索引器、数据源和技能集。 如果选择索引或其他对象，则会有其他页面以显示对象组合、设置和状态（如果适用）。 |
| 4 | 在左侧，你可以访问可打开其他页面的链接，这些页面用于访问连接中使用的 API 密钥，配置服务，配置安全性，监视操作，自动化任务以及获取支持。 |

### <a name="read-only-service-properties"></a>只读服务属性

搜索服务的一些方面是在预配服务时确定的，不能更改：

* 服务名称（不能重命名搜索服务）
* 服务位置（无法轻易地将搜索服务原封不动地移动到另一个区域。 尽管存在模板，但内容移动是手动过程）
* 服务层级（例如，在不创建新服务的情况下，不能从 S1 切换到 S2）

## <a name="management-tasks"></a>管理任务

服务管理在设计上是轻量级的，通常定义为你可以执行并且与服务自身相关的某些任务：

* 通过添加或删除副本和分区来[调整容量](search-capacity-planning.md)
* [管理用于管理操作和查询操作的 API 密钥](search-security-api-keys.md)
* 通过基于角色的安全性[控制对管理操作的访问](search-security-rbac.md)
* [配置 IP 防火墙规则](service-configure-firewall.md)以根据 IP 地址限制访问
* 使用 Azure 专用链接和专用虚拟网络[配置专用终结点](service-create-private-endpoint.md)
* [监视服务运行状况](search-monitor-usage.md)：存储、查询卷和延迟

还可以枚举在该服务中创建的所有对象：索引、索引器、数据源、技能集等。 门户的概述页将显示服务上存在的所有内容。 对搜索服务的绝大多数操作都与内容相关。

在门户中执行的相同管理任务也可以通过以下工具以编程方式处理：[管理 REST API](/rest/api/searchmanagement/)、[Az.Search PowerShell 模块](search-manage-powershell.md)、[az search Azure CLI 模块](search-manage-azure-cli.md)以及适用于 .NET、Python、Java 和 JavaScript 的 Azure SDK。 管理任务完全呈现在门户和所有程序设计界面中。 任何特定管理任务都不会只能通过一种形式使用。

认知搜索充分利用其他 Azure 服务进行更深入的监视和管理。 在搜索服务中单独存储的唯一数据是对象内容（索引、索引器和数据源定义，以及其他对象）。 报告到门户页面的指标按照滚动的 30 天周期从内部日志中拉取。 对于用户控制的日志保留和其他事件，需要 [Azure Monitor](../azure-monitor/index.yml)。 有关为搜索服务设置诊断日志记录的详细信息，请参阅[收集和分析日志数据](search-monitor-logs.md)。

## <a name="administrator-permissions"></a>管理员权限

你打开搜索服务概述页时，分配给你的帐户的权限将决定你可以使用哪些页面。 本文开头的概述页显示管理员或参与者将看到的门户页。

在 Azure 资源中，管理权限是通过角色分配授予的。 在 Azure 认知搜索上下文中，[角色分配](search-security-rbac.md)将确定哪些用户可以分配副本和分区或者管理 API 密钥，而不考虑他们是使用门户、[PowerShell](search-manage-powershell.md)、[Azure CLI](search-manage-azure-cli.md) 还是[管理 REST API](/rest/api/searchmanagement)：

> [!TIP]
> 预配或解除对服务本身的授权可以通过 Azure 订阅管理员或协同管理员完成。 利用 Azure 范围内的机制，可以锁定订阅或资源，以防止具备管理员权限的用户意外或在未经授权的情况下删除搜索服务。 有关详细信息，请参阅[锁定资源以防止意外删除](../azure-resource-manager/management/lock-resources.md)。

## <a name="next-steps"></a>后续步骤

* 查看门户中可用的[监视功能](search-monitor-usage.md)
* 使用 [PowerShell](search-manage-powershell.md) 或 [Azure CLI](search-manage-azure-cli.md) 进行自动化操作
* 查看[安全功能](search-security-overview.md)以保护内容和操作
* 启用[诊断日志记录](search-monitor-logs.md)以监视查询和索引工作负载