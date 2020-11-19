---
title: 创建以应用程序为中心的环境-Azure
description: 本文演示如何使用 Cloud Shell 蚁群和 Microsoft Azure 创建以应用程序为中心的环境。
ms.topic: how-to
ms.date: 11/26/2020
ms.openlocfilehash: 88244f268d5ed038e9bb7082d3d5cc1179e5ec4e
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2020
ms.locfileid: "94918096"
---
# <a name="create-an-application-centric-environment"></a>创建以应用程序为中心的环境

[Quali 的 CloudShell 蚁群](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview) 是一个 SaaS 平台，用于为云技术（包括 Azure 和 Kubernetes）中的复杂、以应用程序为中心的环境提供基础结构自动化。 CloudShell 蚁群对 Azure 开发测试实验室进行了补充，可帮助开发人员团队将复杂的应用程序部署到整个价值流中，这一切都是生产的。

本文演示如何使用 CloudShell 蚁群和 Microsoft Azure 创建以应用程序为中心的环境。

## <a name="set-up-the-environment-with-cloudshell-colony-and-microsoft-azure"></a>设置 CloudShell 蚁群和 Microsoft Azure 的环境

1. 注册免费试用 [蚁群](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview)。

    :::image type="content" source="./media/create-application-centric-environment/free-trial.png" alt-text="注册免费试用版":::    
1. 将 Azure 帐户链接 ([在此处查看](https://colonysupport.quali.com/hc/articles/360008222234)) 的步骤。

    :::image type="content" source="./media/create-application-centric-environment/welcome.png" alt-text="欢迎使用蚁群":::     
1. 邀请用户加入你的空间。
1. 使用 YAML 文件创建第一个蓝图 ([在此处) 查看步骤](https://colonysupport.quali.com/hc/articles/360001680807-Steps-to-Developing-a-Blueprint) 。
    1. 将 GitHub 或 BitBucket 中的蓝图存储库链接到蚁群。
    1. 使用蚁群示例蓝图作为基础，并根据需要进行修改。

        :::image type="content" source="./media/create-application-centric-environment/performance-stress-tests.png" alt-text="压力测试":::    
    1. 发布蓝图供他人使用。
1. 使用蚁群将应用程序环境启动到沙盒。

    :::image type="content" source="./media/create-application-centric-environment/blueprints.png" alt-text="使用蚁群将应用程序环境启动到沙盒":::    

> [!NOTE]
> 你还可以在 Azure DevOps 中将蓝图作为 CI/CD 工作流的一部分进行集成 (在 [此处) 查看步骤](https://colonysupport.quali.com/hc/articles/360008464234) 。

:::image type="content" source="./media/create-application-centric-environment/devops-pipeline.png" alt-text="连接到 Azure DevOps 管道":::    

## <a name="next-steps"></a>后续步骤

[请求蚁群演示](https://info.quali.com/cloudshell-colony-demo-request)
