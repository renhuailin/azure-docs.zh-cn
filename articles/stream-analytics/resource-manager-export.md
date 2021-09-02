---
title: 导出 Azure 流分析作业 Azure 资源管理器模板
description: 本文介绍如何导出适用于 Azure 流分析作业的 Azure 资源管理器模板。
services: stream-analytics
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: c67d6895fe4dde54992c7a79067a45dcdff18305
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113586412"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>导出 Azure 流分析作业 Azure 资源管理器模板

通过 [Azure 资源管理器模板](../azure-resource-manager/templates/overview.md)，可实现基础结构即代码。 该模板是一个定义资源基础结构和配置的 JavaScript 对象表示法 (JSON) 文件。 你可以指定要部署的资源以及这些资源的属性。

可以通过导出 Azure 资源管理器模板重新部署 Azure 流分析作业。

## <a name="open-a-job-in-vs-code"></a>在 VS Code 中打开作业

必须先在 Visual Studio Code 中打开现有的流分析作业，然后才能导出模板。 

若要将作业导出到本地项目，请在 Azure 门户的“流分析资源管理器”中找到要导出的作业。 在“查询”页中，选择“在 Visual Studio 中打开” 。 然后选择“Visual Studio Code”。

![在 Visual Studio Code 中打开流分析作业](./media/resource-manager-export/open-job-vs-code.png)

有关使用 Visual Studio Code 管理流分析作业的详细信息，请参阅 [Visual Studio Code 快速入门](quick-create-visual-studio-code.md)。

## <a name="compile-the-script"></a>编译脚本 

下一步是将作业脚本编译为 Azure 资源管理器模板。 在编译脚本之前，请确保作业至少配置了一个输入和一个输出。 如果未配置任何输入或输出，则需要先配置输入和输出。

1. 在 Visual Studio Code 中，导航到作业的“Transformation.asaql”文件。

   ![Visual Studio Code 中的 Transformation.asaql 文件](./media/resource-manager-export/transformation-asaql.png)

1. 右键单击“Transformation.asaql”文件，然后从菜单中选择“ASA: 编译脚本”。

1. 请注意，“部署”文件夹会显示在流分析作业工作区中。

1. 浏览 JobTemplate.json 文件，这是用于部署的 Azure 资源管理器模板。

## <a name="complete-the-parameters-file"></a>完成参数文件

接下来，完成 Azure 资源管理器模板参数文件。

1. 在 Visual Studio Code 中打开流分析作业工作区的“部署”文件夹中的“JobTemplate.parameters.json”文件。

1. 请注意，输入和输出密钥为 NULL。 将 NULL 值替换为输入和输出资源的实际访问密钥。

1. 保存参数文件。

## <a name="deploy-using-templates"></a>使用模板进行部署

现在可以使用在上一部分中生成的 Azure 资源管理器模板来部署 Azure 流分析作业。

在 PowerShell 窗口中运行以下命令。 请确保将 ResourceGroupName、TemplateFile 和 TemplateParameterFile 替换为实际资源组名称，并替换作业工作区的“部署”文件夹中 JobTemplate.json 和 JobTemplate.parameters.json 文件的完整文件路径    。

如果尚未配置 Azure PowerShell，请按照[安装 Azure PowerShell 模块](/powershell/azure/install-Az-ps)中的步骤进行操作。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>后续步骤

* [通过 Visual Studio Code，使用实时输入在本地测试 Azure 流分析作业](visual-studio-code-local-run-live-input.md)

* [使用 Visual Studio Code（预览版）浏览 Azure 流分析作业](visual-studio-code-explore-jobs.md)
