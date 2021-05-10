---
title: 创建和管理项目
description: 在 Azure Migrate 中查找、创建、管理和删除项目。
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 0c9102f8ca724e431bb478945c5f4ba0369643d6
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714824"
---
# <a name="create-and-manage-projects"></a>创建和管理项目

本文介绍如何创建、管理和删除[项目](migrate-services-overview.md)。 

Azure Migrate 经典版将于 2024 年 2 月停用。 2024 年 2 月之后，Azure Migrate 经典版将不再受支持，并且经典版项目中的库存元数据也将删除。 如果使用的是经典版项目，请删除这些项目，并按照相关步骤来创建新项目。 不能将经典版项目或组件升级到 Azure Migrate。 在开始创建过程之前，请查看 [FAQ](./resources-faq.md#i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version)。

项目用于存储从正在评估或迁移的环境中收集的发现、评估和迁移元数据。 在项目中，你可以跟踪发现的资产、创建评估并协调向 Azure 的迁移。  

## <a name="verify-permissions"></a>验证权限

检查是否具有创建项目的正确权限：

1. 在 Azure 门户中打开相关订阅，然后选择“访问控制(IAM)”。 ****
2. 在“检查访问权限”中，找到相关帐户并选择它以查看权限。 你应该拥有“参与者”或“所有者”权限。  


## <a name="create-a-project-for-the-first-time"></a>首次创建项目

在 Azure 订阅中设置新项目。

1. 在 Azure 门户中搜索“Azure Migrate”。
2. 在“服务”中选择“Azure Migrate”。 
3. 在“概述”中，单击“评估和迁移服务器” 。

    :::image type="content" source="./media/create-manage-projects/assess-migrate-servers.png" alt-text="“概述”中用于评估和迁移服务器的选项":::

4. 在“服务器”中选择“创建项目”。 

    :::image type="content" source="./media/create-manage-projects/create-project.png" alt-text="开始创建项目按钮":::

5. 在“创建项目”中，选择 Azure 订阅和资源组。 如果没有资源组，请创建一个资源组。
6. 在“项目详细信息”中，指定项目名称以及要在其中创建项目的地理位置。
    - 地理位置仅用于存储从本地服务器中收集的元数据。 可为迁移选择任一目标区域。 
    - 查看[公有云](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府云](migrate-support-matrix.md#supported-geographies-azure-government)支持的地理位置。 


    > [!Note]
    > 使用“高级”配置部分，创建具有专用终结点连接的 Azure Migrate 项目。 [了解详细信息](how-to-use-azure-migrate-with-private-endpoints.md#create-a-project-with-private-endpoint-connectivity) 

7. 选择“创建”。

     :::image type="content" source="./media/create-manage-projects/project-details.png" alt-text="输入项目设置的页面":::


等待几分钟，让项目完成部署。

## <a name="create-a-project-in-a-specific-region"></a>在特定区域创建项目

在门户中，可以选择要在其中创建项目的地理位置。 如果要在特定的 Azure 区域内创建项目，请使用以下 API 命令来创建项目。

```rest
PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"
```

## <a name="create-additional-projects"></a>创建其他项目

如果已有一个项目，想创建其他项目，请执行以下操作：  

1. 在 [Azure 公共门户](https://portal.azure.com)或 [Azure 政府](https://portal.azure.us)中，搜索“Azure Migrate”。
2. 在“Azure Migrate”仪表板 >“服务器”中，选择右上角的“更改”。

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="更改项目":::

3. 若要创建新项目，请选择“单击此处”。


## <a name="find-a-project"></a>查找项目

按如下所示查找项目：

1. 在 [Azure 门户](https://portal.azure.com)中搜索“Azure Migrate”。
2. 在“Azure Migrate”仪表板 >“服务器”中，选择右上角的“更改”。

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="切换到现有项目":::

3. 选择相应的订阅和项目。


### <a name="find-a-classic-project"></a>查找经典版项目

如果在 Azure Migrate 的[以前版本](migrate-services-overview.md#azure-migrate-versions)中创建了项目，请按如下所示进行查找：

1. 在 [Azure 门户](https://portal.azure.com)中搜索“Azure Migrate”。
2. 在“Azure Migrate”仪表板中，如果在以前版本中创建了项目，则会出现引用较旧项目的横幅。 选择该横幅。

    :::image type="content" source="./media/create-manage-projects/access-existing-projects.png" alt-text="访问现有项目":::

3. 查看旧项目的列表。


## <a name="delete-a-project"></a>删除项目

按如下所示删除：

1. 打开在其中创建项目的 Azure 资源组。
2. 在“资源组”页中，选择“显示隐藏的类型”。
3. 选择要删除的项目及其关联的资源。
    - 资源类型为“Microsoft.Migrate/migrateprojects”。
    - 当资源组被项目独占使用时，可以删除整个资源组。

请注意：

- 进行删除时，项目和已发现的服务器的元数据都会被删除。
- 如果使用的是较早版本的 Azure Migrate，请打开在其中创建项目的 Azure 资源组。 选择要删除的项目（资源类型为“迁移项目”）。
- 如果将依赖项分析与 Azure Log Analytics 工作区配合使用：
    - 如果已将 Log Analytics 工作区附加到服务器评估工具，则不会自动删除此工作区。 同一 Log Analytics 工作区可用于多个方案。
    - 如果要删除 Log Analytics 工作区，请手动进行删除。
- 项目删除操作不可逆。 已删除的对象无法恢复。

### <a name="delete-a-workspace-manually"></a>手动删除工作区

1. 浏览到附加到该项目的 Log Analytics 工作区。

    - 如果尚未删除此项目，可以在“概要” > “服务器评估”中找到指向此工作区的链接。 
    :::image type="content" source="./media/create-manage-projects/loganalytics-workspace.png" alt-text="LA 工作区":::
       
    - 如果已删除项目，可在 Azure 门户的左侧窗格中选择“资源组”并找到工作区。
       
2. [按照说明](../azure-monitor/logs/delete-workspace.md)删除此工作区。

## <a name="next-steps"></a>后续步骤

将[评估](how-to-assess.md)或[迁移](how-to-migrate.md)工具添加到项目。