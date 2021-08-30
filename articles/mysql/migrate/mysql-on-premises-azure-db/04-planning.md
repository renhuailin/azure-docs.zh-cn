---
title: 将数据从本地 MySQL 迁移到 Azure Database for MySQL：计划
description: Azure 登陆区域是定义为云迁移项目最终放置位置的目标环境。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 86dca0c57b473ca759c7dd2a685707c09dd11f05
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748193"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-planning"></a>将数据从本地 MySQL 迁移到 Azure Database for MySQL：计划

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>先决条件

[评估](03-assessment.md)

## <a name="landing-zone"></a>登陆区域

[Azure 登陆区域](/azure/cloud-adoption-framework/ready/landing-zone/)是定义为云迁移项目最终放置位置的目标环境。 在大多数项目中，对登陆区域进行初始设置时，应通过 ARM 模板对其进行脚本编写。 最后，应使用 PowerShell 或 Azure 门户对其进行自定义以满足工作负载需求。

由于 WWI 设在旧金山，因此 Azure 登陆区域的所有资源均在 `US West 2` 区域中创建。 为支持迁移，创建了以下资源：

- [Azure Database for MySQL](../../quickstart-create-mysql-server-database-using-azure-portal.md)

- [Azure 数据库迁移服务 (DMS)](../../../dms/quickstart-create-data-migration-service-portal.md)

- [Express Route](../../../expressroute/expressroute-introduction.md)

- 采用[中心辐射式设计](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)的 [Azure 虚拟网络](../../../virtual-network/quick-create-portal.md)，并建立了相应的[虚拟网络对等互连](../../../virtual-network/virtual-network-peering-overview.md)。

- [应用服务](../../../app-service/overview.md)

- [应用程序网关](../../../load-balancer/quickstart-load-balancer-standard-internal-portal.md?tabs=option-1-create-internal-load-balancer-standard)

- [专用终结点](../../../private-link/private-endpoint-overview.md)，适用于应用程序服务和 MySQL 实例

> [!NOTE]
> 本指南提供了两个 ARM 模板（一个带有专用终结点，一个不带专用终结点），用于为 MySQL 迁移项目部署可能的 Azure 登陆区域。 专用终结点 ARM 模板提供了更安全和类似生产环境的场景。 根据要求，可能需要额外的手动 Azure 登陆区域配置。

## <a name="networking"></a>网络

以快速且最优的方式将数据从源系统传递到 Azure Database for MySQL 是迁移项目过程中需要考虑的重要部分。 不可靠的小型连接可能需要管理员多次重启迁移，直到获得成功的结果。 由于网络问题而重启迁移可能会导致浪费精力。

花时间了解和评估源、工具和目标环境之间的网络连接。 在某些情况下，可能需要升级 Internet 连接或配置从本地环境到 Azure 的 ExpressRoute 连接。 创建从本地环境到 Azure 的连接后，下一步是验证所选迁移工具是否可以从源连接到目标。

迁移工具位置决定了网络连接要求。 如下表所示，所选迁移工具必须同时连接到本地计算机和 Azure。 应将 Azure 配置为仅接受来自迁移工具位置的网络流量。

| 迁移工具 | 类型 | 位置 | 入站网络要求 | 出站网络要求 |
|----------------|------|----------|------------------------------|-------------------------------|
| **数据库迁移服务 (DMS)** | 脱机 | Azure| 允许来自外部 IP 的 3306 | 连接到 Azure MySQL 数据库实例的路径 |
| **导入/导出（MySQL Workbench、mysqldump）** | 脱机| 本地 | 允许来自内部 IP 的 3306 | 连接到 Azure MySQL 数据库实例的路径 |
| **导入/导出（MySQL Workbench、mysqldump）** | 脱机| Azure VM | 允许来自外部 IP 的 3306 | 连接到 Azure MySQL 数据库实例的路径 |
| **mydumper/myloader** | 脱机 | 本地 | 允许来自内部 IP 的 3306 | 连接到 Azure MySQL 数据库实例的路径 |
| **mydumper/myloader** | 脱机 | Azure VM | 允许来自外部 IP 的 3306 | 连接到 Azure MySQL 数据库实例的路径 |
| **binlog**  | 脱机 | 本地 | 通过专用终结点允许来自外部 IP 或专用 IP 的 3306 | 每台复制服务器到主服务器的路径 |

其他网络注意事项包括：

- 位于 VNET 中的 DMS 为服务分配了一个[动态公共 IP](/azure/dms/faq#setup)。 在创建过程中，可以将服务放置在通过 [ExpressRoute](../../../expressroute/expressroute-introduction.md) 或通过[站点到站点 VPN](../../../vpn-gateway/tutorial-site-to-site-portal.md) 进行连接的虚拟网络中。

- 使用 Azure 虚拟机运行迁移工具时，向它分配一个公共 IP 地址，然后仅允许它连接到本地 MySQL 实例。

- 出站防火墙必须确保与 Azure Database for MySQL 的出站连接。 MySQL 网关 IP 地址可在 [Azure Database for MySQL 中的连接体系结构](../../concepts-connectivity-architecture.md#azure-database-for-mysql-gateway-ip-addresses)页面上获取。

## <a name="ssltls-connectivity"></a>SSL/TLS 连接

除了迁移到基于 SSL 的通信时对应用程序的影响外，还需要考虑 SSL/TLS 连接类型。 创建 Azure Database for MySQL 数据库后，请查看 SSL 设置，并阅读 [Azure Database for MySQL 中的 SSL/TLS 连接](../../concepts-ssl-connection-security.md)一文，以了解 TLS 设置对安全状况的影响。

> [!Important]
> 请注意页面上的免责声明。 默认情况下，不强制启用 TLS 版本。 启用 TLS 后，禁用它的唯一方法是重新启用 SSL。

## <a name="wwi-scenario"></a>WWI 场景

WWI 的云团队在特定资源组中针对 Azure Database for MySQL 创建了必要的 Azure 登陆区域资源。 为了创建登陆区域，WWI 决定使用 ARM 模板编写设置和部署脚本。 通过使用 ARM 模板，他们可以根据需要快速清除和重新设置环境。

作为 ARM 模板的一部分，虚拟网络之间的所有连接在中心辐射式体系结构中均配置了对等互连。 数据库和应用程序放置在单独的虚拟网络中。 在应用服务前设置了一个 Azure 应用程序网关，以便将应用服务与 Internet 隔离。 Azure 应用服务使用专用终结点连接到 Azure Database for MySQL。

WWI 最初想测试联机迁移，但 DMS 连接到其本地环境所需的网络设置不支持此操作。 WWI 选择了进行脱机迁移。 使用了 MySQL Workbench 工具来导出本地数据，然后使用了该工具将数据导入 Azure Database for MySQL 实例。

## <a name="planning-checklist"></a>计划清单

- 准备 Azure 登陆区域。 考虑使用 ARM 模板部署，以防必须快速清除和重建环境。

- 验证网络设置。 验证应包括：连接性、带宽、延迟和防火墙配置。

- 确定要使用联机还是脱机数据迁移策略。

- 确定 SSL 证书策略。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [迁移方法](./05-migration-methods.md)
