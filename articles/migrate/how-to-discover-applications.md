---
title: 通过 Azure Migrate 发现本地服务器上的应用
description: 了解如何通过 Azure Migrate Server 评估发现本地服务器上的应用、角色和功能。
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/10/2020
ms.openlocfilehash: 8266b585881546b37bbb21b82780ab26d85dada7
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048074"
---
# <a name="discover-installed-applications-roles-and-features-software-inventory-and-sql-server-instances-and-databases"></a>发现已安装的应用程序、角色和功能 (软件清单) 以及 SQL Server 实例和数据库

本文介绍如何使用 Azure Migrate： Server 评估工具发现已安装的应用程序、角色和功能 (软件清单) ，以及在 VMware 环境中运行的服务器上的 SQL Server 实例和数据库。

执行软件清单有助于识别和定制适用于你的工作负荷的 Azure 迁移路径。 软件清单使用 Azure Migrate 设备，使用服务器凭据执行发现。 它完全无代理，服务器上未安装任何要收集此数据的代理。

> [!NOTE]
> 软件清单目前仅针对 VMware 环境中运行的服务器提供预览版，并且仅限发现。 目前，我们不提供基于应用程序的评估。<br/> 在 VMware 环境中运行的 SQL Server 实例和数据库的发现和评估现在为预览版。 若要试用此功能，请使用 [**此链接**](https://aka.ms/AzureMigrate/SQL) 在 **澳大利亚东部** 区域中创建一个项目。 如果你已有一个澳大利亚东部的项目，并且想要尝试此功能，请确保已在门户上完成这些 [**先决条件**](how-to-discover-sql-existing-project.md) 。

## <a name="before-you-start"></a>准备工作

- 请确保已 [创建了一个 Azure Migrate 项目](./create-manage-projects.md) ，并在其中添加了 Azure Migrate： Server 评估工具。
- 查看 [VMware 要求](migrate-support-matrix-vmware.md#vmware-requirements) 以执行软件清单。
- 在设置设备之前，请查看 [设备要求](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) 。
- 在服务器上启动软件清单之前，请查看 [应用程序发现要求](migrate-support-matrix-vmware.md#application-discovery-requirements) 。

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>部署和配置 Azure Migrate 设备

1. [查看](migrate-appliance.md#appliance---vmware) 部署 Azure Migrate 设备的要求。
2. 查看设备需要在 [公共](migrate-appliance.md#public-cloud-urls) 和 [政府云中](migrate-appliance.md#government-cloud-urls)访问的 Azure url。
3. [查看](migrate-appliance.md#collected-data---vmware)设备在发现和评估期间收集的数据。
4. [注意](migrate-support-matrix-vmware.md#port-access-requirements)设备的端口访问要求。
5. [部署 Azure Migrate 设备](how-to-set-up-appliance-vmware.md) 以启动发现。 若要部署设备，请下载 .OVA 模板并将其导入 VMware 以创建在 vCenter Server 中运行的服务器。 部署设备后，需要将其注册到 Azure Migrate 项目，并将其配置为启动发现。
6. 配置设备时，需要在设备配置管理器中指定以下各项：
    - 要连接到的 vCenter Server 的详细信息。
    - vCenter Server 用于发现 VMware 环境中的服务器的凭据。
    - 可以是域/Windows (非域) /Linux (非域) 凭据的服务器凭据。 [了解](add-server-credentials.md) 有关如何提供凭据的详细信息以及如何处理凭据的详细信息。

## <a name="verify-permissions"></a>验证权限

- 你需要 [创建一个 vCenter Server 只读帐户](./tutorial-discover-vmware.md#prepare-vmware) 来进行发现和评估。 只读帐户需要为 **虚拟机**  >  **来宾操作** 启用特权，以便与服务器进行交互以执行软件清单。
- 在应用程序发现的设备配置管理器上，你可以添加多个域和非域 (Windows/Linux) 凭据。你需要为 Windows server 使用来宾用户帐户，并且需要为所有 Linux 服务器 (非 sudo 访问) 的常规/普通用户帐户。[了解](add-server-credentials.md) 有关如何提供凭据的详细信息以及如何处理凭据的详细信息。

### <a name="add-credentials-and-initiate-discovery"></a>添加凭据并启动发现

1. 打开设备配置管理器，完成装置的先决条件检查和注册。
2. 导航到 " **管理凭据和发现源** " 面板。
1.  在 **步骤1：提供 vCenter Server 凭据** 中，单击 " **添加凭据** " 为设备将用于发现在 vCenter Server 上运行的服务器的 vCenter Server 帐户提供凭据。
1. 在 **步骤2：提供 vCenter Server 的详细信息** 中，单击 " **添加发现源** " 以从下拉选择 "凭据的友好名称"，在 "设备配置管理器" 上指定 vCenter Server 实例面板3的 **IP 地址/FQDN** :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="以获取 vCenter Server 详细信息":::
1. 在 **步骤3：提供服务器凭据以执行软件清单、无代理依赖项分析和 SQL Server 实例和数据库的发现**，请单击 " **添加凭据** " 以提供多个服务器凭据以启动软件清单。
1. 单击 " **启动发现" 开始** vCenter Server 发现。

 VCenter Server 发现完成后，设备会 (软件清单) 开始发现已安装的应用程序、角色和功能。 持续时间取决于发现的服务器的数量。 对于500服务器，发现的清单在 Azure Migrate 门户中出现大约1小时。

## <a name="review-and-export-the-inventory"></a>查看并导出清单

软件清单完成后，您可以查看和导出 Azure 门户中的清单。

1. 在 **Azure Migrate-服务器**  >  **Azure Migrate：服务器评估** 中，单击显示的计数打开 "**发现的服务器**" 页。

    > [!NOTE]
    > 在此阶段，你还可以选择启用已发现服务器的依赖关系分析，这样你就可以可视化要评估的服务器之间的依赖关系。 [了解](concepts-dependency-visualization.md) 有关依赖项分析的详细信息。

2. 在 " **发现应用程序** " 列中，单击显示的计数，查看发现的应用程序、角色和功能。
4. 若要导出清单，请在 " **发现的服务器**" 中，单击 " **导出应用程序清单**"。

应用程序清单以 Excel 格式导出和下载。 **应用程序清单** 表显示所有服务器上发现的所有应用。

## <a name="discover-sql-server-instances-and-databases"></a>发现 SQL Server 实例和数据库

- 应用程序发现还会标识在 VMware 环境中运行的 SQL Server 实例。
- 如果未在设备配置管理器上提供 Windows 身份验证或 SQL Server 身份验证凭据，请添加凭据，以便设备可以使用这些凭据连接到相应 SQL Server 实例。

连接后，设备会收集 SQL Server 实例和数据库的配置和性能数据。 SQL Server 配置数据每24小时更新一次，每隔30秒捕获一次性能数据。 因此，对 SQL Server 实例的属性和数据库状态、兼容级别等数据库的属性进行任何更改可能需要长达24小时的时间才能在门户中更新。

## <a name="next-steps"></a>后续步骤

- 为发现的服务器[创建评估](how-to-create-assessment.md)。
- 评估要迁移到 Azure SQL 的[Sql server](./tutorial-assess-sql.md) 。
