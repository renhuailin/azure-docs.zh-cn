---
title: 使用 Azure Migrate 发现本地服务器上的软件清单
description: 了解如何使用 Azure Migrate 发现和评估来发现本地服务器上的软件清单。
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: c337229a8e9a4fa98e80c955c317813950c51bfb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732654"
---
# <a name="discover-installed-software-inventory-web-apps-and-sql-server-instances-and-databases"></a>发现已安装的软件清单、Web 应用以及 SQL Server 实例和数据库

本文介绍如何使用 Azure Migrate: 发现和评估工具来发现在 VMware 环境中运行的服务器上已安装的软件清单、Web 应用以及 SQL Server 实例和数据库。

执行软件清单有助于确定和定制适用于你的工作负载的 Azure 迁移路径。 软件清单使用 Azure Migrate 设备，通过服务器凭据执行发现。 它是完全无代理的，服务器上未安装代理来收集此数据。

## <a name="before-you-start"></a>准备工作

- 确保[已创建一个项目](./create-manage-projects.md)，并在其中添加了“Azure Migrate: 发现和评估”工具。
- 查看有关执行软件清单的 [VMware 要求](migrate-support-matrix-vmware.md#vmware-requirements)。
- 在设置设备之前，查看[设备要求](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements)。
- 在服务器上启动软件清单之前，查看[应用程序发现要求](migrate-support-matrix-vmware.md#software-inventory-requirements)。

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>部署并配置 Azure Migrate 设备

1. [查看](migrate-appliance.md#appliance---vmware) Azure Migrate 设备的部署要求。
2. 查看设备在[公有云](migrate-appliance.md#public-cloud-urls)和[政府云](migrate-appliance.md#government-cloud-urls)中需要访问的 Azure URL。
3. [查看](migrate-appliance.md#collected-data---vmware)设备在发现和评估期间收集的数据。
4. [注意](migrate-support-matrix-vmware.md#port-access-requirements)设备的端口访问要求。
5. [部署 Azure Migrate 设备](how-to-set-up-appliance-vmware.md)以启动发现。 若要部署设备，可下载 OVA 模板并将其导入 VMware，创建一个在 vCenter Server 中运行的服务器。 部署设备后，需要先将其注册到项目并进行配置，然后再启动发现。
6. 配置设备时，需要在设备配置管理器中指定以下信息：
    - 要连接到的 vCenter Server 的详细信息。
    - 用于发现 VMware 环境中的服务器的 vCenter Server 凭据。
    - 服务器凭据，可以是域/Windows（非域）/Linux（非域）凭据。 [详细了解](add-server-credentials.md)如何提供凭据以及我们如何处理凭据。

## <a name="verify-permissions"></a>验证权限

- 需要[创建一个 vCenter Server 只读帐户](./tutorial-discover-vmware.md#prepare-vmware)用于发现和评估。 只读帐户需要为“虚拟机” > “来宾操作”启用的特权，以便与服务器进行交互来执行软件清单 。
- 可以在设备配置管理器上添加多个域和非域 (Windows/Linux) 凭据，以用于应用程序发现。 你需要为 Windows 服务器设置一个来宾用户帐户，为所有 Linux 服务器设置一个常规/普通用户帐户（非 sudo 访问权限）。[详细了解](add-server-credentials.md)如何提供凭据以及如何处理凭据。

### <a name="add-credentials-and-initiate-discovery"></a>添加凭据并启动发现

1. 打开设备配置管理器，完成设备的先决条件检查和注册。
2. 导航到“管理凭据和发现源”面板。
1.  在“步骤 1: 提供 vCenter Server 凭据”中，单击“添加凭据”，为设备要用于发现 vCenter Server 上运行的服务器的 vCenter Server 帐户提供凭据。 
1. 在“步骤 2: 提供 vCenter Server 详细信息”中，单击“添加发现源”，从下拉列表中选择凭据的易记名称，指定 vCenter Server 实例的“IP 地址/FQDN”  :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="设备配置管理器上的面板 3，用于显示 vCenter Server 详细信息":::
1. 在“步骤 3: 提供服务器凭据以在 VMware 环境中执行软件清单、无代理依赖项分析、SQL Server 实例与数据库的发现和 ASP.NET Web 应用的发现”中，单击“添加凭据”以提供多个服务器凭据来启动软件清单 。
1. 单击“开始发现”以启动 vCenter Server 发现。

 vCenter Server 发现完成后，设备将启动已安装的应用程序、角色和功能（软件清单）的发现。 具体时间取决于发现的服务器数量。 如果有 500 个服务器，Azure Migrate 门户大约需要一小时才会显示发现的库存。

## <a name="review-and-export-the-inventory"></a>查看并导出清单

软件清单完成后，可以在 Azure 门户中查看和导出该清单。

1. 在“Azure Migrate - 服务器、数据库和 Web 应用” > “Azure Migrate: 发现和评估”页中，单击显示的计数以打开“发现的服务器”页  。

    > [!NOTE]
    > 在此阶段，还可以选择为发现的服务器启用依赖项分析，以便可视化要评估的服务器之间的依赖项。 [详细了解](concepts-dependency-visualization.md)依赖项分析。

2. 在“软件清单”列中，单击显示的计数以查看发现的应用程序、角色和功能。
4. 若要导出清单，请在“发现的服务器”中单击“导出软件清单” 。

软件清单以 Excel 格式导出和下载。 “软件清单”工作表显示在所有服务器上发现的所有应用。

## <a name="discover-sql-server-instances-and-databases"></a>发现 SQL Server 实例和数据库

- 软件清单还可识别在 VMware 环境中运行的 SQL Server 实例。
- 如果尚未在设备配置管理器上提供 Windows 身份验证或 SQL Server 身份验证凭据，请添加凭据，使设备可使用这些凭据连接到相应 SQL Server 实例。

    > [!NOTE]
    > 设备只能连接到与其建立了网络连接的 SQL Server 实例，而软件盘存本身无需网络连接。

连接后，设备会收集 SQL Server 实例和数据库的配置和性能数据。 SQL Server 配置数据每 24 小时更新一次，性能数据每 30 秒捕获一次。 因此，对 SQL Server 实例和数据库的属性（如数据库状态、兼容级别等）进行的任何更改可能需要长达 24 小时才能在门户中更新。

## <a name="discover-aspnet-web-apps"></a>发现 ASP.NET Web 应用

软件清单识别发现的服务器上存在的 Web 服务器角色。 如果发现服务器已启用 Web 服务器角色，Azure Migrate 将在服务器上执行 Web 应用发现。
用户可以在设备中同时添加域凭据和非域凭据。 请确保使用的帐户在源服务器上具有本地管理员权限。 Azure Migrate 自动将凭据映射到相应的服务器，因此不必手动映射凭据。 最重要的是，这些凭据从不发送到 Microsoft，而是保留在源环境中运行的设备上。
连接该设备后，它会收集 IIS Web 服务器和 ASP.NET Web 应用的配置数据。 Web 应用配置数据每 24 小时更新一次。

## <a name="next-steps"></a>后续步骤

- 为发现的服务器[创建评估](how-to-create-assessment.md)。
- [评估 Web 应用](how-to-create-azure-app-service-assessment.md)以迁移到 Azure 应用服务。
