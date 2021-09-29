---
title: 创建以应用程序为中心的环境
description: 本文演示如何使用 Cloud Shell 蚁群和 Microsoft Azure 创建以应用程序为中心的环境。
ms.topic: how-to
ms.date: 11/26/2020
ms.openlocfilehash: 8b56a39996aa93e10ede28de91ae20857a8eefc0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627570"
---
# <a name="create-an-application-centric-environment"></a>创建以应用程序为中心的环境

[Quali 的 CloudShell 蚁群](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview)是一个 SaaS 平台，用于为采用云技术（包括 Azure 和 Kubernetes）的以应用程序为中心的复杂环境大规模提供基础结构自动化。 CloudShell 蚁群是对 Azure 开发测试实验室的补充，可帮助开发人员团队将复杂的应用程序部署到整个价值流，再一路部署到生产中。

本文演示如何使用 CloudShell 蚁群和 Microsoft Azure 创建以应用程序为中心的环境。

## <a name="set-up-the-environment-with-cloudshell-colony-and-microsoft-azure"></a>使用 CloudShell 蚁群和 Microsoft Azure 设置环境

1. 注册[蚁群](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview)的免费试用版。

    :::image type="content" source="./media/create-application-centric-environment/free-trial.png" alt-text="注册免费试用版":::    
1. 链接你的 Azure 帐户（[在此处查看步骤](https://colonysupport.quali.com/hc/articles/360008222234)）。

    :::image type="content" source="./media/create-application-centric-environment/welcome.png" alt-text="欢迎使用蚁群":::     
1. 邀请用户加入你的空间。
1. 使用 YAML 文件创建第一个蓝图（[在此处查看步骤](https://colonysupport.quali.com/hc/articles/360001680807-Steps-to-Developing-a-Blueprint)）。
    1. 将 GitHub 或 BitBucket 中的蓝图存储库链接到蚁群。
    1. 使用蚁群示例蓝图作为基础，并根据需要进行修改。

        :::image type="content" source="./media/create-application-centric-environment/performance-stress-tests.png" alt-text="压力测试":::    
    1. 发布蓝图供他人使用。
1. 使用蚁群将应用程序环境启动到沙盒。

    :::image type="content" source="./media/create-application-centric-environment/blueprints.png" alt-text="使用蚁群将应用程序环境启动到沙盒":::    

> [!NOTE]
> 也可以将蓝图作为 CI/CD 工作流的一部分集成在 Azure DevOps 中（[在此处查看步骤](https://colonysupport.quali.com/hc/articles/360008464234)）。

:::image type="content" source="./media/create-application-centric-environment/devops-pipeline.png" alt-text="连接到 Azure DevOps 管道":::    

## <a name="next-steps"></a>后续步骤

[请求蚁群演示](https://info.quali.com/cloudshell-colony-demo-request)
