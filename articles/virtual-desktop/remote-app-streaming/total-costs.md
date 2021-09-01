---
title: 了解 Azure 虚拟桌面部署总成本 - Azure
description: 如何估算 Azure 虚拟桌面部署的总成本。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 64edffaebf5194e6e8c48466d46cbfc9fd953c92
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113798717"
---
# <a name="understanding-total-azure-virtual-desktop-deployment-costs"></a>了解 Azure 虚拟桌面部署总成本

Azure 虚拟桌面的成本包含两个部分：基础 Azure 资源消耗和授权。 Azure 虚拟桌面成本由拥有 Azure 虚拟桌面部署的组织承担，而非访问部署资源的最终用户。 某些授权费用必须提前支付。 其他许可证和基础资源消耗费用以跟踪使用情况的量表为基础。

在本文中，我们将介绍消耗和授权成本，以及如何在部署 Azure 虚拟桌面之前使用 Azure 定价计算器估算服务成本。 本文还包括有关如何使用 Azure 成本管理在部署 Azure 虚拟桌面之后查看成本的说明。

>[!NOTE]
>为 Azure 虚拟桌面部署付费的客户负责处理其部署的生存期资源管理和成本。 如果所有者不再需要连接到其 Azure 虚拟桌面部署的资源，则应确保妥善删除这些资源。 有关详细信息，请参阅[如何使用 Azure 门户管理 Azure 资源](../../azure-resource-manager/management/manage-resources-portal.md)。

## <a name="consumption-costs"></a>消耗成本

消耗成本是访问 Azure 虚拟桌面主机池的用户产生的所有 Azure 资源使用费的总和。 这些费用来自于主机池中的会话主机虚拟机 (VM)，其中包括 Azure 中其他产品共享的资源，以及任何需要运行其他基础结构方可保持服务可用的标识管理系统（如 Active Directory 域服务 (AD DS) 的域控制器）。

### <a name="session-host-vm-costs"></a>会话主机 VM 成本

在 Azure 虚拟桌面中，会话主机 VM 使用以下三种 Azure 服务：

- 虚拟机（计算）
- 托管磁盘存储（包括每个 VM 的 OS 存储和个人桌面设备的任何数据磁盘）
- 带宽（网络）

这些费用可在 Azure 资源组级别查看，主机池特定资源（包括会话主机 VM）将在该级别分配。 如果一个或多个主机池也被配置为使用付费 Log Analytics 服务来将 VM 数据发送到可选 Azure 虚拟桌面 Insights 功能，则账单中还将计入因相应 Azure 资源组的 Log Analytics 产生的费用。 你可以查看[监视 Windows 虚拟桌面成本定价估算](../azure-monitor-costs.md)，了解详细信息。

在此部分开头列出的三个主 VM 会话主机使用成本中，计算产生的成本通常最高。 为了降低计算成本并优化资源需求与可用性，许多客户选择[自动缩放会话主机](../set-up-scaling-script.md)。

### <a name="domain-controller-costs-for-active-directories"></a>Active Directory 的域控制器成本

域控制器 VM 至少使用以下四种 Azure 服务：

- 虚拟机（计算）
- 托管磁盘存储（包括每个 VM 的 OS 存储和个人桌面设备的任何数据磁盘）
- 带宽（网络）
- 虚拟网络

