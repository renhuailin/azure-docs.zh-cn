---
title: 使用 Azure Functions 扩展 Azure 开发测试实验室
description: 了解如何使用 Azure Functions 扩展 Azure 开发测试实验室。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 8a6200dbfce99ee7904dc1a65965e95d81e98471
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128623639"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>使用 Azure Functions 扩展开发测试实验室
可以使用 Azure Functions 来支持开发测试实验室已支持的方案以外的其他方案。 Azure Functions 可用于扩展服务的内置功能，以满足特定于业务的需求。 以下列表提供了一些可能的方案。 本文介绍如何实现其中的一个示例方案。

- 提供实验室中虚拟机 (VM) 的顶级摘要
- [将实验室配置为使用远程桌面网关](configure-lab-remote-desktop-gateway.md)
- 内部支持页上的合规性报告
- 使用户能够完成订阅中需要增加权限的操作
- [根据开发测试实验室事件启动工作流](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>概述
[Azure Functions](../azure-functions/functions-overview.md) 是 Azure 中的无服务器计算平台。 通过在解决方案中结合使用 Azure Functions 和开发测试实验室，可以使用自己的自定义代码来扩展现有功能。 有关 Azure Functions 的详细信息，请参阅 [Azure Functions 文档](../azure-functions/functions-overview.md)。 为了说明如何借助 Azure Functions 满足需求或完成开发测试实验室中的方案，本文使用一个示例来展示有关实验室中 VM 的概括性摘要，如下所示：

**示例要求/方案**：用户可以查看实验室中所有 VM 的详细信息，包括操作系统、所有者和任何已应用的项目。  此外，如果最近未应用“应用 Windows 更新”项目，可以使用一种简单的方法来应用它。

为完成该方案，需要使用下图中所述的两个函数：  

![总体流](./media/extend-devtest-labs-azure-functions/flow.png)

这些示例函数的源代码位于[开发测试实验室 GitHub 存储库](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions)中（C# 和 PowerShell 实现均可使用）。

- **UpdateInternalSupportPage**：此函数查询开发测试实验室，并直接使用虚拟机的详细信息更新内部支持页。
- **ApplyWindowsUpdateArtifact**：此函数会为实验室中某个 VM 应用“Windows 更新”项目。

## <a name="how-it-works"></a>工作原理
当用户在开发测试实验室中选择“内部支持”页时，会生成一个预先填充的页，其中包含有关 VM、实验室所有者和支持联系人的信息。  

选择“选择刷新此处”按钮时，该页将调用第一个 Azure 函数：UpdateInternalSupportPage 。 函数向开发测试实验室查询信息，然后用新信息重写“内部支持”页。

对于最近未应用 Windows 更新项目的任何 VM，还可以执行其他操作，会有一个按钮，可用于向 VM 应用 Windows 更新。 为 VM 选择“运行 Windows 更新”按钮时，该页将调用第二个 Azure 函数：ApplyWindowsUpdateArtifact 。 此函数检查虚拟机是否正在运行，如果是，则直接应用 [Windows 更新](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates)项目。

## <a name="step-by-step-walkthrough"></a>分步演练
本部分提供设置更新“内部支持”页所需的 Azure 资源的分步说明。 本演练提供了一个扩展开发测试实验室的示例。 可以将此模式用于其他方案。

### <a name="step-1-create-a-service-principal"></a>步骤 1：创建服务主体 
第一步是获取有权访问包含实验室的订阅的服务主体。 服务主体必须使用基于密码的身份验证。 可以通过 [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli)、[Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps) 或 [Azure 门户](../active-directory/develop/howto-create-service-principal-portal.md)完成此操作。 如果已有一个可用的服务主体，可以跳过此步骤。

记下服务主体的“应用程序 ID”、“密钥”和“租户 ID”  。 本演练后面的步骤中会用到这些信息。 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>步骤 2：下载示例，并在 Visual Studio 2019 中打开
在本地下载 [C# Azure Functions 示例](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp)的副本（通过克隆存储库或从[此处](https://github.com/Azure/azure-devtestlab/archive/master.zip)下载存储库）。  

1. 使用 Visual Studio 2019 打开示例解决方案。  
1. 如果尚未安装适用于 Visual Studio 的 Azure 开发工作负载，请安装它。 可以通过“工具” -> “获取工具和功能”菜单项进行安装 。

    ![Azure 开发工作负载](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. 生成解决方案。 选择“生成”，然后选择“生成解决方案”菜单项 。

### <a name="step-3-deploy-the-sample-to-azure"></a>步骤 3：将示例部署到 Azure
在 Visual Studio 的“解决方案资源管理器”窗口中，右键单击“AzureFunctions”项目，然后选择“发布”  。 按照向导完成：发布到新的或现有 Azure 函数应用。 有关使用 Visual Studio 开发和部署 Azure Functions 的详细信息，请参阅[使用 Visual Studio 开发 Azure Functions](../azure-functions/functions-develop-vs.md)。

![“发布”对话框](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>步骤 4：收集应用程序设置
发布函数后，需要从 Azure 门户获取这些函数的 URL。 

1. 导航到 [Azure 门户](https://portal.azure.com)。 
1. 找到函数应用。
1. 在“函数应用”页上，选择函数。 
1. 选择“获取函数 URL”，如下图所示。 

    ![Azure 函数 URL](./media/extend-devtest-labs-azure-functions/function-url.png)
4. 复制并保存 URL。 对于其他 Azure 函数，请重复以上步骤。 

还需要关于服务主体的其他信息，例如应用程序 ID、密钥和租户 ID。


### <a name="step-5--update-application-settings"></a>步骤 5：更新应用程序设置
在 Visual Studio 中发布 Azure 函数后，在“操作”下选择“编辑 Azure 应用服务设置” 。 更新以下应用程序设置（远程）：

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- WindowsUpdateAllowedDays（默认为 7）
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![应用程序设置](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>步骤 6：测试 Azure 函数
本演练的最后一步是测试 Azure 函数。  

1. 导航到在步骤 3 中创建的函数应用中的“UpdateInternalSupportPage”函数。 
1. 选择页面右侧的“测试”。 
1. 在路由属性中输入（LABNAME、RESOURCEGROUPNAME 和 SUBSCRIPTIONID）。
1. 选择“运行”以执行该函数。  

    此函数将更新指定实验室的内部支持页。 它还包括一个按钮，供用户下次直接调用该函数

    ![测试函数](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>后续步骤
Azure Functions 可以帮助扩展开发测试实验室功能，使其拥有比原有内置功能更多的功能，并帮助客户满足其团队的独特要求。 可进一步扩展此模式，以涵盖更多的内容。  若要了解有关开发测试实验室的详细信息，请参阅以下文章： 

- [开发测试实验室企业参考体系结构](devtest-lab-reference-architecture.md)
- [常见问题解答](devtest-lab-faq.yml)
- [扩展开发测试实验室](devtest-lab-guidance-scale.md)
- [使用 PowerShell 实现开发测试实验室自动化](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests)
