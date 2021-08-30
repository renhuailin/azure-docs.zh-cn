---
title: Azure Migrate 支持矩阵
description: 汇总了 Azure Migrate 服务的支持设置和限制。
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: b98695e9f7d91ae72ca13714814eeca3bde23c74
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736462"
---
# <a name="azure-migrate-support-matrix"></a>Azure Migrate 支持矩阵

可以使用 [Azure Migrate 服务](./migrate-services-overview.md)来评估服务器并将其迁移到 Microsoft Azure 云。 本文汇总了 Azure Migrate 方案和部署的一般支持设置和限制。

## <a name="supported-assessmentmigration-scenarios"></a>支持的评估/迁移方案

下表汇总了支持的发现、评估和迁移方案。

**部署** | **详细信息**
--- | ---
**发现** | 可以发现服务器元数据和动态性能数据。
**软件清单** | 可以发现 VMware VM 上运行的应用、角色和功能。 目前，此功能仅限于发现。 评估目前处于服务器级别。 我们尚未提供基于应用、角色或功能的评估。
**评估** | 评估 VMware VM、Hyper-V VM 和物理服务器上运行的本地工作负荷和数据。 使用“Azure Migrate：发现和评估”、Microsoft 数据迁移助手 (DMA) 以及其他工具和 ISV 产品/服务进行评估。
**迁移** | 将物理服务器、VMware VM、Hyper-V VM、物理服务器以及基于云的 VM 上运行的工作负荷和数据迁移到 Azure。 使用“Azure Migrate：发现和评估”、Azure 数据库迁移服务 (DMS) 以及其他工具和 ISV 产品/服务进行迁移。

> [!NOTE]
> 目前，ISV 工具无法将数据发送到 Azure 政府中的 Azure Migrate。 可以使用集成的 Microsoft 工具，也可以独立使用合作伙伴工具。

## <a name="supported-tools"></a>支持的工具

表中汇总了特定工具支持。

