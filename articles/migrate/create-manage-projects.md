---
title: 创建和管理 Azure Migrate 项目
description: 在 Azure Migrate 中查找、创建、管理和删除项目。
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: f66aef64d68bd5d99d7a27d1382306d9202bd350
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388050"
---
# <a name="create-and-manage-azure-migrate-projects"></a>创建和管理 Azure Migrate 项目

本文介绍如何创建、管理和删除 [Azure Migrate](migrate-services-overview.md) 项目。 

经典 Azure Migrate 在2024年2月停用。 2024年2月之后，将不再支持经典版本的 Azure Migrate，并且将删除经典项目中的清单元数据。 如果使用的是经典 Azure Migrate 项目，请删除这些项目，然后按照步骤创建新的 Azure Migrate 项目。 不能将经典 Azure Migrate 项目或组件升级到 Azure Migrate。 在开始创建过程之前，请查看 [常见问题解答](https://docs.microsoft.com/azure/migrate/resources-faq#i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version) 。

Azure Migrate 项目用于存储从正在评估或迁移的环境中收集的发现、评估和迁移元数据。 在项目中，可以跟踪发现的资产，创建评估，并安排迁移到 Azure。  

## <a name="verify-permissions"></a>验证权限

请检查你是否具有创建 Azure Migrate 项目的正确权限：

1. 在 Azure 门户中，打开相关订阅，并选择 " **访问控制" (IAM)**"。
2. 在 " **检查访问权限**" 中，找到相关帐户，然后选择 "查看权限"。 你应该拥有“参与者”或“所有者”权限。  


## <a name="create-a-project-for-the-first-time"></a>首次创建项目

在 Azure 订阅中设置新的 Azure Migrate 项目。

1. 在 Azure 门户中，搜索 *Azure Migrate*。
2. 在 " **服务**" 中，选择 **Azure Migrate**。
3. 在“概述”中，单击“评估和迁移服务器” 。

    ![概述用于评估和迁移服务器的选项](./media/create-manage-projects/assess-migrate-servers.png)

4. 在 " **服务器**" 中，选择 " **创建项目**"。

    ![用于开始创建项目的按钮](./media/create-manage-projects/create-project.png)

5. 在 " **创建项目**" 中，选择 Azure 订阅和资源组。 如果没有资源组，请创建一个资源组。
6. 在“项目详细信息”中，指定项目名称以及要在其中创建项目的地理位置。
    - 地理位置仅用于存储从本地计算机收集的元数据。 你可以选择任何要迁移的目标区域。 
    - 查看[公有云](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府云](migrate-support-matrix.md#supported-geographies-azure-government)支持的地理位置。

8. 选择“创建”。

   ![页面到输入项目设置](./media/create-manage-projects/project-details.png)


等待几分钟，让 Azure Migrate 项目部署完成。

## <a name="create-a-project-in-a-specific-region"></a>在特定区域中创建项目

在门户中，可以选择要在其中创建项目的地理位置。 如果要在特定的 Azure 区域中创建项目，请使用以下 API 命令创建项目。

```rest
PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"
```

## <a name="create-additional-projects"></a>创建其他项目

如果你已有一个 Azure Migrate 项目，并且想要创建其他项目，请执行以下操作：  

1. 在 [azure 公共门户](https://portal.azure.com) 或 [azure 政府](https://portal.azure.us)版中，搜索 " **Azure Migrate**"。
2. 在 Azure Migrate 仪表板 > **服务器** 上，选择右上角的 " **更改** "。

   ![更改 Azure Migrate 项目](./media/create-manage-projects/switch-project.png)

3. 若要创建新项目，请选择 **"单击此处"**。


## <a name="find-a-project"></a>查找项目

查找项目，如下所示：

1. 在 [Azure 门户](https://portal.azure.com)中，搜索 *Azure Migrate*。
2. 在 Azure Migrate 面板 > **服务器**"中，选择右上角的" **更改** "。

    ![切换到现有 Azure Migrate 项目](./media/create-manage-projects/switch-project.png)

3. 选择相应的订阅并 Azure Migrate 项目。


### <a name="find-a-classic-azure-migrate-project"></a>查找经典 Azure Migrate 项目

如果在 Azure Migrate 的 [以前版本](migrate-services-overview.md#azure-migrate-versions) 中创建项目，请按如下所示进行查找：

1. 在 [Azure 门户](https://portal.azure.com)中，搜索 *Azure Migrate*。
2. 在 Azure Migrate "仪表板" 中，如果已在以前的版本中创建了一个项目，则将显示引用较旧项目的标题。 选择标题。

    ![访问现有项目](./media/create-manage-projects/access-existing-projects.png)

3. 查看旧项目的列表。


## <a name="delete-a-project"></a>删除项目

按如下所示删除：

1. 打开在其中创建项目的 Azure 资源组。
2. 在 "资源组" 页上，选择 " **显示隐藏的类型**"。
3. 选择要删除的迁移项目及其关联的资源。
    - 资源类型为 " **migrateprojects**"。
    - 如果 Azure Migrate 项目专门使用资源组，则可以删除整个资源组。

请注意：

- 删除时，将删除项目和有关发现的计算机的元数据。
- 如果使用的是较旧版本的 Azure Migrate，请打开在其中创建项目的 Azure 资源组。 选择要删除的迁移项目 (资源类型为 " **迁移项目**) "。
- 如果使用 Azure Log Analytics 工作区进行依赖关系分析：
    - 如果已将 Log Analytics 工作区附加到服务器评估工具，则不会自动删除此工作区。 同一 Log Analytics 工作区可用于多个方案。
    - 如果要删除 Log Analytics 工作区，请手动执行该操作。
- 项目删除操作不可逆。 无法恢复已删除的对象。

### <a name="delete-a-workspace-manually"></a>手动删除工作区

1. 浏览到附加到该项目的 Log Analytics 工作区。

    - 如果尚未删除 Azure Migrate 项目，可以在 **Essentials**  >  **Server 评估** 中找到指向工作区的链接。
       ![LA 工作区 ](./media/create-manage-projects/loganalytics-workspace.png) 。
       
    - 如果已删除 Azure Migrate 项目，请在 Azure 门户的左窗格中选择 " **资源组** "，然后找到工作区。
       
2. [按照说明](../azure-monitor/platform/delete-workspace.md) 删除工作区。

## <a name="next-steps"></a>后续步骤

将 [评估](how-to-assess.md) 或 [迁移](how-to-migrate.md) 工具添加到 Azure Migrate 项目。
