---
title: 从 Azure DevOps 运行映像工厂
description: 本文介绍从 Azure DevOps（之前称为 Visual Studio Team Services）运行映像工厂所需的全部准备工作。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: bc12dc67160a68898191ab802cc9f6e9bd3f3f62
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604853"
---
# <a name="run-an-image-factory-from-azure-devops"></a>从 Azure DevOps 运行映像工厂
本文介绍从 Azure DevOps（之前称为 Visual Studio Team Services）运行映像工厂所需的全部准备工作。

> [!NOTE]
> 所有业务流程引擎都适用！ Azure DevOps 不是必需的。 映像工厂通过 Azure PowerShell 脚本运行，因此可以使用 Windows 任务计划程序、其他 CI/CD 系统等来手动运行它。

## <a name="create-a-lab-for-the-image-factory"></a>为映像工厂创建实验室
设置映像工厂的第一步是在 Azure 开发测试实验室中创建实验室。 此实验室是映像工厂实验室，在其中可以创建虚拟机并保存自定义映像。 此实验室被视为整个映像工厂过程的一部分。 创建实验室后，请确保保存该名称，因为稍后需要用到它。

## <a name="scripts-and-templates"></a>脚本和模板
为团队采用映像工厂的下一步是了解可用的内容。 [开发测试实验室 GitHub 存储库](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)中公开提供映像工厂脚本和模板。 下面对此进行简要概述：

- 映像工厂。 它是根文件夹。
    - 配置。 映像工厂的输入
        - GoldenImages。 此文件夹包含表示自定义映像定义的 JSON 文件。
        - Labs.json。 团队注册以接收特定自定义映像的文件。
- 脚本。 映像工厂的引擎。

本部分中的文章提供了有关这些脚本和模板的详细信息。

## <a name="create-an-azure-devops-team-project"></a>创建 Azure DevOps 团队项目
通过 Azure DevOps 可以存储源代码，并在一个位置运行 Azure PowerShell。 可以计划定期运行以保持更新映像。 有较好的工具可以记录结果，便于诊断任何问题。  但并不强制使用 Azure DevOps，你可以使用任何可连接到 Azure 的工具/引擎并运行 Azure PowerShell。

如果想改用现有的 DevOps 帐户或项目，请跳过此步骤。

若要开始使用，请在 Azure DevOps 中创建一个免费帐户。 访问 https://www.visualstudio.com/ 并在“Azure DevOps”（以前称为 VSTS）正下方选择“免费试用”。 需要选择唯一帐户名，并确保选择使用 Git 来管理代码。 创建帐户后，将 URL 保存到团队项目。 下面是一个示例 URL：`https://<accountname>.visualstudio.com/MyFirstProject`。

## <a name="check-in-the-image-factory-to-git"></a>将映像工厂签入到 Git
映像工厂的所有 PowerShell、模板和配置都位于[公共开发测试实验室 GitHub 存储库](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)中。 将代码引入新团队项目的最快方法是导入存储库。 这会对整个开发测试实验室存储库执行拉取操作（因此会获取额外的文档和示例）。

1. 访问在上一步中创建的 Azure DevOps 项目（URL 类似于 https:\//\<accountname>.visualstudio.com/MyFirstProject）。
2. 选择“导入存储库”。
3. 输入开发测试实验室存储库的“克隆 URL”：`https://github.com/Azure/azure-devtestlab`。
4. 选择“导入”  。

    ![导入 Git 存储库](./media/set-up-devops-lab/import-git-repo.png)

如果决定只签入确切所需的内容（映像工厂文件），请按照[此处](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs)步骤克隆 Git 存储库并仅推送 scripts/ImageFactory 目录中的文件。

## <a name="create-a-build-and-connect-to-azure"></a>创建生成并连接到 Azure
此时，Azure DevOps 的 Git 存储库中存储了源文件。 现在，需要设置管道来运行 Azure PowerShell。 有诸多选择来执行这些步骤。 为了简单起见，本文将使用生成定义，但它会用于 DevOps 生成、DevOps 发布（单个或多个环境）、其他执行引擎（例如 Windows 任务计划程序）或可执行 Azure PowerShell 的任何其他工具。

> [!NOTE]
> 一个重要注意事项是，如果要创建大量（10 个以上）自定义映像，则某些 PowerShell 文件的运行时间会很长。 免费托管的 DevOps 生成/发布代理的超时时间为 30 分钟，因此要开始生成许多映像时，就无法使用免费的托管代理。 此超时质询适用于你决定要使用的任何工具，最好先验证是否可以延长典型超时时间来长时间运行 Azure PowerShell 脚本。 如果使用 Azure DevOps，则可以使用付费托管代理，也可以使用自己的生成代理。

