---
title: 在 Azure Migrate 中添加迁移工具
description: 了解如何在 Azure Migrate 中添加迁移工具。
ms.topic: article
ms.date: 11/23/2020
ms.openlocfilehash: 5ff5fb54e077896fb6169ad53ce29483cd2c2f89
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545280"
---
# <a name="add-migration-tools"></a>添加迁移工具

本文介绍如何在 [Azure Migrate](./migrate-services-overview.md)中添加迁移工具。

- 如果要添加迁移工具并且尚未设置 Azure Migrate 项目，请遵循此 [文](create-manage-projects.md)。
- 如果已添加用于迁移的 ISV 工具，请 [按照步骤](prepare-isv-movere.md)进行操作以准备使用该工具。

## <a name="select-a-migration-scenario"></a>选择迁移方案

1. 在 Azure Migrate 项目中，单击“概述”。
2. 选择要使用的迁移方案：

    - 若要将计算机和工作负荷迁移到 Azure，请选择 " **评估和迁移服务器**"。
    - 若要迁移本地数据库，请选择 " **评估和迁移数据库**"。
    - 若要迁移本地 web 应用，请选择 "**浏览更多**  >  **web 应用**"。
    - 若要使用 data box 将数据迁移到 Azure，请选择 "**浏览更多**  >  **数据" 框**。

    ![用于选择迁移方案的选项](./media/how-to-migrate/migrate-scenario.png)


## <a name="select-a-server-migration-tool"></a>选择服务器迁移工具

1. 添加工具：

    - 如果使用门户中的 " **评估和迁移服务器** " 选项创建了 Azure Migrate 项目，则 Azure Migrate 服务器迁移工具会自动添加到该项目中。 若要添加其他迁移工具，请在 " **服务器**" 中的 " **迁移工具**" 旁选择 " **添加更多工具**"。
    
         ![添加其他迁移工具的按钮](./media/how-to-migrate/add-migration-tools.png)

    - 如果使用不同的选项创建了项目，但尚未使用任何迁移工具，请在 "**服务器**"  >  **迁移工具** 中选择 **"单击此处"**。

    ![用于添加第一个迁移工具的按钮](./media/how-to-migrate/no-migration-tool.png)

2. 在 **Azure Migrate**  >  **添加工具**"中，选择要添加的工具。 然后选择 " **添加工具**"。

    ![从列表中选择评估工具](./media/how-to-migrate/select-migration-tool.png)


## <a name="select-a-database-migration-tool"></a>选择数据库迁移工具

如果使用门户中的 " **评估和迁移数据库** " 选项创建了 Azure Migrate 项目，则数据库迁移工具将自动添加到项目。 

1. 如果数据库迁移工具不在项目中，请在 **数据库**  >  **评估工具** 中选择 **"单击此处"**。
    
    ![添加数据库迁移工具](./media/how-to-migrate/no-database-migration-tool.png)


2. 在 **Azure Migrate**  >  **添加工具**"中，选择数据库迁移工具。 然后选择 " **添加工具**"。

    ![从列表中选择数据库迁移工具](./media/how-to-migrate/select-database-migration-tool.png)

    

## <a name="select-a-web-app-migration-tool"></a>选择 web 应用迁移工具

如果使用门户中的 "**探索更多** WebApps" 选项创建了一个 Azure Migrate 项目  >  **WebApps** ，则会将 Web 应用迁移工具自动添加到该项目中。 

1. 如果 web 应用迁移工具不在项目中，请在 **web 应用**  >  **评估工具** 中选择 **"单击此处"**。

    ![添加 web 应用迁移工具](./media/how-to-migrate/no-web-app-migration-tool.png)
 

2. 在 **Azure Migrate**  >  **添加工具**"中，选择" Web 应用迁移工具 "。 然后选择 " **添加工具**"。

    ![从列表中选择 webapp 评估工具](./media/how-to-migrate/select-web-app-migration-tool.png)


## <a name="order-an-azure-data-box"></a>排序 Azure Data Box

若要将大量数据迁移到 Azure，可对脱机数据传输进行 Azure Data Box 排序。

1. 在 " **概述**" 中，选择 " **浏览更多**"。
2. 在 " **浏览更多**" 中，选择 **Data box**。
3. 在 **Data Box 开始**"中，选择对 Data Box 进行排序时要使用的订阅和资源组。
4. **传输类型** 是导入到 Azure。 指定数据所在的国家/地区，以及要将数据传输到的 Azure 区域。 
5. 单击 " **应用** " 以保存设置。

## <a name="next-steps"></a>后续步骤

使用适用于 [hyper-v](tutorial-migrate-hyper-v.md) 或 [VMware](tutorial-migrate-vmware.md) Vm 的 Azure Migrate 服务器迁移工具尝试迁移。
