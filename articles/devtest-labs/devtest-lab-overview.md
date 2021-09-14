---
title: 关于 Azure 开发测试实验室 | Microsoft Docs
description: 了解开发测试实验室如何轻松地创建、管理和监视 Azure 虚拟机
ms.topic: article
ms.date: 08/20/2021
ms.openlocfilehash: 5ef1aabb861b90679fbb36e46b837d6567f122d4
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123468035"
---
# <a name="about-azure-devtest-labs"></a>关于 Azure 开发测试实验室
Azure 开发测试实验室使团队中的开发人员能够高效地自行管理虚拟机 (VM) 和 PaaS 资源，而无需等待审批。

开发测试实验室可创建由预配置的基础或 Azure 资源管理器模板组成的实验室。 这些实验室具有可用于创建环境的所有必要工具和软件。 你可以在几分钟内创建环境，而不需要几小时或几天。

通过使用开发测试实验室，可以通过执行以下任务来测试应用程序的最新版本：

- 通过使用可重用模板和项目，快速预配 Windows 和 Linux 环境。
- 轻松地将部署管道与开发测试实验室集成，以预配按需环境。
- 通过预配多个测试代理来纵向扩展负载测试，并为训练和演示创建预预配的环境。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>功能
开发测试实验室为使用 VM 的开发人员提供以下功能：

- 只需执行不到五个简单的步骤即可快速创建 VM。
- 从 VM 库的策展列表中进行选择，这些模板由团队主管或中央 IT 配置、批准和授权。
- 从预创建的自定义映像创建 VM，这些映像已安装所有软件和工具。 
- 根据本质上是自定义映像的公式创建 VM，并结合使用创建 VM 时安装的软件的最新版本。 
- 预配 VM 后，安装作为扩展部署在 VM 上的项目。
- 在 VM 上设置自动关闭和自动启动计划。
- 在不经历创建过程的情况下声明一个预创建的 VM。

开发测试实验室为使用 PaaS 环境的开发人员提供以下功能：

- 只需执行不到三个简单的步骤即可使用资源管理器快速创建 PaaS 环境。
- 从资源管理器模板模板的策展列表中进行选择，这些模板由团队主管或中央 IT 配置和授权。
- 通过使用资源管理器模板启动空的资源组（沙盒），以在实验室环境中浏览 Azure。

通过执行以下任务，开发测试实验室还使中央 IT 能够控制浪费、优化资源成本并使成本保持在预算范围内：  

- 在 VM 上设置自动关闭和自动启动计划。
- 对用户可创建的 VM 数量设置相应策略。
- 对可供用户选择的 VM 大小和库映像设置相应策略。
- 跟踪实验室成本并设定实验室目标。
- 如果实验室预计成本较高，则则提供通知，以便可采取必要措施。

开发测试实验室为创建、配置和管理云中的环境提供了以下优势。

## <a name="cost-control-and-governance"></a>成本控制和管理
借助开发测试实验室，你可以执行以下任务，从而更轻松地控制成本：

- [针对实验室设置策略](devtest-lab-set-lab-policy.md)，例如每个用户或每个实验室的 VM 数。 
- 创建[策略以自动关闭](devtest-lab-set-lab-policy.md)和启动 VM。
- 跟踪在实验室内部运行的 VM 和 PaaS 资源的成本，使其保持在[预算](devtest-lab-configure-cost-management.md)范围内。
- 保持在实验室环境中，这样就不会在实验室外部启动资源。

## <a name="quickly-get-to-ready-to-test"></a>快速转至测试就绪
通过开发测试实验室可以创建预配置的环境，其中包含团队开发和测试应用程序所需的所有内容。 只需[声明安装了应用程序的最新版本的环境](devtest-lab-add-claimable-vm.md)，就可以开始使用。 或者，使用容器来更快速、更高效地创建环境。

## <a name="create-once-use-everywhere"></a>一次创建，随处可用
在团队或组织内部捕获并共享 PaaS [环境模板](devtest-lab-create-environment-from-arm.md)和[项目](add-artifact-repository.md)（均处于源代码管理下），从而轻松地创建开发和测试环境。

## <a name="worry-free-self-service"></a>无忧的自助服务
使用开发测试实验室，开发人员和测试人员可以通过使用一组预配置的资源快速轻松地[创建 IaaS VM](devtest-lab-add-vm.md) 和 [PaaS 资源](devtest-lab-create-environment-from-arm.md)。

## <a name="use-iaas-and-paas-resources"></a>使用 IaaS 和 PaaS 资源 
开发人员还可以使用资源管理器模板来启动 PaaS 资源，例如 Azure Service Fabric 群集、Azure 应用服务的 Web 应用功能以及 SharePoint 场。 若要在实验室中开始使用 PaaS，请使用[公共环境存储库](devtest-lab-configure-use-public-environments.md)中的模板，或[将实验室连接到自己的 Git 存储库](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories)。 还可以跟踪这些资源的成本，使其保持在预算范围内。

## <a name="integrate-with-your-existing-toolchain"></a>与现有工具链集成
使用预制的插件或 API 直接从首选的[持续集成 (CI) 工具](devtest-lab-integrate-ci-cd.md)、集成开发环境 (IDE) 或自动发布管道中预配开发/测试环境。 还可以使用综合命令行工具。

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- 若要了解有关开发测试实验室的详细信息，请参阅[开发测试实验室概念](devtest-lab-concepts.md)。
- 有关分步说明的演练，请参阅[教程：使用 Azure 开发测试实验室设置实验室](tutorial-create-custom-lab.md)。
