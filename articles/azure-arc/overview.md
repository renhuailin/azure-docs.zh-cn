---
title: Azure Arc 概述
description: 了解什么是 Azure Arc，以及它如何帮助客户使用其他 Azure 服务和功能对混合资源进行管理和治理。
ms.date: 05/25/2021
ms.topic: overview
ms.openlocfilehash: 878e1b9166a10a071d3870edcc70d1775fbcd914
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867118"
---
# <a name="azure-arc-overview"></a>Azure Arc 概述

如今，公司正努力控制和管理日益复杂的环境。 这些环境跨数据中心、多个云和边缘扩展。 每个环境和云都具有自己的一组互不关联的管理工具，你需要学习和操作这些工具。

新的 DevOps 和 ITOps 操作模型很难并行实现，因为现有工具无法为新的云本机模式提供支持。

Azure Arc 通过提供一致的多云和本地管理平台，从而简化了治理和管理。 使用 Azure Arc，你能够：
* 通过将现有非 Azure 本地或其他云资源投影到 Azure 资源管理器中，使用单一虚拟管理平台来管理整个环境。 
* 管理虚拟机、Kubernetes 群集和数据库，就像它们在 Azure 中运行一样。 
* 不管它们在何处，你都可以使用熟悉的 Azure 服务和管理功能。 
* 继续使用传统的 ITOps，同时引入 DevOps 做法，以支持环境中的新云本机模式。
* 将自定义位置配置为已启用 Azure Arc 的 Kubernetes 群集、群集连接和群集扩展上的抽象层。  

:::image type="content" source="./media/overview/azure-arc-control-plane.png" alt-text="Azure Arc 管理控制平面关系图" border="false":::

现在，可以通过 Azure Arc 管理 Azure 外部托管的以下资源类型：

* 服务器 - 运行 Windows 或 Linux 的物理计算机和虚拟机。
* Kubernetes 群集 - 支持多个 Kubernetes 分发。
* Azure 数据服务 - Azure SQL 托管实例和 PostgreSQL 超大规模服务。
* SQL Server - 使用[启用 Azure Arc 的服务器上的 SQL Server](/sql/sql-server/azure-arc/overview) 从任何位置注册实例。

## <a name="what-does-azure-arc-deliver"></a>Azure Arc 提供哪些功能？

Azure Arc 的主要功能包括：

* 在整个环境中为服务器实现一致的清单、管理、治理和安全性。

* 配置 [Azure VM 扩展](./servers/manage-vm-extensions.md)以使用 Azure 管理服务监视、保护和更新服务器。

* 大规模管理和治理 Kubernetes 群集。

* 使用 GitOps 跨 Git 存储库的一个或多个群集部署配置。

*  使用 Azure Policy 实现 Kubernetes 群集的零接触合规性和配置。

* 在任何 Kubernetes 环境（特别是 Azure SQL 托管实例和 Azure Database for PostgreSQL 超大规模，其中包含升级/更新、安全性和监视等权益）上运行 [Azure 数据服务](../azure-arc/kubernetes/custom-locations.md)，就像在 Azure 中运行一样。 使用弹性缩放，应用更新，而不会导致任何应用程序停机，即使没有持续连接到 Azure。

* 在[已启用 Azure Arc 的 Kubernetes](./kubernetes/overview.md) 群集上创建[自定义位置](./kubernetes/custom-locations.md)，将它们用作部署 Azure 服务实例的目标位置。 为[已启用 Azure Arc 的数据服务](./data/create-data-controller-direct-azure-portal.md)、[Azure Arc 上的应用服务](../app-service/overview-arc-integration.md)（包括 Web、函数和逻辑应用）和 [Kubernetes 上的事件网格](../event-grid/kubernetes/overview.md)部署 Azure 服务群集扩展。

* 无论使用的是 Azure 门户、Azure CLI、Azure PowerShell 还是 Azure REST API，都可以查看已启用 Azure Arc 的资源的统一体验。

## <a name="how-much-does-azure-arc-cost"></a>Azure Arc 的费用是多少？

下面是 Azure Arc 目前可用功能的定价详细信息。

### <a name="arc-enabled-servers"></a>已启用 Arc 的服务器

提供以下 Azure Arc 控制平面功能，而无需额外费用：

* 通过 Azure 管理组和标记来整理资源。

* 通过 Azure Resource Graph 进行搜索和索引。

* 通过 RBAC 和订阅实现访问和安全性。

* 通过模板和扩展实现环境和自动化。

* 更新管理。

在已启用 Arc 的服务器上使用的任何 Azure 服务（例如 Azure 安全中心或 Azure Monitor）都将按照该服务的定价收费。 有关详细信息，请参阅 [Azure 定价页](https://azure.microsoft.com/pricing/)。

### <a name="azure-arc-enabled-kubernetes"></a>已启用 Azure Arc 的 Kubernetes

在已启用 Arc 的 Kubernetes 上使用的任何 Azure 服务（例如 Azure 安全中心或 Azure Monitor）都将按照该服务的定价收费。 有关已启用 Azure Arc 的 Kubernetes 上的配置定价的详细信息，请参阅 [Azure 定价页](https://azure.microsoft.com/pricing/)。

### <a name="azure-arc-enabled-data-services"></a>已启用 Azure Arc 的数据服务

有关信息，请参阅 [Azure 定价页](https://azure.microsoft.com/pricing/)。

## <a name="next-steps"></a>后续步骤

* 若要了解有关已启用 Azure Arc 的服务器的详细信息，请参阅以下[概述](./servers/overview.md)

* 若要了解有关已启用 Azure Arc 的 Kubernetes 的详细信息，请参阅以下[概述](./kubernetes/overview.md)

* 若要了解有关已启用 Azure Arc 的数据服务的详细信息，请参阅以下[概述](https://azure.microsoft.com/services/azure-arc/hybrid-data-services/)

* 若要了解有关已启用 Azure Arc 的服务器上的 SQL Server 的详细信息，请参阅以下[概述](/sql/sql-server/azure-arc/overview)

* 从[快速入门概念证明](https://azurearcjumpstart.io/azure_arc_jumpstart/)体验已启用 Azure Arc 的服务
