---
title: 大规模管理 Azure Sentinel 工作区
description: Azure Lighthouse 有助于跨委派的客户资源有效地使用 Azure Sentinel。
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: c72dcdd190acfaf3db0b5c9feb1a30b00c63a94d
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112077819"
---
# <a name="manage-azure-sentinel-workspaces-at-scale"></a>大规模管理 Azure Sentinel 工作区

作为服务提供商，你可能已经将多个客户租户载入了 [Azure Lighthouse](../overview.md)。 Azure Lighthouse 允许服务提供商同时在多个 Azure Active Directory (Azure AD) 租户之间大规模执行操作，从而提高管理任务的效率。

Azure Sentinel 提供安全分析和威胁智能，为警报检测、威胁可见性、主动搜寻和威胁响应提供单一解决方案。 利用 Azure Lighthouse，可以在多个租户之间大规模管理多个 Azure Sentinel 工作区。 这样可以实现各种方案，例如，在多个工作区中运行查询，或创建工作簿，以可视化和监视已连接数据源中的数据，以获得见解。 查询和行动手册中的 IP 仍保留在管理租户中，但可用于在客户租户中执行安全管理。

本主题概述了如何以可伸缩方式使用 [Azure Sentinel](../../sentinel/overview.md) 来实现跨租户可见性和托管安全服务。

> [!TIP]
> 尽管本主题中提及的是服务提供商和客户，但本指南同样适用于[使用 Azure Lighthouse 管理多个租户的企业](../concepts/enterprise.md)。

## <a name="architectural-considerations"></a>体系结构注意事项

对于希望使用 Azure Sentinel 构建安全即服务产品/服务的托管安全服务提供商 (MSSP) 而言，可能需要一个安全运营中心 (SOC) 来集中监视、管理和配置在单个客户租户内部署的多个 Azure Sentinel 工作区。 同样，具有多个 Azure AD 租户的企业则可能想要集中管理部署在其租户中的多个 Azure Sentinel 工作区。

这种集中部署模型具有以下优势：

- 数据的所有权在每个托管租户中保持不变。
- 支持在地理边界内存储数据的要求。
- 确保数据隔离，因为多个客户的数据未存储在同一工作区中。
- 阻止从托管租户渗透数据，帮助确保数据合规性。
- 相关成本向每个托管租户收取，而不是向管理租户收取。
- 所有数据源的数据以及与 Azure Sentinel 集成的数据连接器（例如 Azure AD 活动日志、Office 365 日志或 Microsoft 威胁防护警报）将保留在每个客户租户内。
- 减少网络延迟。
- 轻松地添加或删除新的子公司或客户。

