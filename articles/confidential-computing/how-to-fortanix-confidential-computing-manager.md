---
title: Azure 托管应用程序中的 Fortanix Confidential Computing Manager
description: 了解如何在 Azure 门户的托管应用程序中部署 Fortanix Confidential Computing Manager (CCM)。
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/03/2021
ms.author: jencook
ms.openlocfilehash: 757ce9b7502316bbc8a5b8f27ba672048b7bbace
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563415"
---
# <a name="fortanix-confidential-computing-manager-in-an-azure-managed-application"></a>Azure 托管应用程序中的 Fortanix Confidential Computing Manager

本文介绍如何在 Azure 门户中部署由 Fortanix Confidential Computing Manager 管理的应用程序。

Fortanix 是第三方软件供应商，它提供了基于 Azure 基础结构构建的产品和服务。 还有其他的第三方提供商也在 Azure 提供了类似的机密计算服务。

> [!NOTE]
>本文档中提及的产品不受 Microsoft 的控制。 Microsoft 向你提供此信息仅便于你参考，对这些非 Microsoft 产品的提及并不表示 Microsoft 予以认可。

## <a name="prerequisites"></a>先决条件

- 一个专用的 Docker 注册表，用于推送转换后的应用程序映像。
- 如果还没有 Azure 订阅，可以在开始前[创建一个帐户](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)。

## <a name="deploy-a-confidential-computing-manager-through-an-azure-managed-application"></a>通过 Azure 托管应用程序部署 Confidential Computing Manager

1. 转到 [Azure 门户](https://portal.azure.com/)。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/azure-portal.png" alt-text="Azure 门户。":::

2. 在搜索栏中搜索“Fortanix Confidential Computing Manager”，你会找到针对 Fortanix CCM 的市场列表。 选择“Azure 上的 Fortanix Confidential Computing Manager”。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/search-marketplace-listing.png" alt-text="市场列表。":::

3. 此时会打开在其中创建 CCM 托管应用程序的页面。 选择“创建”。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/create-managed-application.png" alt-text="创建应用程序。":::

4. 填写所有必填字段。
   1. 在“托管应用程序详细信息”部分，“托管资源组”字段会有一个默认值，用户可以根据需要修改该默认值。
   2. 在“区域”字段中，选择“澳大利亚东部”、“澳大利亚东南部”、“美国东部”、“美国西部 2”、“西欧”、“北欧”、“加拿大中部”、“加拿大东部”或“美国东部 2 EUAP” 。

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/required-fields.png" alt-text="必填字段":::

   选择“查看 + 创建”以创建 Fortanix CCM 托管应用程序。

5. 查看详细信息并在验证通过后选择“我同意上述条款和条件”复选框，然后选择“创建”来创建托管应用程序。

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/review-details.png" alt-text="查看详细信息。":::

6. Fortanix CCM 部署会启动，并通知你部署正在进行。

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-progress.png" alt-text="部署进度。":::

7. 部署完成后，选择“转到资源”按钮，转到已部署的 CCM 托管应用程序的“概述”页，以注册计算节点。

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-resource.png" alt-text="屏幕截图，显示 Azure 门户中的成功部署。":::

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-overview.png" alt-text="屏幕截图，显示 Azure 门户中机密计算资源的概述。":::

## <a name="enroll-the-compute-node-in-fortanix-ccm"></a>在 Fortanix CCM 中注册计算节点

1. 从左侧导航菜单中选择“Confidential Computing Manager”。 登录到 Fortanix CCM 并创建一个帐户，如 **图 9** 所示。

    若要更详细地了解如何在 CCM 中注册、登录和创建帐户，请参阅 [CCM 入门](https://support.fortanix.com/hc/en-us/articles/360034373551-User-s-Guide-Logging-in)。
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-login.png" alt-text="屏幕截图，显示 Fortanix Confidential Computing Manager 登录。":::
    
2. 若要从 CCM 管理控制台获取联接令牌，请首先选择“注册节点”按钮。 然后，在“注册节点”窗口中选择“复制”按钮，以便复制联接令牌。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/get-join-token.png" alt-text="屏幕截图，显示如何获取联接令牌。":::

3. 现在，若要注册节点代理，请选择“Confidential Computing Node Agent”选项卡，然后选择“添加”以添加 CCM 节点代理。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/add-node-agent.png" alt-text="屏幕截图，显示如何添加节点代理。":::

4.  在 CCM 节点代理窗体中，填写所有必填字段。 在“联接令牌”中粘贴你在步骤 2 中复制的联接令牌。 选择“查看 + 提交”进行确认。

    若要详细了解如何注册 CCM 计算节点，请参阅[注册计算节点](https://support.fortanix.com/hc/en-us/articles/360043085652-User-s-Guide-Compute-Nodes)。
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/enroll-compute-node.png" alt-text="屏幕截图，显示如何注册计算节点。":::
    
5. 验证通过后，选择“提交”以完成节点代理的创建。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-agent-created.png" alt-text="屏幕截图，显示节点代理已创建。":::

6. 若要检查部署状态，请转到“概述”选项卡，选择“托管资源组”链接。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-enrolled.png" alt-text="屏幕截图，显示节点已注册。":::
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/managed-resource-group.png" alt-text="屏幕截图，显示如何检查部署状态。":::

7. 现在，你会注意到部署状态仍然是“正在进行”，需要几分钟时间才能成功注册节点代理。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-in-progress.png" alt-text="屏幕截图，显示部署正在进行。":::

8. 成功注册节点代理后，状态将更改为“成功”。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-succeeded.png" alt-text="屏幕截图，显示部署已成功。":::

9. 现在，在 CCM 托管应用程序中转到“计算节点”页面，你会发现节点处于“活动”状态且已成功注册。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-active-state.png" alt-text="屏幕截图，显示节点已成功注册。":::

## <a name="clean-up-resources"></a>清理资源

用户还可以从“Confidential Computing Node Agent”页中删除 CCM 节点代理。 若要删除节点代理，请选择节点代理，然后在顶部栏中选择“删除”按钮。

:::image type="content" source="media/how-to-fortanix-confidential-computing-manager/delete-node-agent.png" alt-text="屏幕截图，显示如何删除节点代理。":::

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何将使用 Azure 托管应用的节点注册到 Fortanix 的 Confidential Computing Manager。 使用节点注册，你可以将应用程序映像转换为在机密计算虚拟机之上运行。 有关 Azure 上的机密计算虚拟机的详细信息，请参阅[有关虚拟机的解决方案](virtual-machine-solutions.md)。

若要详细了解 Azure 的机密计算产品/服务，请参阅 [Azure机密计算](overview.md)。

了解如何在 Azure 上使用其他第三方产品/服务（例如 [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) 和 [Scone](https://sconedocs.github.io)）完成类似的任务。

