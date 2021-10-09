---
title: 将环境集成到 Azure Pipelines
description: 了解如何将 Azure 开发测试实验室环境集成到 Azure DevOps 持续集成 (CI) 和持续交付 (CD) 管道。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 4f205fb70978a667df8a8fe82beea38e492828a0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128651113"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>将环境集成到 Azure DevOps CI/CD 管道中
你可以使用安装在 Azure DevOps Services 中的 Azure DevTest Labs Tasks 扩展，轻松地将持续集成 (CI)/持续交付 (CD) 生成和发布管道与 Azure 开发测试实验室集成。 这些扩展便于你为特定测试任务快速部署[环境](devtest-lab-test-env.md)，并在测试完成后将其删除。 

本文介绍如何在一个完整的管道中创建、部署和删除环境。 通常要在自己的自定义生成-测试-部署管道中单独执行每个任务。 本文中使用的扩展是对以下[创建/删除 DTL VM 任务](devtest-lab-integrate-ci-cd.md)的补充：

- 创建环境
- 删除环境

## <a name="before-you-begin"></a>开始之前
将 CI/CD 管道与 Azure 开发测试实验室集成之前，请从 Visual Studio Marketplace 安装 [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) 扩展。 

## <a name="create-and-configure-the-lab-for-environments"></a>为环境创建和配置实验室
本部分介绍如何创建和配置将在其中部署 Azure 环境的实验室。

1. [创建实验室](devtest-lab-create-lab.md)（如果还没有）。 
2. 按照[使用 Azure 资源管理器模板创建多 VM 环境和 PaaS 资源](devtest-lab-create-environment-from-arm.md)一文中的说明配置实验室并创建环境模板。
3. 对于此示例，请使用现有的 Azure 快速启动模板 [https://azure.microsoft.com/resources/templates/web-app-redis-cache-sql-database](https://azure.microsoft.com/resources/templates/web-app-redis-cache-sql-database)。
4. 将 web-app-redis-cache-sql-database 文件夹复制到在步骤 2 中配置于存储库中的 ArmTemplate 文件夹。

## <a name="create-a-release-definition"></a>创建发布定义
要创建发布定义，请执行以下操作：

1.  在“生成与发布”中心的“发布”选项卡上，选择加号 (+) 按钮  。
2.  在“创建发布定义”窗口中，选择“空”模板，然后选择“下一步”。
3.  选择“稍后选择”和“创建”，以新建有一个默认环境且无链接项目的发布定义。
4.  在新发布定义中，选择环境名称旁边的省略号 (...)，然后选择“配置变量”，可打开快捷菜单 。
5.  在“配置 - 环境”窗口中，对于在发布定义中使用的变量，输入以下值：
1.  对于 administratorLogin，请输入 SQL 管理员登录名。
2.  对于 administratorLoginPassword，请输入 SQL 管理员登录使用的密码。 使用“挂锁”图标隐藏和保护密码。
3.  对于 databaseName，请输入 SQL 数据库名称。
4.  这些变量特定于示例环境，不同环境可能具有不同的变量。

## <a name="create-an-environment"></a>创建环境
部署的下一阶段是创建用于开发或测试目的的环境。

1. 在发布定义中，选择“添加任务”。
2. 在“任务”选项卡上，添加“Azure 开发测试实验室创建环境”任务。 对任务进行如下配置：
    1. 对于“Azure RM 订阅”，从“可用 Azure 服务连接”列表中选择连接，或创建到 Azure 订阅的限制更严格的权限连接。 有关详细信息，请参阅 [Azure 资源管理器服务终结点](/azure/devops/pipelines/library/service-endpoints)。
2. 对于“实验室名称”，请选择之前创建的实例的名称*。
3. 对于“存储库名称”，请选择资源管理器模板 (201) 推送到的存储库*。
4. 对于“模板名称”，请选择已保存到源代码存储库中的环境的名称*。 
5. “实验室名称”、“存储库名称”和“模板名称”是 Azure 资源 ID 的易记表示形式  。 手动输入易记名称会导致失败，请使用下拉列表选择信息。
6. 对于“环境名称”，请输入一个唯一标识实验室中的环境实例的名称。  它在实验室内必须是唯一的。
7. 借助“参数文件”和“参数”，可以将自定义参数传递到环境 。 其中一个或两个都可以用于设置参数值。 此示例将使用“参数”部分。 使用在环境中定义的变量的名称，例如：`-administratorLogin "$(administratorLogin)" -administratorLoginPassword "$(administratorLoginPassword)" -databaseName "$(databaseName)" -cacheSKUCapacity 1`
8. 环境模板中的信息可以在模板的输出部分中传递。 选中“根据环境模板输出创建输出变量”，以便其它任务可以使用数据。 `$(Reference name.Output Name)` 是要遵循的模式。 例如，如果“引用名称”是 DTL，并且模板中输出名称是 location，则变量将为 `$(DTL.location)`。

## <a name="delete-the-environment"></a>删除环境
最后一个阶段是删除在 Azure 开发测试实验室实例中部署的环境。 在部署的资源上执行所需开发任务或运行所需测试后，通常要删除环境。

在发布定义中，选择“添加任务”，然后在“部署”选项卡上添加“Azure 开发测试实验室删除环境”任务  。 请如下所述对其进行配置：

1. 若要删除 VM，请参阅 [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)：
    1. 对于“Azure RM 订阅”，从“可用 Azure 服务连接”列表中选择连接，或创建到 Azure 订阅的限制更严格的权限连接。 有关详细信息，请参阅 [Azure 资源管理器服务终结点](/azure/devops/pipelines/library/service-endpoints)。
    2. 对于“实验室名称”，请选择环境所在的实验室。
    3. 对于“环境名称”，请输入要删除的环境的名称。
2. 输入发布定义名称，然后保存它。

## <a name="next-steps"></a>后续步骤
请参阅以下文章： 
- [使用资源管理器模板创建多 VM 环境](devtest-lab-create-environment-from-arm.md)。
- 有关适用于开发测试实验室自动化的资源管理器模板快速入门，请参阅[开发测试实验室 GitHub 存储库](https://github.com/Azure/azure-quickstart-templates)。
- [VSTS 故障排除页](/azure/devops/pipelines/troubleshooting)