**工具** | 评估 | **迁移**
--- | --- | ---
Azure Migrate：发现和评估 | 评估 [VMware VM](./tutorial-discover-vmware.md)、[Hyper-V VM](./tutorial-discover-hyper-v.md) 和[物理服务器](./tutorial-discover-physical.md)。 |  不可用 (N/A)
Azure Migrate:服务器迁移 | 不可用 | 迁移 [VMware VM](tutorial-migrate-vmware.md)、[Hyper-V VM](tutorial-migrate-hyper-v.md) 和[物理服务器](tutorial-migrate-physical-virtual-machines.md)。
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | 不可用 | 迁移 VMware VM、Hyper-V VM、物理服务器和其他云工作负载。
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| 评估 VMware VM、Hyper-V VM、物理服务器和其他云工作负载。 | 不可用
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | 评估 VMware VM、Hyper-V VM、物理服务器和其他云工作负荷。 |  迁移 VMware VM、Hyper-V VM、物理服务器、公有云工作负荷。
[设备 42](https://go.microsoft.com/fwlink/?linkid=2097158) | 评估 VMware VM、Hyper-V VM、物理服务器和其他云工作负载。| 不可用
[DMA](/sql/dma/dma-overview) | 评估 SQL Server 数据库。 | 不可用
[DMS](../dms/dms-overview.md) | 不可用 | 迁移 SQL Server、Oracle、MySQL、PostgreSQL、MongoDB。
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | 评估虚拟桌面基础结构 (VDI) | 不可用
[Movere](https://www.movere.io/) | 评估 VMware VM、Hyper-V VM、Xen VM、物理服务器、工作站（包括 VDI）和其他云工作负荷。 | 不可用
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | 不可用 | 迁移 VMware VM、Hyper-V VM、Xen VM、KVM VM、物理服务器和其他云工作负载
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | 评估 VMware VM、Hyper-V VM、物理服务器和其他云工作负载。 | 不可用
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | 评估 VMware VM、Hyper-V VM、物理服务器及其他云工作负荷和 SQL Server 数据库。 | 不可用
[Webapp 迁移助手](https://appmigration.microsoft.com/) | 评估 Web 应用 | 迁移 Web 应用。
[Zerto](https://go.microsoft.com/fwlink/?linkid=2157322) | 不可用 |  迁移 VMware VM、Hyper-V VM、物理服务器和其他云工作负载。

## <a name="project"></a>Project

**支持** | **详细信息**
--- | ---
订阅 | 一个订阅中可以有多个项目。
Azure 权限 | 用户需要订阅中的“参与者”或“所有者”权限才能创建项目。
VMware VM  | 在单个项目中最多评估 35,000 个 VMware VM。
Hyper-V VM    | 在单个项目中最多评估 35,000 个 Hyper-V VM。

一个项目可以同时包含 VMware VM 和 Hyper-V VM，只要不超过评估限制即可。

## <a name="azure-permissions"></a>Azure 权限

若要让 Azure Migrate 与 Azure 配合使用，需要以下权限才能开始评估和迁移服务器。

**任务** | **权限** | **详细信息**
--- | --- | ---
创建一个项目 | Azure 帐户需要创建项目的权限。 | 针对 [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account)、[Hyper-V](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) 或[物理服务器](./tutorial-discover-physical.md#prepare-an-azure-user-account)进行设置。
注册 Azure Migrate 设备| Azure Migrate 使用轻型 [Azure Migrate 设备](migrate-appliance.md)通过“Azure Migrate：发现和评估”来发现和评估服务器，并通过“Azure Migrate：服务器迁移”来执行 VMware VM 的[无代理迁移](server-migrate-overview.md)。 此设备发现服务器，并将元数据和性能数据发送到 Azure Migrate。<br/><br/> 在注册过程中，注册提供程序（Microsoft.OffAzure、Microsoft.Migrate 和 Microsoft.KeyVault）将注册到在设备中选择的订阅，以便订阅可使用资源提供程序。 若要注册，你需要订阅的“参与者”或“所有者”访问权限。<br/><br/> VMware - 在载入过程中，Azure Migrate 将创建两个 Azure Active Directory (Azure AD) 应用。 第一个应用在设备代理与 Azure Migrate 服务之间通信。 应用无权进行 Azure 资源管理调用或对资源具有 Azure RBAC 访问权限。 第二个应用可访问在用户订阅中创建的仅用于无代理 VMware 迁移的 Azure Key Vault。 在无代理迁移中，Azure Migrate 需要创建一个 Key Vault，用于管理订阅中复制存储帐户的访问密钥。 从设备启动发现时，将具有对 Azure Key Vault（在客户租户中）的 Azure RBAC 访问权限。<br/><br/> Hyper-V - 在载入过程中。 Azure Migrate 创建一个 Azure AD 应用。 该应用在设备代理与 Azure Migrate 服务之间通信。 应用无权进行 Azure 资源管理调用或对资源具有 Azure RBAC 访问权限。 | 针对 [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account)、[Hyper-V](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) 或[物理服务器](./tutorial-discover-physical.md#prepare-an-azure-user-account)进行设置。
创建用于 VMware 无代理迁移的密钥保管库 | 若要使用无代理 Azure Migrate 服务器迁移工具迁移 VMware VM，Azure Migrate 需要创建一个 Key Vault，用于管理订阅中复制存储帐户的访问密钥。 若要创建保管库，需要在项目所在的资源组中设置权限（所有者或参与者和用户访问管理员）。 | [设置](./tutorial-discover-vmware.md#prepare-an-azure-user-account)权限。

## <a name="supported-geographies-public-cloud"></a>支持的地理区域（公有云）

可以在公有云的许多地理位置中创建项目。

- 尽管只能在这些地理区域中创建项目，但可以针对其他目标位置评估或迁移服务器。
- 项目地域仅用于存储已发现的元数据。
- 在创建项目时选择一个地域。 将在该地域中的某个区域创建项目和相关资源。 此区域由 Azure Migrate 服务分配。

**地域** | **元数据存储位置**
--- | ---
非洲 | 南非或北非
亚太区 | 东亚或东南亚
澳大利亚 | 澳大利亚东部或澳大利亚东南部
巴西 | 巴西南部
Canada | 加拿大中部或加拿大东部
欧洲 | 欧洲北部或欧洲西部
法国 | 法国中部
德国 | 德国中西部
印度 | 印度中部或印度南部
日本 |  日本东部或日本西部
韩国 | 韩国中部或韩国南部
挪威 | 挪威东部
瑞士 | 瑞士北部
阿拉伯联合酋长国 | 阿拉伯联合酋长国北部
United Kingdom | 英国南部或英国西部
United States | 美国中部或美国西部 2

> [!NOTE]
> 对于瑞士地域，瑞士西部仅适用于 REST API 用户并且需要一个批准的订阅。

## <a name="supported-geographies-azure-government"></a>支持的地理区域（Azure 政府）

**任务** | **地域** | **详细信息**
--- | --- | ---
创建项目 | United States | 元数据存储在 US Gov 亚利桑那州、US Gov 弗吉尼亚州
目标评估 | United States | 目标区域：US Gov 亚利桑那州、US Gov 弗吉尼亚州、US Gov 德克萨斯州
目标复制 | United States | 目标区域：US DoD 中部、US DoD 东部、US Gov 亚利桑那州、US Gov 爱荷华州、US Gov 德克萨斯州、US Gov 弗吉尼亚州

## <a name="vmware-assessment-and-migration"></a>VMware 评估和迁移

[查看](migrate-support-matrix-vmware.md) VMware VM 的“Azure Migrate：发现和评估”以及“Azure Migrate：服务器迁移”支持矩阵。

## <a name="hyper-v-assessment-and-migration"></a>Hyper-V 评估和迁移

[查看](migrate-support-matrix-hyper-v.md) Hyper-V VM 的“Azure Migrate：发现和评估”以及“Azure Migrate：服务器迁移”支持矩阵。

## <a name="azure-migrate-versions"></a>Azure Migrate 版本

有两个版本的 Azure Migrate 服务：

- 当前版本：使用此版本可以创建新的项目，发现本地评估，并安排评估和迁移。 [了解详细信息](whats-new.md)。
- **以前版本**：如果客户使用的是以前版本的 Azure Migrate（仅支持对本地 VMware VM 的评估），则现在应使用当前版本。 在以前的版本中，已无法创建新的项目，也无法执行新发现。

## <a name="next-steps"></a>后续步骤

- [评估 VMware VM](./tutorial-assess-vmware-azure-vm.md) 以进行迁移。
- [评估 HYPER-V VM](tutorial-assess-hyper-v.md) 以进行迁移。
