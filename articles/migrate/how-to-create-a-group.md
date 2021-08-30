---
title: 分组服务器以便使用 Azure Migrate 进行评估 | Microsoft Docs
description: 介绍如何在使用 Azure Migrate 服务运行评估之前分组服务器。
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/17/2019
ms.openlocfilehash: 9ffa031a9058eb6864298c5c9387402955a84968
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747251"
---
# <a name="create-a-group-for-assessment"></a>创建组以进行评估

本文介绍如何创建服务器组，以使用“Azure Migrate: 发现和评估”进行评估。

[Azure Migrate](migrate-services-overview.md) 可帮助你迁移到 Azure。 Azure Migrate 提供了一个中心，用于跟踪 Azure 的本地基础结构、应用程序和数据的发现、评估以及迁移。 该中心提供用于评估和迁移的 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 产品。

## <a name="grouping-servers"></a>分组服务器

将服务器分组，以评估它们是否适合迁移到 Azure，并为其获取 Azure 大小和成本估算。 创建组的方法有以下两种：

- 如果知道需要一起迁移的服务器，则可以在 Azure Migrate 中手动创建组。
- 如果不确定需要将哪些服务器分组在一起，则可以在 Azure Migrate 中使用依赖项可视化功能创建组。 

> [!NOTE]
> 依赖项可视化功能在 Azure 政府中不可用。

## <a name="create-a-group-manually"></a>手动创建组

你可以在[创建评估](how-to-create-assessment.md)的同时创建一个组。

如果要在创建评估之外手动创建组，请执行以下操作：

1. 在 Azure Migrate 项目 >“概述”中，单击“评估并迁移服务器” 。 在“Azure Migrate: 发现和评估”中，单击“组” 
    - 如果尚未添加“Azure Migrate: 发现和评估”工具，则单击进行添加。 [了解详细信息](how-to-assess.md)。
    - 如果尚未创建 Azure Migrate 项目，请[了解详细信息](./create-manage-projects.md)。

    ![选择组](./media/how-to-create-a-group/select-groups.png)

2. 单击“组”图标。
3. 在“创建组”中，指定组的名称，然后在“设备名称”中，选择要用于发现服务器的 Azure Migrate 设备 。
4. 在服务器列表中，选择要添加到组的服务器，然后选择“创建”。

    ![创建组](./media/how-to-create-a-group/create-group.png)

现在可以在[创建 Azure VM 评估](how-to-create-assessment.md)、[Azure VMware 解决方案 (AVS) 评估](how-to-create-azure-vmware-solution-assessment.md)[Azure SQL 评估](how-to-create-azure-sql-assessment.md)或 [Azure 应用服务评估](how-to-create-azure-app-service-assessment.md)时使用此组。

## <a name="refine-a-group-with-dependency-mapping"></a>通过依赖项映射优化组

依赖项映射有助于直观呈现服务器之间的依赖关系。 在评估可信度较高的服务器组时，通常会使用依赖项映射。

- 它有助于在运行评估之前交叉检查服务器依赖项。
- 它还有助于有效地计划向 Azure 的迁移，确保不会遗漏任何内容，从而避免迁移过程中出现意外中断。
- 可以发现需要一起迁移的互相依赖的系统，并识别运行中的系统是仍然为用户提供服务还是在等待解除授权而非迁移。

如果已[设置依赖项映射](how-to-create-group-machine-dependencies.md)，并且想要优化现有组，请执行以下操作：

1. 在“服务器”选项卡的”Azure Migrate: 发现和评估”磁贴中，单击“组”  。
2. 单击要优化的组。
    - 如果尚未设置依赖项映射，“依赖项”列将显示“需要安装”状态 。 对于要为其可视化依赖项的每个服务器，单击“需要安装”。 在每个服务器上安装几个代理，然后才可以映射服务器依赖项。 [了解详细信息](how-to-create-group-machine-dependencies.md)。

        ![添加依赖项映射](./media/how-to-create-a-group/add-dependency-mapping.png)

    - 如果已设置依赖项映射，则在组页上单击“查看依赖项”，打开组依赖项映射。

3. 单击“查看依赖项”后，组依赖项映射会显示以下内容：

    - 与已安装依赖项代理的组中的所有服务器之间的入站（客户端）和出站（服务器）TCP 连接。
    - 未安装依赖项代理的依赖服务器按端口号进行分组。
    - 安装了依赖项代理的依赖服务器显示为单独的框。
    - 服务器内运行的进程。 展开每个服务器框可查看进程。
    - 服务器属性（包括 FQDN、操作系统、MAC 地址）。 单击每个服务器框以查看详细信息。

4. 要查看所选时间间隔内的依赖项，请通过指定开始和结束日期或持续时间，修改时间范围（默认值为 1 个小时）。

    > [!NOTE]
    > 时间范围可长达一小时。 如果需要更长的范围，请使用 [Azure Monitor 查询更长时间内的依赖数据](how-to-create-group-machine-dependencies.md)。

5. 确定要添加到组或从组中删除的依赖项后，可以修改组。 按住 Ctrl 键单击，在组中添加或删除服务器。

    - 你只能添加已发现的服务器。
    - 添加和删除服务器会使过去对组的评估失效。
    - 修改组时，你可以视情况创建新的评估。

## <a name="next-steps"></a>后续步骤

了解如何设置[依赖项映射](how-to-create-group-machine-dependencies.md)，并将其用于创建高可信度的组。