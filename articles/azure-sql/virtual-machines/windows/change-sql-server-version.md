---
title: SQL Server 版本的就地更改
description: 了解如何在 Azure 中更改 SQL Server 虚拟机的版本。
services: virtual-machines-windows
documentationcenter: na
author: ramakoni1
manager: ramakoni1
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/08/2020
ms.author: RamaKoni
ms.reviewer: sqlblt, daleche
ms.custom: seo-lt-2019
ms.openlocfilehash: 8428f9ce69bc58a206eb5c60b3602ffbb17f7c19
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225662"
---
# <a name="in-place-change-of-sql-server-version-on-azure-vm"></a>Azure VM 上 SQL Server 版本的就地更改

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介绍如何更改 Microsoft Azure 中 Windows 虚拟机 (VM) 上的 Microsoft SQL Server 版本。

## <a name="prerequisites"></a>先决条件

若要就地升级 SQL Server ，请满足以下条件：

- 需要具有 SQL Server 所需版本的安装介质。 如果客户有[软件保障](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)，则可以从[批量许可中心](https://www.microsoft.com/Licensing/servicecenter/default.aspx)获取其安装介质。 没有软件保障的客户可以使用 Azure 市场 SQL Server VM 映像中的安装介质，它具有 SQL Server 的更高版本（通常位于 C:\SQLServerFull 中）。
- 版次升级应遵循[支持升级路径](/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15)。

## <a name="planning-for-version-change"></a>规划版本更改

建议在更改版本之前查看以下各项：

1. 检查升级版本中的新增功能：

   - [SQL 2019](/sql/sql-server/what-s-new-in-sql-server-ver15) 中的新增功能
   - [SQL 2017](/sql/sql-server/what-s-new-in-sql-server-2017) 中的新增功能
   - [SQL 2016](/sql/sql-server/what-s-new-in-sql-server-2016) 中的新增功能


1. 建议检查更新版本的[兼容性证书](/sql/database-engine/install-windows/compatibility-certification)，以便可以使用数据库兼容性模式来最大程度地降低升级的影响。
1. 阅读下文，成功升级：

   - [视频：升级 SQL Server | Pam Lahoud & Pedro Lopes | 20 Years of PASS](https://www.youtube.com/watch?v=5RPkuQHcxxs&feature=youtu.be)
   - [AB 测试的数据库实验助手 (DEA)](/sql/dea/database-experimentation-assistant-overview)
   - [使用查询优化助手升级数据库](/sql/relational-databases/performance/upgrade-dbcompat-using-qta)
   - [更改数据库兼容性级别和使用查询存储](/sql/database-engine/install-windows/change-the-database-compatibility-mode-and-use-the-query-store)

## <a name="upgrade-sql-version"></a>升级 SQL 版本

> [!WARNING]
> 升级 SQL Server 版本将重新启动 SQL Server 的服务，以及任何关联的服务，如 Analysis Services 和 R Services。

若要升级 SQL Server 版本，请获取升级版本适用的 SQL Server 安装介质，它可[支持 SQL Server 升级路径](/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15)，然后完成以下步骤：

1. 在开始此过程之前，请先备份数据库，包括系统（除开 tempdb）和用户数据库。 此外，还可以使用 Azure 备份服务创建与应用程序一致的 VM 级别备份。
1. 从 SQL Server 安装介质启动 Setup.exe。
1. 安装向导将运行 SQL Server 安装中心。 若要升级 SQL Server 的现有实例，请在导航窗格中选择“安装”，然后选择“从较低版本的 SQL Server 升级”。

   :::image type="content" source="./media/change-sql-server-version/upgrade.png" alt-text="用于升级 SQL Server 版本的选择":::

1. 在“产品密钥”页上，选择相应的选项，指示你是升级到 SQL Server 免费版次，还是你拥有该产品生产版本的 PID 密钥。 有关详细信息，请参阅 [SQL Server 2019 (15.x) 的版次和支持功能](/sql/sql-server/editions-and-components-of-sql-server-version-15)以及[支持的版本和版次升级 (SQL Server 2016)](/sql/database-engine/install-windows/supported-version-and-edition-upgrades)。
1. 选择“下一步”，直至转到“准备升级”页，然后选择“升级”。 在更改生效之前，安装窗口可能会停止响应几分钟。 “完成”页将确认版本升级已完成。 有关升级的分步过程，请参阅[完整步骤](/sql/database-engine/install-windows/upgrade-sql-server-using-the-installation-wizard-setup#procedure)。

   :::image type="content" source="./media/change-sql-server-version/complete-page.png" alt-text="完成页面":::

如果除了更改了 SQL Server 版本外，还更改了其版次，则需更新版次，并参考“在门户中验证版本和版次”部分来更改 SQL VM 实例。

   :::image type="content" source="./media/change-sql-server-version/change-portal.png" alt-text="更改版本元数据":::

## <a name="downgrade-the-version-of-sql-server"></a>降级 SQL Server 版本

若要降级 SQL Server 的版本，需要完全卸载 SQL Server，然后再次安装所需版本。 这类似于全新安装 SQL Server，因为你将不能把更高版本中的早期数据库恢复到新安装的较低版本中。 必须从头开始重建数据库。 如果在升级过程中还更改了 SQL Server 的版次，则将 Azure 门户中 SQL Server VM 的“版次”属性更改为新版次值。 这将更新与此 VM 关联的元数据和计费。

> [!WARNING]
> 不支持 SQL Server 就地降级。

可以按照以下步骤降级 SQL Server 的版本：

1. 确保没有使用任何[仅在更高版本中可用](https://social.technet.microsoft.com/wiki/contents/articles/24222.find-enterprise-only-features-in-your-database.aspx)的功能。
1. 备份所有数据库，包括系统（除开 tempdb）和用户数据库。
1. 将全部所需的服务器级对象（如服务器触发器、角色、登录名、链接服务器、作业、凭据和证书）导出。
1. 如果较低版本中没有用于重建用户数据库的脚本，则必须使用 BCP.exe、SSIS 或 DACPAC 为所有对象编写脚本并导出所有数据。

   确保在编写目标版本、依赖对象和高级选项的脚本时，选择正确的选项。

   :::image type="content" source="./media/change-sql-server-version/scripting-options.png" alt-text="脚本选项":::

1. 完全卸载 SQL Server 和所有关联的服务。
1. 重启 VM。
1. 使用所需程序版本的介质安装 SQL Server。
1. 安装最新的 Service Pack 和累积更新。
1. 将步骤 3 中导出的全部所需的服务器级对象导入。
1. 使用创建的脚本或步骤 4 中的文件，从头开始重建所有所需的用户数据库。

## <a name="verify-the-version-and-edition-in-the-portal"></a>验证门户中的版本和版次

更改 SQL Server 版本后，重新向 [SQL IaaS 代理扩展](sql-agent-extension-manually-register-single-vm.md)注册 SQL Server VM，以便可以使用 Azure 门户查看 SQL Server 的版本。 列出的版本号应反映 SQL Server 的新升级版本和版次。

:::image type="content" source="./media/change-sql-server-version/verify-portal.png" alt-text="验证版本":::

> [!NOTE]
> 如果已注册 SQL IaaS 代理扩展，请[从 RP 注销](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension)，然后重新[注册 SQL VM 资源](sql-agent-extension-manually-register-single-vm.md#full-mode)，以便它可以检测到 VM 上安装的 SQL Server 的正确版本和版次。 这将更新与此 VM 关联的元数据和计费信息。

## <a name="remarks"></a>备注

- 建议在升级完成后，启动备份/更新统计信息/重建索引/检查一致性。 还可以检查单个数据库兼容性级别，确保它们反映了所需的级别。
- 在 VM 上更新 SQL Server 后，确保 Azure 门户中 SQL Server 的“版次”属性与计费的已安装版次编号匹配。
- [更改版次](change-sql-server-edition.md#change-edition-in-portal)是 SQL IaaS 代理扩展的一项功能。 通过 Azure 门户部署 Azure 市场映像会自动将 SQL Server VM 注册到代理扩展中。 但自行安装 SQL Server 的客户需要手动[注册其 SQL Server VM](sql-agent-extension-manually-register-single-vm.md)。
- 如果删除了 SQL Server VM 资源，则会恢复映像的硬编码版次设置。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章：

- [Windows VM 上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Windows VM 上的 SQL Server 常见问题解答](frequently-asked-questions-faq.yml)
- [Windows VM 上的 SQL Server 定价指南](pricing-guidance.md)
- [Windows VM 上的 SQL Server 发行说明](doc-changes-updates-release-notes.md)