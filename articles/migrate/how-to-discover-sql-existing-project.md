---
title: 发现现有 Azure Migrate 项目中的 SQL Server 实例
description: 了解如何发现现有 Azure Migrate 项目中的 SQL Server 实例。
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 3dc9b178b9aa22991230f4cc6a9d54b44cf09b4e
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098802"
---
# <a name="discover-sql-server-instances-in-an-existing-project"></a>发现现有项目中的 SQL Server 实例 

本文介绍如何发现在 Azure SQL 评估预览功能之前创建的 [Azure Migrate](./migrate-services-overview.md) 项目中 SQL Server 实例和数据库。

发现本地计算机上运行 SQL Server 实例和数据库有助于识别和定制 Azure SQL 的迁移路径。 Azure Migrate 设备使用域凭据或 SQL Server 身份验证凭据执行此发现，这些凭据具有对目标服务器上运行的 SQL Server 实例和数据库的访问权限。 此发现过程是无代理的，即目标服务器上未安装任何内容。

> [!Note]
> 在 VMware 环境中运行的 SQL Server 实例和数据库的发现和评估现在为预览版。 若要试用此功能，请使用 [**此链接**](https://aka.ms/AzureMigrate/SQL) 在 **澳大利亚东部** 区域中创建一个项目。 如果你已有一个澳大利亚东部的项目，并且想要尝试此功能，请确保已完成本文中的 [**先决条件**](how-to-discover-sql-existing-project.md) 。

## <a name="before-you-start"></a>准备工作

- 请确保已完成以下操作： 
    - 在针对你所在区域的 SQL 评估功能公告之前创建了[Azure Migrate 项目](./create-manage-projects.md)
    - 向项目添加了 [Azure Migrate：发现和评估](./how-to-assess.md) 工具
- 查看 [应用发现支持和要求](./migrate-support-matrix-vmware.md#vmware-requirements)。
-  请确保运行应用发现的服务器安装了 PowerShell 2.0 版或更高版本，并且已安装 10.2.0) 之前的 VMware 工具 (。
- 检查部署 Azure Migrate 设备的 [要求](./migrate-appliance.md) 。
- 验证是否在订阅中拥有创建资源 [所需的角色](./create-manage-projects.md#verify-permissions) 。
- 确保你的设备能够访问 internet

## <a name="enable-discovery-of-sql-server-instances-and-databases"></a>启用 SQL Server 实例和数据库的发现

1. 在 Azure Migrate 项目中，
    - 在 "中心" 磁贴上选择 " **未启用** "，或在   :::image type="content" source="./media/how-to-discover-sql-existing-project/hub-not-enabled.png" alt-text="未启用 SQL 发现的情况下 Azure Migrate 集线器磁贴":::
    - 对于 "SQL 实例" 列下的 "服务器发现" 页中的任何条目，选择 " **未启用** "   :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-not-enabled.png" alt-text="Azure Migrate 发现的服务器边栏选项卡未启用 sql 发现":::
2. 在发现 SQL Server 实例和数据库中，请按照以下步骤引起：
    - 选择 " **升级**" 以创建所需的资源。
        :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-upgrade-appliance.png" alt-text="用于升级 Azure Migrate 设备的按钮":::
    - 验证是否已将设备上运行的服务更新为最新版本。 为此，请从设备服务器启动设备配置管理器，然后从 "设置先决条件" 面板中选择 "查看设备服务"。
        - 设备及其组件会自动更新 :::image type="content" source="./media/how-to-discover-sql-existing-project/appliance-services-version.png" alt-text="检查设备版本":::
    - 在设备配置管理器的 "管理凭据和发现源" 面板中，添加对要发现的 SQL Server 实例和数据库具有 Sysadmin 访问权限的域或 SQL Server 身份验证凭据。 
    你可以利用设备的自动凭据映射功能，或手动将凭据映射到 [此处](https://review.docs.microsoft.com/azure/migrate/tutorial-discover-vmware?branch=release-migrate-sql-scenario#start-continuous-discovery)突出显示的相应服务器。
        
    一些需要注意的要点：
    - 请确保已启用软件清单，或者提供域或非域凭据以启用相同的凭据。 若要发现 SQL Server 实例，必须执行软件清单。
    - 设备将在添加时尝试验证 AD 的域凭据。 请确保设备服务器能够在与凭据关联的 AD 服务器上看到网络线路。 不会验证与 SQL Server 身份验证关联的凭据。 

3. 添加所需的凭据后，请选择 "开始发现" 以开始扫描。

> [!Note] 
>请在创建 Azure SQL 评估之前，允许 SQL 发现运行一段时间。 如果不允许 SQL Server 实例和数据库的发现完成，则相应的实例会在评估报表中标记为 " **未知** "。

## <a name="next-steps"></a>后续步骤

- 了解如何创建 [AZURE SQL 评估](./how-to-create-azure-sql-assessment.md)
- 了解有关[AZURE SQL 评估](./concepts-azure-sql-assessment-calculation.md)的详细信息