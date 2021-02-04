---
title: Azure 托管应用程序中的 Fortanix 机密计算管理器
description: 了解如何在 Azure 门户 (CCM) 在托管应用程序中部署 Fortanix 机密计算管理器。
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/03/2021
ms.author: jencook
ms.openlocfilehash: 6132b864e94c5c2af691183e198ca882c361d5ec
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551226"
---
# <a name="fortanix-confidential-computing-manager-in-an-azure-managed-application"></a>Azure 托管应用程序中的 Fortanix 机密计算管理器

本文介绍如何在 Azure 门户中部署由 Fortanix 的机密计算管理器管理的应用程序。

Fortanix 是第三方软件供应商，其中的产品和服务构建在 Azure 基础结构之上。 其他第三方提供程序在 Azure 上提供类似的机密计算服务。

> [!NOTE]
>本文档中提及的产品不受 Microsoft 控制。 Microsoft 仅为你提供此信息，并且对这些非 Microsoft 产品的引用不表示 Microsoft 认可。

## <a name="prerequisites"></a>先决条件

- 用于推送转换后的应用程序映像的专用 Docker 注册表。
- 如果还没有 Azure 订阅，可以在开始前[创建一个帐户](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)。

## <a name="deploy-a-confidential-computing-manager-through-an-azure-managed-application"></a>通过 Azure 托管应用程序部署机密计算管理器

1. 转到 [Azure 门户](https://portal.azure.com/)。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/azure-portal.png" alt-text="Azure 门户。":::

2. 在搜索栏中，搜索 "Fortanix 机密计算管理器"，你将看到 Fortanix CCM 的 Marketplace 列表。 选择 **"Fortanix"**。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/search-marketplace-listing.png" alt-text="Marketplace 列表。":::

3. 此时将打开在其中创建 CCM 管理的应用程序的页面。 选择 " **创建**"。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/create-managed-application.png" alt-text="创建应用程序。":::

4. 填写所有必填字段。
   1. 在 "托管应用程序详细信息" 部分中，" **托管资源组** " 字段将具有一个默认值，用户可以根据需要修改该默认值。
   2. 在 " **区域** " 字段中， **选择澳大利亚东部**、 **澳大利亚东南部**、 **美国东部**、 **美国西部 2**、 **西欧**、 **北欧**、 **加拿大中部**、 **加拿大东部** 或 **美国东部 2 EUAP**。

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/required-fields.png" alt-text="必填字段":::

   选择 " **查看 + 创建** " 以创建 Fortanix CCM 托管的应用程序。

5. 查看详细信息并在验证通过后，选择 " **我同意上述条款和条件** " 复选框，然后选择 " **创建** " 来创建托管应用程序。

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/review-details.png" alt-text="查看详细信息。":::

6. Fortanix CCM 部署将启动并通知部署正在进行。

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-progress.png" alt-text="部署进度。":::

7. 部署完成后，选择 " **前往资源** " 按钮，以跳到已部署的 CCM 托管应用程序的 "概述" 页，以注册计算节点。

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-resource.png" alt-text="显示 Azure 门户中成功部署的屏幕截图。]":::

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-overview.png" alt-text="显示 Azure 门户中机密计算资源的概述的屏幕截图。":::

## <a name="enroll-the-compute-node-in-fortanix-ccm"></a>在 Fortanix CCM 中注册计算节点

1. 从左侧导航菜单中选择 " **机密计算管理器** "。 登录到 Fortanix CCM 并创建一个帐户，如 **图 9** 所示。

    有关如何注册的详细信息，请参阅 ccm [入门](https://support.fortanix.com/hc/en-us/articles/360034373551-User-s-Guide-Logging-in)中的登录和创建帐户。
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-login.png" alt-text="显示 Fortanix 机密计算管理器登录名的屏幕截图。":::
    
2. 若要从 CCM 管理控制台获取联接标记，请首先选择 " **注册" 节点** 按钮。 然后，在 "注册节点" 窗口中，选择 " **复制** " 按钮以复制联接标记。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/get-join-token.png" alt-text="显示获取联接令牌的屏幕截图。":::

3. 现在若要注册节点代理，请选择 " **机密计算节点代理** " 选项卡，然后选择 " **添加** " 以添加 CCM 节点代理。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/add-node-agent.png" alt-text="显示添加节点代理的屏幕截图。":::

4.  在 CCM 节点代理窗体中，填写所有必填字段。 粘贴在 " **联接标记**" 的步骤2中复制的联接标记。 选择 "查看" 和 " **提交** " 以确认。

    有关如何注册 CCM 计算节点的详细信息，请参阅 [注册计算节点](https://support.fortanix.com/hc/en-us/articles/360043085652-User-s-Guide-Compute-Nodes)。
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/enroll-compute-node.png" alt-text="显示注册计算节点的屏幕截图。":::
    
5. 验证通过后，选择 " **提交** " 以完成节点代理创建。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-agent-created.png" alt-text="显示节点代理的屏幕截图。":::

6. 若要检查部署状态，请参阅 " **概述** " 选项卡，然后选择 " **托管资源组** " 链接。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-enrolled.png" alt-text="显示节点已注册的屏幕截图。":::
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/managed-resource-group.png" alt-text="显示检查部署状态的屏幕截图。":::

7. 现在，你会注意到部署状态仍在进行，并需要几分钟时间才能成功注册节点代理。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-in-progress.png" alt-text="显示正在进行的部署的屏幕截图。":::

8. 成功注册节点代理后，状态将更改为 "成功"。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-succeeded.png" alt-text="显示部署成功的屏幕截图。":::

9. 现在，在 CCM 托管的应用程序中，请参阅计算节点页面，你会发现节点处于 **活动** 状态且已成功注册。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-active-state.png" alt-text="显示节点已成功注册的屏幕截图。":::

## <a name="clean-up-resources"></a>清理资源

用户还可以从 "机密计算节点代理" 页中删除 CCM 节点代理。 若要删除节点代理，请选择节点代理，然后在顶部栏中选择 " **删除** " 按钮。

:::image type="content" source="media/how-to-fortanix-confidential-computing-manager/delete-node-agent.png" alt-text="显示删除节点代理的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 Azure 托管应用将节点注册到 Fortanix 的机密计算管理器。 使用节点注册，你可以将应用程序映像转换为在机密计算虚拟机之上运行。 有关 Azure 上的机密计算虚拟机的详细信息，请参阅[有关虚拟机的解决方案](virtual-machine-solutions.md)。

若要了解有关 Azure 机密计算产品/服务的详细信息，请参阅 [azure 机密计算](overview.md)。

了解如何使用 Azure 上的其他第三方产品（例如 [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) 和 [Scone](https://sconedocs.github.io)）完成类似的任务。