如果 Azure 虚拟桌面部署依赖域控制器来运行其 Active Directory，则应将其包含在 Azure 虚拟桌面总部署成本中。 托管在 Azure 中的域控制器还将共享[会话主机 VM 成本](#session-host-vm-costs)中介绍的三种 Azure 服务（适用于会话主机 VM），这是因为标准 Azure VM 还必须保持 Active Directory 的标识可用。

但是，域控制器与会话主机 VM 之间存在一些主要差异：

- 域控制器将产生额外的虚拟网络费用，这是因为这些控制器必须与部署之外的其他服务通信。
- 扩展域控制器可用性可能会带来问题，这是因为部署要求域控制器始终可用。

### <a name="shared-service-costs"></a>共享服务成本

根据 Azure 虚拟桌面部署所使用的功能，你可能还需要为以下任意可选功能组合支付 Azure 存储费用：

- [MSIX 应用附加](../what-is-app-attach.md)
- [自定义 OS 映像](../set-up-customize-master-image.md)
- [FSLogix 配置文件](../fslogix-containers-azure-files.md)

这些功能使用 Azure 存储选项，如 [Azure 文件存储](../../storage/files/storage-files-introduction.md)和 [Azure NetApp 文件](../../azure-netapp-files/azure-netapp-files-introduction.md)，这意味着它们可以与 Azure 虚拟桌面以外的其他 Azure 服务共享其存储。 建议为 Azure 虚拟桌面功能购买的存储创建一个单独的存储帐户，以确保可以了解其成本与所用其他 Azure 服务的成本之间的差异。

### <a name="user-access-costs"></a>用户访问成本

你每月需为连接到 Azure 虚拟桌面部署中的应用或桌面设备的每位用户支付用户访问成本。 若要详细了解 Azure 虚拟桌面每用户访问定价如何运作，请参阅[了解授权和每用户访问定价](licensing.md)。

## <a name="predicting-costs-before-deploying-azure-virtual-desktop"></a>部署 Azure 虚拟桌面之前预测成本

现在，你已了解基础知识，让我们开始估算。 为此，我们需要估算消耗和用户访问成本。

### <a name="predicting-consumption-costs"></a>预测消耗成本

在创建部署之前，可以使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)估算 Azure 虚拟桌面消耗成本。 下面介绍如何预测消耗成本：

1. 在定价计算器中，选择“计算”选项卡，以显示 Azure 定价计算器计算选项。

2. 选择“Azure 虚拟桌面”。 此时 Azure 虚拟桌面计算器模块应显示。

3. 在字段中输入部署的值，以根据预期的计算、存储和网络使用情况估算 Azure 月度账单。

>[!NOTE]
>目前，Azure 定价计算器 Azure 虚拟桌面模块只能估算会话主机 VM 和任何可选 Azure 虚拟桌面功能（需要所选部署的存储）的聚合附加存储的消耗成本。 但是，你可以在同一 Azure 定价计算器页内的不同模块中为其他 Azure 虚拟桌面功能添加估算值，以获得更完整或模块化的成本估算情况。
>
>你可以添加额外的 Azure 定价计算器模块，以估算其他部署组件的成本影响，其中包括但不限于：
>
>- 域控制器
>- 其他依赖存储的功能，如自定义 OS 映像、MSIX 应用附加和 Azure Monitor

### <a name="predicting-user-access-costs"></a>预测用户访问成本

用户访问成本取决于每个月连接到部署的用户数量。 若要了解如何估算所需的总用户访问成本，请参阅[估算 Azure 虚拟桌面的每用户应用流式传输成本](streaming-costs.md)。

## <a name="viewing-costs-after-deploying-azure-virtual-desktop"></a>在部署 Azure 虚拟桌面后查看成本

部署 Azure 虚拟桌面后，可以使用 [Azure 成本管理](../../cost-management-billing/cost-management-billing-overview.md)来查看账单。 本部分将介绍如何查找当前服务的价格。

### <a name="viewing-bills-for-consumption-costs"></a>查看消耗成本的账单

有了适当的 [Azure RBAC](../../role-based-access-control/rbac-and-directory-admin-roles.md) 权限，组织中的用户（例如计费管理员）可以使用[成本分析工具](../../cost-management-billing/costs/cost-analysis-common-uses.md)，并通过 [Azure 成本管理](../../cost-management-billing/cost-management-billing-overview.md)查找 Azure 账单，以跟踪一个 Azure 订阅或多个订阅下的每月 Azure 虚拟桌面消耗成本。

### <a name="viewing-bills-for-user-access-costs"></a>查看用户访问成本的账单

对于任何已注册的订阅，用户访问成本的每个计费周期、消耗成本和其他 Azure 费用都将显示在 Azure 账单上。

## <a name="next-steps"></a>后续步骤

若要更清楚地了解具体的部署部件所需的成本，请查看以下文章：

- [了解授权和每用户访问定价](licensing.md)