> [!NOTE]
> 你可以管理位于不同[区域](../../availability-zones/az-overview.md#regions)的委托资源。 但是，不支持跨[国家云](../../active-directory/develop/authentication-national-cloud.md)和 Azure 公有云或跨两个不同的国家云进行订阅委托。

## <a name="granular-azure-role-based-access-control-azure-rbac"></a>精细化 Azure 基于角色的访问控制 (Azure RBAC)

MSSP 将管理的每个客户订阅都必须[载入 Azure Lighthouse](onboard-customer.md)。 这允许管理租户中的指定用户在客户租户中部署的 Azure Sentinel 工作区上访问和执行管理操作。

创建授权时，可以将 Azure Sentinel 内置角色分配给管理租户中的用户、组或服务主体：

- [Azure Sentinel 读取者](../../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Azure Sentinel 响应方](../../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Azure Sentinel 参与者](../../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

你可能还想要分配其他内置角色来执行其他功能。 有关可与 Azure Sentinel 一起使用的特定角色的信息，请参阅 [Azure sentinel 中的权限](../../sentinel/roles.md)。

载入客户后，指定的用户可以登录到管理租户，并使用分配的角色[直接访问客户的 Azure Sentinel 工作区](../../sentinel/multiple-tenants-service-providers.md)。

## <a name="view-and-manage-incidents-across-workspaces"></a>跨工作区查看和管理事件

如果要为多个客户管理 Azure Sentinel 资源，可以一次在多个租户中查看和管理多个工作区中的事件。 有关详细信息，请参阅[在多个工作区中同时处理事件](../../sentinel/multiple-workspace-view.md)和[跨工作区和租户扩展 Azure Sentinel](../../sentinel/extend-sentinel-across-workspaces-tenants.md)。

> [!NOTE]
> 请确保已为管理租户中的用户分配了对所管理的所有工作区的读取和写入权限。 如果用户仅在某些工作空间上具有读取权限，则在这些工作空间中选择事件时，可能会显示警告消息，并且该用户将无法修改这些事件或与这些事件一起被选定的任何其他事件（即使你确实具有其他事件的权限）。

## <a name="configure-playbooks-for-mitigation"></a>配置缓解 playbook

在触发警报时，[playbook](../../sentinel/tutorial-respond-threats-playbook.md) 可用于自动缓解。 可以手动运行这些 playbook，也可以在触发特定警报时自动运行。 可以在管理租户或客户租户中部署 playbook，并根据哪个租户的用户将需要采取措施来响应安全威胁的响应过程来配置响应程序。

## <a name="create-cross-tenant-workbooks"></a>创建跨租户工作簿

[Azure Sentinel 中 Azure Monitor 工作簿](../../sentinel/overview.md#workbooks)可帮助你可视化和监视已连接数据源中的数据，以获取见解。 可以在 Azure Sentinel 中使用内置工作簿模板，也可以为方案创建自定义工作簿。

你可以在管理租户中部署工作簿并创建缩放仪表板，以跨客户租户监视和查询数据。 有关详细信息，请参阅[跨工作区检视](../../sentinel/extend-sentinel-across-workspaces-tenants.md#using-cross-workspace-workbooks)。 

你还可以直接在你管理的单个租户中部署工作簿，以便为特定于该客户的方案进行管理。

## <a name="run-log-analytics-and-hunting-queries-across-azure-sentinel-workspaces"></a>跨 Azure Sentinel 工作区运行 Log Analytics 和搜寻查询

在管理租户中集中创建并保存用于威胁检测的 Log Analytics 查询，包括[搜寻查询](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-hunting)。 然后，可以通过使用 Union 运算符和工作区 () 表达式在所有客户的 Azure Sentinel 工作区中运行这些查询。 有关详细信息，请参阅[跨工作区查询](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-querying)。

## <a name="use-automation-for-cross-workspace-management"></a>使用自动化进行跨工作区管理

可以使用自动化来管理多个 Azure Sentinel 工作区，并配置[搜寻查询](../../sentinel/hunting.md)、playbook 和工作簿。 有关详细信息，请参阅[使用自动化的跨工作区管理](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-management-using-automation)。

## <a name="monitor-security-of-office-365-environments"></a>监视 Office 365 环境的安全性

结合使用 Azure Lighthouse 和 Azure Sentinel 来跨租户监视 Office 365 环境的安全性。 首先，[必须在托管租户中启用开箱即用的 Office 365 数据连接器](../../sentinel/connect-office-365.md)，以使 Exchange 和 SharePoint 中的用户和管理活动（包括 OneDrive）的信息可引入到托管租户中的 Azure Sentinel 工作区。 这包括有关操作的详细信息，例如文件下载、发送的访问请求、对组事件的更改和邮箱操作以及有关执行操作的用户的信息。 [Office 365 DLP 警报](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-office-365-dlp-events-into-azure-sentinel/ba-p/1031820)也作为内置 office 365 连接器的一部分受到支持。

可以启用 [Microsoft Cloud App Security (MCAS) 连接器](../../sentinel/connect-cloud-app-security.md)，以将警报和 Cloud Discovery 日志流式传输到 Azure Sentinel。 这使你可以了解云应用程序，获得复杂的分析来识别和应对网络威胁，并控制数据的传输方式。 可以[使用通用事件格式 (CEF) 使用](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-box-com-activity-events-via-microsoft-cloud-app-security/ba-p/1072849) MCAS 的活动日志。

设置 Office 365 数据连接器后，可以使用跨租户 Azure Sentinel 功能，如查看和分析工作簿中的数据、使用查询创建自定义警报，以及配置行动手册以应对威胁。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Sentinel](../../sentinel/overview.md)。
- 查看 [Azure Sentinel 定价页](https://azure.microsoft.com/pricing/details/azure-sentinel/)。
- 了解[跨租户管理体验](../concepts/cross-tenant-management-experience.md)。

