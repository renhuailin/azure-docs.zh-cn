---
title: Azure DevOps Starter 概述 | Microsoft Docs
description: 了解 DevOps Starter 的价值
services: devops-project
documentationcenter: ''
author: mlearned
manager: gwallace
editor: mlearned
ms.assetid: ''
ms.service: az-devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/24/2020
ms.author: mlearned
ms.openlocfilehash: 7c36539ef5be503b2cb7e14047e596522ef8e962
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "92330642"
---
# <a name="overview-of-devops-starter"></a>DevOps Starter 概述

 可使用 GitHub Actions 或 Azure DevOps 通过 DevOps Starter 轻松完成 Azure 入门。 有了它，仅需几个简单的步骤即可从 Azure 门户在所选的 Azure 服务上启动最喜爱的应用。 

 DevOps Starter 提供开发、部署和监视应用程序所需的一切设置。 可以通过 DevOps Starter 仪表板，从 Azure 门户的单一视图监视代码提交、生成和部署。

## <a name="advantages-of-using-devops-starter"></a>使用 DevOps Starter 的优势

  DevOps Starter 支持以下两个 CI/CD 提供程序，以自动执行部署
  * [GitHub Actions](https://github.com/features/actions)
  * [Azure DevOps](https://azure.microsoft.com/services/devops)

  DevOps Starter 将应用程序的整个持续集成 (CI) 和持续交付 (CD) 的设置自动执行到 Azure。  可以从现有的代码着手，也可以使用提供的示例应用程序之一。 然后，可以快速将该应用程序部署到不同的 Azure 服务，例如虚拟机、应用服务、Azure Kubernetes 服务 (AKS)、Azure SQL 数据库和 Azure Service Fabric。  

  DevOps Starter 可完成初始配置 DevOps 管道的所有工作，包括设置初始 Git 存储库、配置 CI/CD 管道、创建用于监视的 Application Insights 资源，以及通过在 Azure 门户中创建 DevOps Starter 仪表板提供整个解决方案的单一视图。

可以使用 DevOps Starter 执行以下操作：

* 快速将应用程序部署到 Azure
* 自动设置 CI/CD 工作流或管道
* 查看并了解如何正确设置 CI/CD 工作流或管道
* 进一步根据特定方案自定义发布管道

## <a name="how-to-use-devops-starter"></a>如何使用 DevOps Starter？

  可从 Azure 门户获取 DevOps Starter。 创建 DevOps Starter 资源的方式与在门户中创建任何其他 Azure 资源相同。 DevOps Projects 为各种配置选项提供了分步的向导式体验。  

在初始设置过程中，选择多个配置选项。 这些选项包括：

* 选择偏爱的 CI/CD 提供程序
* 使用所提供的示例应用或使用自己的代码（仅用于 Azure DevOps）
* 选择应用语言
* 选择基于语言的应用框架
* 选择 Azure 服务（部署目标）
* 选择 GitHub 或 Azure DevOps 组织
* 选择 Azure 订阅
* 选取 Azure 服务的位置
* 从 Azure 服务的各种定价层中进行选择

创建 DevOps Starter 后，可以：

* 自定义 GitHub 工作流或 Azure DevOps 管道
* 使用拉取请求管理代码流并保持高质量
* 在合并代码之前，测试和生成每个提交以提升质量要求

在使用 DevOps Starter 后，还可以从 Azure 门户上 DevOps Starter 仪表板的单个位置删除所有资源。

## <a name="devops-starter-and-github-integration"></a>DevOps Starter 和 GitHub 集成

DevOps Starter 现在支持将 GitHub Actions 作为 CI/CD 提供程序。 它使用 GitHub Actions 自动完成 GitHub 中设置 CI/CD 工作流所需的所有工作。 它在现有 GitHub 组织中创建 GitHub 存储库，然后将示例应用程序提交到新的 GitHub 存储库。  

自动化功能还为工作流建立触发器，使每个新的代码提交都启动生成并在工作流中部署作业。 此应用程序可部署到所选的 Azure 服务。 可以为其他方案自定义 GitHub 工作流。 

## <a name="devops-starter-and-azure-devops-integration"></a>DevOps Starter 和 Azure DevOps 集成

使用 Azure DevOps 的 DevOps Starter 可自动执行 Azure Pipelines 中设置 CI/CD 管道所需的所有工作。 它在新的或现有的 Azure DevOps 组织中创建一个 Git 存储库，然后将示例应用程序或现有代码提交到新的 Git 存储库。  

自动执行还为生成建立 CI 触发器，使每个新的代码提交都启动生成。 DevOps Starter 创建一个 CD 触发器并将每个新的成功的生成部署到所选的 Azure 服务。  

可以为其他方案自定义生成和发布管道。 另外，还可以克隆生成和发布管道，用于其他项目。

## <a name="getting-started-with-devops-starter"></a>DevOps Starter 入门

* [DevOps Starter 入门](./azure-devops-project-github.md)

##  <a name="devops-starter-videos"></a>DevOps Starter 视频

* [使用 Azure DevOps Starter 创建 CI/CD](https://www.youtube.com/watch?v=NuYDAs3kNV8)