1. 若要开始使用，请在 DevOps 项目的主页上选择“设置生成”：

    ![“设置生成”按钮](./media/set-up-devops-lab/setup-build-button.png)
2. 指定生成的“名称”（例如：“生成映像并将其传送到开发测试实验室”）。
3. 选择空的生成定义，然后选择“应用”以创建生成。
4. 在此阶段，可以为生成代理选择“托管”。
5. “保存”生成定义。

    ![生成定义](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>配置生成变量
若要简化命令行参数，请将用于驱动映像工厂的关键值封装到一组生成变量中。 选择“变量”选项卡，将看到包含多个默认变量的列表。 下面是要在 Azure DevOps 中输入的变量的列表：


| 变量名 | 值 | 说明 |
| ------------- | ----- | ----- |
| ConfigurationLocation | /Scripts/ImageFactory/Configuration | 这是存储库中指向“Configuration”文件夹的完整路径。 如果导入了上述整个存储库，则左侧的值是正确的。 否则，进行更新以指向“Configuration”位置。 |
| DevTestLabName | MyImageFactory | Azure 开发测试实验室中的实验室名称，该实验室用作生成映像的工厂。 如果没有此名称，请创建一个。 确保实验室所在的订阅与服务终结点有权访问的订阅相同。 |
| ImageRetention | 1 | 要保存的每种类型的映像数。 将默认值设置为 1。 |
| MachinePassword | ******* | 虚拟机的内置管理员帐户密码。 这是临时帐户，因此务必确保安全。 选择右侧的小锁图标以确保它是安全字符串。 |
| MachineUserName | ImageFactoryUser | 虚拟机的内置管理员帐户用户名。 这是临时帐户。 |
| StandardTimeoutMinutes | 30 | 常规 Azure 操作应等待的超时时间。 |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | 实验室所在且服务终结点有权访问的订阅的 ID。 |
| VMSize | Standard_A3 | 用于执行“创建”步骤的虚拟机的大小。 创建的 VM 是临时的。 其大小必须是[为实验室启用](devtest-lab-set-lab-policy.md)的大小。 确认有足够的[订阅核心配额](../azure-resource-manager/management/azure-subscription-service-limits.md)。

![生成变量](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>连接到 Azure
下一步是设置服务主体。 这是 Azure Active Directory 中的一个身份，它允许 DevOps 生成代理代表用户在 Azure 中执行操作。 若要进行设置，请先添加 Azure PowerShell 第一个生成步骤。

1. 选择“添加任务”。
2. 搜索“Azure PowerShell”。
3. 找到后，选择“添加”以将此任务添加到生成。 执行此操作时，会看到任务在左侧显示为已添加。

![设置 PowerShell 步骤](./media/set-up-devops-lab/set-up-powershell-step.png)

若要设置服务主体，最快的方法是让 Azure DevOps 为我们执行此操作。

1. 选择刚才添加的任务。
2. 对于“Azure 连接类型”，选择“Azure 资源管理器”。 
3. 选择“管理”链接以设置服务主体。

有关详细信息，请参阅此 [博客文章](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)。 选择“管理”链接时，你会进入 DevOps 中正确的位置（博客文章中的第二个截图），以便设置到 Azure 的连接。 进行设置时，请确保选择“Azure 资源管理器服务终结点”。

## <a name="complete-the-build-task"></a>完成生成任务
如果选择生成任务，将在右侧窗格中看到应填写的所有详细信息。

1. 首先，命名生成任务：“创建虚拟机”。
2. 选择“Azure 资源管理器”来选择所创建的服务主体
3. 选择服务终结点。
4. 对于“脚本路径”，请选择右侧的“…”（省略号）。 
5. 导航到“MakeGoldenImageVMs.ps1”脚本。
6. 脚本参数应如下所示：`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![完成生成定义](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>让生成排队
将新生成排入队列来验证是否已正确设置所有内容。 运行生成时，切换到 [Azure 门户](https://portal.azure.com)，并在映像工厂实验室选择“所有虚拟机”，以确认一切是否正常工作。 应会看到实验室中创建了三个虚拟机。

![实验室中的 VM](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>后续步骤
已完成第一步，即基于 Azure 开发测试实验室设置映像工厂。 在本系列的下一篇文章中，会将 VM 通用化并保存到自定义映像。 然后，将这些映像分发到所有其他实验室。 请参阅本系列的下一篇文章：[保存自定义映像并将其分发到多个实验室](image-factory-save-distribute-custom-images.md)。
