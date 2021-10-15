---
title: 安装 Visual Studio Code 扩展（预览版）
titleSuffix: Azure Machine Learning
description: 了解如何安装 Azure 机器学习 Visual Studio Code 扩展。
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.service: machine-learning
ms.subservice: core
ms.date: 05/25/2021
ms.topic: how-to
ms.custom: devplatv2
ms.openlocfilehash: 2b175d209c6506cb802e0105dff03c7c0f6e6ad9
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129425024"
---
# <a name="set-up-the-visual-studio-code-azure-machine-learning-extension-preview"></a>安装 Visual Studio Code Azure 机器学习扩展（预览版）

了解如何为机器学习工作流安装 Azure 机器学习 Visual Studio Code 扩展。

> [!div class="mx-imgBorder"]
> ![VS Code 扩展](./media/how-to-setup-vs-code/vs-code-extension.PNG)

适用于 VS Code 的 Azure 机器学习扩展提供一个用户界面用于执行以下操作：

- 管理 Azure 机器学习资源（试验、虚拟机、模型、部署等）
- 使用远程计算实例在本地进行开发
- 训练机器学习模型
- 在本地调试机器学习试验
- 支持基于架构的语言，为规范文件创作提供自动完成和诊断
- 用于完成常见任务的代码片段

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有订阅，注册之后即可试用 [Azure 机器学习免费版或付费版](https://azure.microsoft.com/free/)。
- Visual Studio Code。 如果尚未安装它，请[安装它](https://code.visualstudio.com/docs/setup/setup-overview)。
- [Python](https://www.python.org/downloads/)
- （可选）若要使用扩展创建资源，需要安装 CLI (v2)。 有关安装说明，请参阅[安装、设置和使用 CLI (v2)（预览版）](how-to-configure-cli.md)。

## <a name="install-the-extension"></a>安装扩展

1. 打开 Visual Studio Code。
1. 从 **活动栏** 选择“扩展”图标，打开“扩展”视图。
1. 在“扩展”视图中，搜索“Azure 机器学习”。
1. 选择“安装”  。

    > [!div class="mx-imgBorder"]
    > ![安装 Azure 机器学习 VS Code 扩展](./media/how-to-setup-vs-code/install-aml-vscode-extension.PNG)

> [!NOTE]
> 也可[直接下载安装程序](https://aka.ms/vscodetoolsforai)，以便通过 Visual Studio Marketplace 安装 Azure 机器学习扩展。

本教程中的其余步骤已使用最新版本的扩展进行了测试。

> [!NOTE]
> Azure 机器学习 VS Code 扩展默认使用 CLI (v2)。 若要切换到 1.0 CLI，请将 Visual Studio Code 中的 `azureML.CLI Compatibility Mode` 设置指定为 `1.0`。 有关在 Visual Studio 中修改设置的详细信息，请参阅[用户和工作区设置文档](https://code.visualstudio.com/docs/getstarted/settings)。

## <a name="sign-in-to-your-azure-account"></a>登录 Azure 帐户

若要在 Azure 上预配资源并运行工作负载，必须使用 Azure 帐户凭据登录。 Azure 机器学习会自动安装 Azure 帐户扩展，帮助你进行帐户管理。 请访问以下站点，[详细了解 Azure 帐户扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)。

若要登录到你的 Azure 帐户，请在 Visual Studio Code 状态栏上选择“Azure: 登录”按钮启动登录过程。

或者，可以使用命令面板：

1. 从菜单栏选择“视图”>“命令面板”，打开命令面板。
1. 在命令面板中输入命令“Azure: Sign In”启动登录过程。

## <a name="choose-your-default-workspace"></a>选择默认工作区

在创作 CLI (v2) YAML 规范文件时，选择默认的 Azure 机器学习工作区可启用以下功能：

- 架构验证
- 自动完成
- 诊断

如果你没有工作区，请创建一个。 有关详细信息，请参阅[使用 VS Code 扩展管理 Azure 机器学习资源](how-to-manage-resources-vscode.md)。

若要选择默认工作区，请在 Visual Studio Code 状态栏上选择“设置 Azure ML 工作区”按钮，然后按照提示设置工作区。

或者，在命令面板中使用 `> Azure ML: Set Default Workspace` 命令，然后按照提示设置工作区。

## <a name="next-steps"></a>后续步骤

- [管理 Azure 机器学习资源](how-to-manage-resources-vscode.md)
- [在本地通过远程计算实例进行开发](how-to-set-up-vs-code-remote.md)
- [将计算实例用作远程 Jupyter 服务器](how-to-set-up-vs-code-remote.md)
- [使用 Visual Studio Code 扩展训练图像分类模型](tutorial-train-deploy-image-classification-model-vscode.md)
- [在本地运行和调试机器学习试验](how-to-debug-visual-studio-code.md)