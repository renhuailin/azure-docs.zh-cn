---
title: 在工作室（预览版）中管理环境
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习工作室中创建和管理环境。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: saachigopal
ms.author: sagopal
ms.date: 5/25/2021
ms.topic: how-to
ms.custom: devx-track-python
ms.openlocfilehash: 8015152a7888df86128aaff31d4b8c6dd9179cf2
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112460587"
---
# <a name="manage-software-environments-in-azure-machine-learning-studio-preview"></a>在 Azure 机器学习工作室（预览版）中管理软件环境

本文介绍如何在 Azure 机器学习工作室中创建和管理 Azure 机器学习[环境](/python/api/azureml-core/azureml.core.environment.environment)。 使用环境可以在项目软件依赖项演化时对其进行跟踪和再现。

本文中的示例演示如何执行以下操作：

* 浏览特选环境。
* 创建环境并指定包依赖项。
* 编辑现有的环境规范及其属性。
* 重新生成环境并查看映像生成日志。

有关环境在 Azure 机器学习中的作用的综合概述，请参阅[什么是 ML 环境？](concept-environments.md) 有关详细信息，请参阅[如何设置 Azure 机器学习的开发环境](how-to-configure-environment.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* 一个 [Azure 机器学习工作区](how-to-manage-workspace.md)

## <a name="browse-curated-environments"></a>浏览特选环境

特选环境包含 Python 包的集合，默认情况下可以在你的工作区中使用。 这些环境由缓存的 Docker 映像支持，降低了运行准备成本。 

单击环境以查看其内容的详细信息。 有关详细信息，请参阅 [Azure 机器学习特选环境](resource-curated-environments.md)。 

## <a name="create-an-environment"></a>创建环境

要创建环境，请执行以下操作：
1. 在 [Azure 机器学习工作室](https://ml.azure.com)中打开工作区。
1. 在左侧选择“环境”。
1. 选择“自定义环境”选项卡。 
1. 选择“创建”按钮。 

通过指定以下内容之一来创建环境：
* Pip 要求[文件](https://pip.pypa.io/en/stable/cli/pip_install)
* Conda yaml [文件](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html)
* Docker [映像](https://hub.docker.com/search?q=&type=image)
* [Dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

:::image type="content" source="media/how-to-manage-environments-in-studio/create-page.jpg" alt-text="环境创建向导":::

你可以自定义配置文件、添加标记和说明并在创建实体之前查看属性。 

如果为新环境指定的名称与工作区中现有环境的名称相同，则将创建现有环境的新版本。

## <a name="view-and-edit-environment-details"></a>查看并编辑环境详细信息

创建环境后，单击名称即可查看其详细信息。 使用下拉菜单选择环境的不同版本。 在这里，你可以通过环境的 Docker 和 Conda 层查看其元数据和内容。 

单击铅笔图标以编辑标记、说明以及配置文件或映像。 请记住，对 Docker 或 Conda 部分所做的任何更改都将创建新版环境。 

:::image type="content" source="media/how-to-manage-environments-in-studio/details-page.jpg" alt-text="环境详细信息页":::

## <a name="view-image-build-logs"></a>查看映像生成日志

单击详细信息页中的“生成日志”选项卡以查看环境版本的映像生成日志。 

## <a name="rebuild-an-environment"></a>重新生成环境

在详细信息页中，单击“重新生成”按钮以重新生成环境。 配置文件中的任何取消固定的包版本都可以通过此操作更新到最新版本。 
