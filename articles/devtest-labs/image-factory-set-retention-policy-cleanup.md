---
title: 设置保留策略
description: 了解如何在开发测试实验室中配置保留策略、清理工厂和停用旧映像。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 91baf275a1ff85cadd0e1226ceb2f1ff529189c0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128642864"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中设置保留策略
本文介绍如何在组织的所有其他开发测试实验室中设置保留策略、清理工厂和停用旧映像。 

## <a name="prerequisites"></a>先决条件
在继续下一步之前，请务必遵循以下文章中的要求：

- [创建映像工厂](image-factory-create.md)
- [从 Azure DevOps 运行映像工厂](image-factory-set-up-devops-lab.md)
- [保存自定义映像并分发到多个实验室](image-factory-save-distribute-custom-images.md)

应已准备好以下各项：

- Azure 开发测试实验室中的映像工厂的实验室
- 一个或多个目标 Azure 开发测试实验室，工厂将在其中分发黄金映像
- 用于实现映像工厂自动化的 Azure DevOps 项目。
- 包含脚本和配置的源代码位置（在我们的示例中，该位置在上面使用的 DevOps 项目中）
- 用于协调 Azure PowerShell 任务的生成定义
 
## <a name="setting-the-retention-policy"></a>设置保留策略
在配置“清理”步骤之前，请定义要在开发测试实验室中保留的历史映像数量。 按照[从 Azure DevOps 运行映像工厂](image-factory-set-up-devops-lab.md)一文的要求进行操作时，已配置各种生成变量。 其中的一个变量是 **ImageRetention**。 将此变量设置为 `1`，这意味着开发测试实验室不会保留自定义映像的历史记录。 只会提供最新的分布式映像。 如果将此变量更改为 `2`，则会保留最新的分布式映像和之前的分布式映像。 可以设置此值来定义要在开发测试实验室中保留的历史映像的数目。

## <a name="cleaning-up-the-factory"></a>清理工厂
清理工厂的第一步是从映像工厂中删除黄金映像 VM。 可以使用脚本来执行此任务，就像使用前面的脚本那样。 第一步是将另一个 **Azure PowerShell** 任务添加到生成定义，如下图所示：

![PowerShell 步骤](./media/set-retention-policy-cleanup/powershell-step.png)

将新任务置于列表中以后，请选择该项，然后填写所有详细信息，如下图所示：

![清理旧映像 PowerShell 任务](./media/set-retention-policy-cleanup/configure-powershell-task.png)

脚本参数为 `-DevTestLabName $(devTestLabName)`。

## <a name="retire-old-images"></a>停用旧映像 
此任务会删除任何旧的映像，仅保留与 **ImageRetention** 生成变量匹配的历史记录。 向生成定义添加其他 **Azure PowerShell** 生成任务。 添加该任务后，选择它，并填写详细信息，如下图所示： 

![停用旧映像 PowerShell 任务](./media/set-retention-policy-cleanup/retire-old-image-task.png)

脚本参数为 `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>让生成排队
现在，你已完成生成定义，请让新的生成排队，以确保一切正常。 成功完成生成后，新的自定义映像会显示在目标实验室中。如果你检查映像工厂实验室，将看不到预配的 VM。 此外，如果让更多的生成排队，则会看到清理任务根据生成变量中设置的保留值从开发测试实验室中停用旧的自定义映像。

> [!NOTE]
> 如果在此系列的最后一篇文章末尾执行了生成管道，请在让新的生成排队之前，手动删除在映像工厂实验室中创建的虚拟机。  仅当我们设置好一切并验证其可以正常工作时，才需要手动清理步骤。



## <a name="summary"></a>总结
现在，你有了一个正在运行的映像工厂，该工厂可以根据需要生成自定义映像并将其分发到实验室。 此时，只需正确设置图像并标识目标实验室即可。 如前一篇文章所述，位于 **Configuration** 文件夹中的 **Labs.json** 文件指定应在每个目标实验室中提供哪些映像。 将其他开发测试实验室添加到组织中时，只需在 Labs.json 中为新实验室添加一个条目。

将新映像添加到工厂也很简单。 如果要在工厂中包含新映像，请打开 [Azure 门户](https://portal.azure.com)，导航到工厂开发测试实验室，选择用于添加 VM 的按钮，然后选择所需的市场映像和项目。 选择“查看 Azure 资源管理器模板”（而不是选择“创建”按钮来创建新的 VM），将模板另存为存储库中 GoldenImages 文件夹内的一个 .json 文件。 下次你运行映像工厂时，它会创建你的自定义映像。


## <a name="next-steps"></a>后续步骤
1. [计划生成/发布](/azure/devops/pipelines/build/triggers?tabs=designer)以便定期运行映像工厂。 它会定期刷新工厂生成的映像。
2. 为工厂创建更多黄金映像。 你还可以考虑[创建项目](devtest-lab-artifact-author.md)以编写 VM 设置任务的其他部分的脚本，并将项目包括在工厂映像中。
4. 创建 [单独的生成/发布](/azure/devops/pipelines/overview)来单独运行 **DistributeImages** 脚本。 你可以在对 Labs.json 进行更改时运行此脚本，将映像复制到目标实验室，无需重新创建所有映像。
