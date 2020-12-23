---
title: 在 Azure Migrate 中添加评估工具
description: 了解如何在 Azure Migrate 中添加评估工具。
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 9a4ed5542945b8d281ec750d9bbd3a8f444e44d2
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96752254"
---
# <a name="add-assessment-tools"></a>添加评估工具

本文介绍如何在 [Azure Migrate](./migrate-services-overview.md)中添加评估工具。 

- 如果你想要添加一个评估工具，但还没有 Azure Migrate 项目，请遵循此 [文](create-manage-projects.md)。
- 如果已添加 ISV 工具或 Movere，请 [按照步骤](prepare-isv-movere.md)进行操作以准备使用该工具。

## <a name="select-an-assessment-scenario"></a>选择评估方案

1. 在 Azure Migrate 项目中，单击“概述”。
2. 选择评估方案：

    - 若要发现和评估要迁移到 Azure 的计算机和工作负荷，请选择 " **评估和迁移服务器**"。
    - 若要评估本地 SQL Server 数据库，请选择 " **评估和迁移数据库**"。
    - 若要评估或迁移本地 web 应用，请选择 "**浏览更多**  >  **web 应用**"。
    - 若要评估虚拟桌面基础结构，请选择 "**浏览更多**  >  **虚拟桌面基础结构**"。

    ![用于选择评估方案的选项](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>选择服务器评估工具 


1. 添加工具：

    - 如果使用门户中的 " **评估和迁移服务器** " 选项创建了 Azure Migrate 项目，则 Azure Migrate 服务器评估工具会自动添加到项目。 若要添加其他评估工具，请在 "**评估工具**" 旁边的 "**服务器**" 中，选择 "**添加更多工具**"。
    
         ![用于添加其他评估工具的按钮](./media/how-to-assess/add-assessment-tool.png)

    - 如果使用不同的选项创建了项目，但尚未使用任何评估工具，请在 **服务器**  >  **评估工具** 中选择 **"单击此处"**。

        ![用于添加第一个评估工具的按钮](./media/how-to-assess/no-assessment-tool.png)

2. 在 **Azure Migrate**  >  **添加工具**"中，选择要添加的工具。 然后选择 " **添加工具**"。

    ![从列表中选择评估工具](./media/how-to-assess/select-assessment-tool.png)



## <a name="select-a-database-assessment-tool"></a>选择数据库评估工具

1. 添加工具：

    - 如果使用门户中的 " **评估和迁移数据库** " 选项创建了 Azure Migrate 项目，则数据库评估工具会自动添加到项目。 若要添加其他评估工具，请在 "**评估工具**" 旁的 "**数据库**" 中，选择 "**添加更多工具**"。

    - 如果使用不同的选项创建了项目，但尚未拥有任何数据库评估工具，请在 **数据库**  >  **评估工具** 中选择 **"单击此处"**。

2. 在 **Azure Migrate**  >  **添加工具**"中，选择要添加的工具。 然后选择 " **添加工具**"。

    ![从列表中选择数据库评估工具](./media/how-to-assess/select-database-assessment-tool.png)


## <a name="select-a-web-app-assessment-tool"></a>选择 web 应用评估工具

如果使用门户中的 "**探索更多** WebApps" 选项创建了一个 Azure Migrate 项目  >  **WebApps** ，则会将 Web 应用评估工具自动添加到该项目中。 


1. 如果 web 应用评估工具不在项目中，请在 **web 应用**  >  **评估工具** 中选择 **"单击此处"**。
    
    ![添加 web 应用评估工具](./media/how-to-assess/no-web-app-assessment-tool.png)


2. 在 **Azure Migrate**  >  **添加工具**"中，选择 Web 应用评估工具。 然后选择 " **添加工具**"。

    ![从列表中选择数据库迁移工具](./media/how-to-assess/select-web-app-assessment-tool.png)

 


## <a name="next-steps"></a>后续步骤

使用适用于[VMware](./tutorial-discover-vmware.md) Vm、 [hyper-v](./tutorial-discover-hyper-v.md)或[物理服务器](./tutorial-discover-physical.md)的 Azure Migrate Server 评估工具发现本地计算机进行评估