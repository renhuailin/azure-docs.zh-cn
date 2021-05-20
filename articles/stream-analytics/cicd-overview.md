---
title: Azure 流分析的持续集成和部署
description: 本文概述了 Azure 流分析的持续集成和部署 (CI/CD) 管道。
services: stream-analytics
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: 940813f12d542715db47781731144a75e854a98e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019561"
---
# <a name="continuous-integration-and-deployment-cicd-for-azure-stream-analytics"></a>Azure 流分析的持续集成和部署 (CI/CD)

可以使用源代码管理集成持续部署 Azure 流分析作业。 源代码管理集成启用一个工作流，代码更新在其中触发到 Azure 的资源部署。 本文概述了创建持续集成和部署 (CI/CD) 管道的基本步骤。

如果不熟悉 Azure 流分析，请开始使用 [Azure 流分析快速入门](stream-analytics-quick-create-portal.md)。

## <a name="create-a-cicd-pipeline"></a>创建 CI/CD 管道

按照本指南中的步骤为流分析创建 CI/CD 管道。

1. 开发 Azure 流分析查询。

   使用适用于 [Visual Studio Code](./quick-create-visual-studio-code.md) 或 [Visual Studio](stream-analytics-quick-create-vs.md) 的 Azure 流分析工具[在本地开发和测试查询](develop-locally.md)。 也可以[将现有作业导出到本地项目](visual-studio-code-explore-jobs.md#export-a-job-to-a-local-project)。

2. 将 Azure 流分析项目提交到源代码管理系统，如 Git 存储库。

3. 使用 [Azure 流分析 CI/CD 工具](cicd-tools.md)生成项目并生成用于部署的 Azure 资源管理模板。

4. 运行用于质量回归的[自动化脚本测试](cicd-tools.md#automated-test)。

5. [自动将作业部署到 Azure](cicd-tools.md#deploy-to-azure)。

## <a name="auto-build-test-and-deploy"></a>自动生成、测试和部署

可以使用命令行和 [Azure 流分析 CI/CD 工具](cicd-tools.md)自动生成、测试和部署。 还可以在 [Azure Pipelines](set-up-cicd-pipeline.md) 中设置 CI/CD 管道。 Azure Pipelines 可启用更高级的功能，例如管道管理、可视化效果和触发器。

## <a name="next-steps"></a>后续步骤

* [使用 CI/CD 工具自动生成、测试和部署 Azure 流分析作业](cicd-tools.md)
* [使用 Azure Pipelines 为流分析作业设置 CI/CD 管道](set-up-cicd-pipeline.md)