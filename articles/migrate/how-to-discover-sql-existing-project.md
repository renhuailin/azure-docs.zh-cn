---
title: 在现有的 Azure Migrate 项目中发现 SQL Server 实例
description: 了解如何在现有的 Azure Migrate 项目中发现 SQL Server 实例。
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2021
ms.openlocfilehash: 2de60880b511e43ffb2949a15fec2cf2a94f62fa
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567146"
---
# <a name="discover-sql-server-instances-in-an-existing-project"></a>在现有的项目中发现 SQL Server 实例 

本文介绍如何在 Azure SQL 评估功能预览版推出之前创建的 [Azure Migrate](./migrate-services-overview.md) 项目中发现 SQL Server 实例和数据库。

发现本地计算机上运行的 SQL Server 实例与数据库有助于识别和定制到 Azure SQL 的迁移路径。 Azure Migrate 设备使用有权访问目标服务器上运行的 SQL Server 实例和数据库的域凭据或 SQL Server 身份验证凭据来执行此发现。 此发现过程不使用代理，即，不在目标服务器上安装任何代理。

## <a name="before-you-start"></a>准备工作

- 请确保已完成以下操作：
    - 在宣布推出适用于你所在区域的 SQL 评估功能之前已创建一个 [Azure Migrate 项目](./create-manage-projects.md)
    - 已将 [Azure Migrate: 发现和评估](./how-to-assess.md)工具添加到项目
- 查看[应用发现支持和要求](./migrate-support-matrix-vmware.md#vmware-requirements)。
-  确保运行应用发现的服务器上安装了 PowerShell 2.0 或更高版本，并且已安装 VMware 工具（版本高于 10.2.0）。
- 查看有关部署 Azure Migrate 设备的[要求](./migrate-appliance.md)。
- 验证订阅中是否包含可创建资源的[所需角色](./create-manage-projects.md#verify-permissions)。
- 确保设备能够访问 Internet

## <a name="enable-discovery-of-sql-server-instances-and-databases"></a>启用 SQL Server 实例和数据库的发现

1. 在 Azure Migrate 项目中执行以下操作之一：
    - 在“中心”磁贴上选择“未启用”，或者   :::image type="content" source="./media/how-to-discover-sql-existing-project/hub-not-enabled.png" alt-text="Azure Migrate 中心磁贴，其中的 SQL 发现未启用":::
    - 针对“服务器发现”页中“SQL 实例”列下的任何条目选择“未启用”   :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-not-enabled.png" alt-text="Azure Migrate“已发现的服务器”边栏选项卡，其中的 SQL 发现未启用":::
2. 在“发现 SQL Server 实例和数据库”中执行所需的步骤：
    - 选择“升级”以创建所需的资源。
        :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-upgrade-appliance.png" alt-text="用于升级 Azure Migrate 设备的按钮":::
    - 验证设备上运行的服务是否已更新为最新版本。 为此，请从设备服务器启动设备配置管理器，然后在“设置必备组件”面板中选择“查看设备服务”。
        - 设备及其组件将自动更新 :::image type="content" source="./media/how-to-discover-sql-existing-project/appliance-services-version.png" alt-text="检查设备版本":::
    - 在设备配置管理器的“管理凭据和发现源”面板中，添加对要发现的 SQL Server 实例和数据库拥有 Sysadmin 访问权限的域凭据或 SQL Server 身份验证凭据。
    可以利用设备的自动凭据映射功能，或者根据[此处](./tutorial-discover-vmware.md#start-continuous-discovery)的重点介绍手动将凭据映射到相应的服务器。

    一些需要注意的要点：
    - 请确保已启用软件盘点，或者提供域凭据或非域凭据来实现相同的效果。 必须执行软件盘点才能发现 SQL Server 实例。
    - 添加域凭据后，设备会尝试在 AD 中验证这些凭据。 确保设备服务器与凭据关联到的 AD 服务器已建立通畅的网络连接。 不会验证与 SQL Server 身份验证关联的凭据。

3. 添加所需的凭据后，请选择“开始发现”以开始扫描。

> [!Note]
>在创建 Azure SQL 评估之前，请允许 SQL 发现运行一段时间。 如果不允许 SQL Server 实例和数据库发现完成，相应的实例将在评估报告中标记为“未知”。

## <a name="next-steps"></a>后续步骤

- 了解如何创建 [Azure SQL 评估](./how-to-create-azure-sql-assessment.md)
- 详细了解 [Azure SQL 评估](./concepts-azure-sql-assessment-calculation.md)