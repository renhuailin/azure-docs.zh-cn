---
title: 创建映像工厂
description: 本文介绍如何使用 Git 存储库（Azure 开发测试实验室）中提供的示例脚本来设置自定义映像中心。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 0e4ed8444475d1cd396dfa6c54083858b7671d5e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128623582"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中创建自定义映像中心
本文介绍如何使用 [Git 存储库](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)中提供的示例脚本来设置自定义映像中心。

## <a name="whats-an-image-factory"></a>什么是映像中心？
映像中心是一个配置即代码解决方案，其使用所有所需的配置定期自动构建和分发映像。 映像中心的映像始终是最新的，实现整个过程自动化后，几乎无需进行持续维护。 并且，由于所有必需的配置都已在映像中，因此在使用基础 OS 创建 VM 后，将节省手动配置系统的时间。

在开发测试实验室中使开发者桌面进入就绪状态的重要促进因素是使用自定义映像。 自定义映像的缺点在于，要在实验室进行一些额外的维护。 例如，产品的试用版在一段时间后过期（或）未应用新发布的安全更新程序，这会迫使我们定期刷新自定义映像。 在映像中心，可以将映像定义签入源代码管理中，并可使用自动化流程基于该定义生成自定义映像。

此解决方案可快速从自定义映像创建虚拟机，同时消除额外的持续维护成本。 利用此解决方案，可以自动创建自定义映像，将其分发给其他开发测试实验室，并停用旧映像。 在下面的视频中，你将了解映像中心，以及如何在开发测试实验室中实现它。  所有 Azure Powershell 脚本都可从以下网址免费获取：[https://aka.ms/dtlimagefactory](https://aka.ms/dtlimagefactory)。

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>解决方案的简要概述
此解决方案可快速从自定义映像创建虚拟机，同时消除额外的持续维护成本。 利用此解决方案，可以自动创建自定义映像，并将其分发给其他开发测试实验室。 使用 Azure DevOps（以前称为 Visual Studio Team Services）作为业务流程引擎，以便实现开发测试实验室中所有操作的自动化。

![解决方案的简要概述](./media/create-image-factory/high-level-view-of-solution.png)

有一个[用于开发测试实验室的 VSTS 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)，可用于执行以下各个步骤：

- 创建自定义映像
- 创建 VM
- 删除 VM
- 创建环境
- 删除环境
- 填充环境

使用开发测试实验室扩展，可以轻松地开始在开发测试实验室中自动创建自定义映像。

对于更复杂的方案，可使用 PowerShell 脚本进行备用实现。 使用 PowerShell，可以基于开发测试实验室完全自动创建映像中心，可在持续集成和持续交付 (CI/CD) 工具链中使用。 本备用解决方案遵循的原则包括：

- 常见更新应该不需要对映像中心进行更改。 （例如，添加新类型的自定义映像，自动停用旧映像，添加新的“终结点”开发测试实验室来接收自定义映像等。）
- 常见更改由源代码管理（基础结构即代码）支持
- 接收自定义映像的开发测试实验室可能不在同一 Azure 订阅中（实验室范围内的订阅）
- PowerShell 脚本必须可重用，以便我们可以根据需要增加更多中心

## <a name="next-steps"></a>后续步骤
转到本部分中的下一篇文章：[从 Azure DevOps 运行映像中心](image-factory-set-up-devops-lab.md)
