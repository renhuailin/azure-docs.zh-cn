---
title: 在区域之间复制、备份和移动 Azure 流分析作业
description: 本文介绍如何移动、复制和备份 Azure 流分析作业。
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/11/2019
ms.openlocfilehash: 7b3d203d3d34612b3495fa3eceaf13e357eaa2be
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829404"
---
# <a name="copy-back-up-and-move-your-azure-stream-analytics-jobs-between-regions"></a>在区域之间复制、备份和移动 Azure 流分析作业

可以使用 Visual Studio Code 或 Visual Studio 来复制或备份已部署的 Azure 流分析作业。 将作业复制到其他区域时不会复制上次输出时间。 因此，在启动复制的作业时，不能使用 [**上次停止时**](./start-job.md#start-options)选项。

## <a name="before-you-begin"></a>开始之前
* 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/)。

* 登录到 [Azure 门户](https://portal.azure.com/)。

* 安装[适用于 Visual Studio Code 的 Azure 流分析扩展](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension)或[适用于 Visual Studio 的 Azure 流分析工具](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension)。  

## <a name="visual-studio-code"></a>Visual Studio Code

1. 单击 Visual Studio Code 活动栏上的 **Azure** 图标，然后展开“流分析”节点。 你的作业应显示在你的订阅下。

   ![打开流分析资源管理器](./media/vscode-explore-jobs/open-explorer.png)

2. 若要将作业导出到本地项目，请在 Visual Studio Code 的 **流分析资源管理器** 中找到要导出的作业。 然后为项目选择一个文件夹。

    ![在 Visual Studio Code 中找到 ASA 作业](./media/vscode-explore-jobs/export-job.png)

    项目会导出到所选文件夹并添加到当前工作区。

3. 若要使用另一名称将作业发布到其他区域或备份，请在查询编辑器 (\*.asaql) 中选择“从订阅中选择以发布”，然后按照说明进行操作。

    ![在 Visual Studio Code 中发布到 Azure](./media/quick-create-visual-studio-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. 按照[将部署的 Azure 流分析作业导出到项目说明](./stream-analytics-vs-tools.md#export-jobs-to-a-project)进行操作。

2. 在查询编辑器中打开 \*.asaql 文件，在脚本编辑器中选择“提交到 Azure”，然后按照说明使用新名称将作业发布到其他区域或备份。

## <a name="next-steps"></a>后续步骤

* [快速入门：使用 Visual Studio Code 创建流分析作业](quick-create-visual-studio-code.md)
* [快速入门：使用 Visual Studio 创建流分析作业](stream-analytics-quick-create-vs.md)