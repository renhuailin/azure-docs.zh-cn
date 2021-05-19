---
title: 在 Azure Migrate 中添加评估工具
description: 了解如何在 Azure Migrate 中添加评估工具。
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 37f3748b4f0f3db47bbd6fbe9bc06a307781c2f8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786798"
---
# <a name="add-assessment-tools"></a>添加评估工具

本文介绍如何在 [Azure Migrate](./migrate-services-overview.md) 中添加评估工具。 

- 如果要添加评估工具，但还没有 Azure Migrate 项目，请按照本[文](create-manage-projects.md)操作。
- 如果已添加 ISV 工具或 Movere 用于评估，请[按照步骤](prepare-isv-movere.md)进行操作以准备使用该工具。

## <a name="select-an-assessment-scenario"></a>选择评估方案

1. 在 Azure Migrate 项目中，单击“概述”。
2. 选择评估方案：

    - 若要发现、评估服务器（物理或虚拟）并将其从数据中心或其他云迁移到 Azure，请选择“发现、评估和迁移”。 现在还可以使用此迁移目标来发现和评估 VMware 环境中的 SQL Server。
    - 若要评估本地 SQL Server 数据库，请选择“评估和迁移数据库”。
    - 若要评估或迁移本地 Web 应用，请选择“了解更多” > “Web 应用”。 
    - 若要评估虚拟桌面基础结构，请选择“了解更多” > “虚拟桌面基础结构”。

    ![用于选择评估方案的选项](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-discovery-and-assessment-tool"></a>选择发现和评估工具 


1. 添加工具：

    - 如果使用门户中的“评估和迁移服务器”选项创建了 Azure Migrate 项目，则 Azure Migrate 发现和评估工具会自动添加到该项目中。 若要添加其他评估工具，在“Windows、Linux 和 SQL Server”中的“评估工具”旁边，选择“添加更多工具”。

         ![用于添加其他评估工具的按钮](./media/how-to-assess/add-assessment-tool.png)

    - 如果使用其他选项创建了项目，但没有任何评估工具，请在“Windows、Linux 和 SQL Server” > “评估工具”中选择“单击此处”。

        ![用于添加第一个评估工具的按钮](./media/how-to-assess/no-assessment-tool.png)

2. 在“Azure Migrate” > “添加工具”中，选择要添加的工具。  然后选择“添加工具”。

    ![从列表中选择评估工具](./media/how-to-assess/select-assessment-tool.png)



## <a name="select-a-database-assessment-tool"></a>选择数据库评估工具

1. 添加工具：

    - 如果使用门户中的“评估和迁移数据库”选项创建了 Azure Migrate 项目，则“数据库评估”工具会自动添加到该项目中。 若要添加其他评估工具，请在“数据库”中的“评估工具”旁边，选择“添加更多工具”。

    - 如果使用其他选项创建了项目，但没有任何数据库评估工具，请在“数据库” > “评估工具”中选择“单击此处”。

2. 在“Azure Migrate” **“添加工具”中，选择要添加的工具。**  >  然后选择“添加工具”。

    ![从列表中选择数据库评估工具](./media/how-to-assess/select-database-assessment-tool.png)


## <a name="select-a-web-app-assessment-tool"></a>选择 Web 应用评估工具

如果使用门户中的“了解更多” > “WebApps”选项创建了 Azure Migrate 项目，则“Web 应用评估”工具会自动添加到该项目中。  


1. 如果 Web 应用评估工具不在项目中，请在“Web 应用” > “评估工具”中选择“单击此处”。  
    
    ![添加 Web 应用评估工具](./media/how-to-assess/no-web-app-assessment-tool.png)


2. 在“Azure Migrate” > “添加工具”中，选择“Web 应用评估”工具。  然后选择“添加工具”。

    ![从列表中选择数据库迁移工具](./media/how-to-assess/select-web-app-assessment-tool.png)

 


## <a name="next-steps"></a>后续步骤

使用适用于 [VMware](./tutorial-discover-vmware.md)、[Hyper-V](./tutorial-discover-hyper-v.md) 或[物理服务器](./tutorial-discover-physical.md)的 Azure Migrate 发现和评估工具发现本地服务器以进行评估