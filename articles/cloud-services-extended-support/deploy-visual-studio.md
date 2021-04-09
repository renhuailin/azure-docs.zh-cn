---
title: 使用云服务（外延支持）（预览）
description: 立即了解如何通过 Visual Studio 使用 Azure 资源管理器创建和部署 Azure 云服务
author: ghogen
ms.service: cloud-services-extended-support
manager: jillfra
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: tutorial
ms.date: 10/5/2020
ms.author: ghogen
ms.openlocfilehash: 80aa160c53b278137467dba2afa41384c7c4f378
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101722664"
---
# <a name="create-and-deploy-a-azure-cloud-service-extended-support-using-visual-studio"></a>使用 Visual Studio 创建和部署 Azure 云服务（外延支持）

从 [Visual Studio 2019 版本 16.9](https://visualstudio.microsoft.com/vs/preview/)（目前提供预览版）开始，可以使用 Azure 资源管理器 (ARM) 来处理云服务，这将极大地简化对 Azure 资源的维护和管理并实现其现代化。 此功能由称为云服务（外延支持）的新的 Azure 服务提供支持。 可以将现有的云服务发布到云服务（外延支持）。 有关此 Azure 服务的信息，请参阅[云服务（外延支持）文档](overview.md)。

> [!IMPORTANT]
> 云服务（外延支持）目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="register-the-feature-for-your-subscription"></a>为订阅注册此功能
云服务（外延支持）目前以预览版提供。 为订阅注册此功能，如下所示：

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```
有关详细信息，请参阅[部署云服务（外延支持）的先决条件](deploy-prerequisite.md)

## <a name="create-a-project"></a>创建一个项目

Visual Studio 提供一种项目模板，可用于创建具有外延支持的 Azure 云服务，名为 Azure 云服务（外延支持）。 云服务是一种简单的通用 Azure 服务。 创建项目后，可通过 Visual Studio 配置和调试云服务，并将其部署到 Azure。

### <a name="to-create-an-azure-cloud-service-extended-support-project-in-visual-studio"></a>在 Visual Studio 中创建 Azure 云服务（外延支持）

本节介绍如何在 Visual Studio 中创建具有一个或多个 Web 角色的 Azure 云服务项目。

1. 在“启动”窗口中，选择“创建新项目”。

1. 在搜索框中，键入“云”，然后选择“Azure 云服务(外延支持)”。

   ![具有外延支持的新的 Azure 云服务](./media/choose-project-template.png)

1. 为项目提供名称，然后选择“创建”。

   ![为项目提供名称](./media/configure-new-project.png)

1. 在“新建 Microsoft Azure 云服务”对话框中，选择要添加的角色，然后选择右箭头按钮以将其添加到解决方案。

    ![选择新的 Azure 云服务角色](./media/choose-roles.png)

1. 要重命名已添加的角色，请在“新建 Microsoft Azure 云服务”对话框中将鼠标悬停在该角色上，然后从上下文菜单中选择“重命名”。 还可在添加角色后在解决方案（**解决方案资源管理器** 中）内对其进行重命名。

    ![重命名 Azure 云服务角色](./media/new-cloud-service-rename.png)

Visual Studio Azure 项目与解决方案中的角色项目具有关联。 该项目还包括服务定义文件和服务配置文件：

- **服务定义文件** - 定义了应用程序的运行时设置，包括所需角色、终结点和虚拟机大小。
- **服务配置文件** - 配置了角色有多少实例在运行以及为角色定义的设置的值。

有关这些文件的详细信息，请参阅[使用 Visual Studio 配置 Azure 云服务的角色](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service)。

## <a name="publish-a-cloud-service"></a>发布云服务

1. 在 Visual Studio 中创建或打开 Azure 云服务项目。

1. 在“解决方案资源管理器”中右键单击项目，并从上下文菜单中选择“发布”。

   ![登录页](./media/publish-step-1.png)

1. **帐户** - 选择一个帐户，或者在帐户下拉列表中选择“添加帐户”。

1. **选择订阅** - 选择要用于部署的订阅。 用于部署云服务（外延支持）的订阅需要具有通过基于角色的访问控制 (RBAC) 分配的所有者或参与者角色。 如果订阅不具有这些角色中的任何一种角色，请参阅[角色分配的添加步骤](../role-based-access-control/role-assignments-steps.md)来添加角色，然后再继续操作。

1. 选择“下一步”，移动到“设置”页 。

   ![通用设置](./media/publish-settings.png)

1. **云服务** - 使用下拉列表选择现有的云服务，或者选择“新建”，然后创建一个云服务。 每项云服务的数据中心均显示在括号中。 建议云服务的数据中心位置与存储帐户的数据中心位置相同。

   如果选择创建新的云服务，将显示“创建云服务(外延支持)”对话框。 指定要用于云服务的位置和资源组。

   ![创建具有外延支持的云服务](./media/extended-support-dialog.png)

1. **生成配置** - 选择“调试”或“发布”。

1. **服务配置** - 选择“云”或“本地”。

1. **存储帐户** - 选择要用于此部署的存储帐户，或者单击“新建”以创建一个存储帐户。 每个存储帐户的区域均显示在括号中。 建议存储帐户的数据中心位置与云服务的数据中心位置相同（常用设置）。

   Azure 存储帐户将存储应用程序部署的包。 部署应用程序之后，将从存储帐户中删除该包。

1. **Key Vault** - 指定包含此云服务的机密的 Key Vault。 如果启用了远程桌面或已将证书添加到配置中，则会启用此功能。

1. **为所有角色启用远程桌面** - 如果希望能够远程连接到服务，请选中此选项。 系统会要求你指定凭据。

   ![远程桌面设置](./media/remote-desktop-configuration.png)

1. 选择“下一步”，移动到“诊断设置”页面 。

   ![诊断设置](./media/diagnostics-settings.png)

   通过诊断，可以对 Azure 云服务（或 Azure 虚拟机）进行故障排除。 有关诊断的详细信息，请参阅 [Configuring Diagnostics for Azure Cloud Services and Virtual Machines](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)（为 Azure 云服务和虚拟机配置诊断）。 有关 Application Insights 的信息，请参阅[什么是 Application Insights？](../azure-monitor/app/app-insights-overview.md)。

1. 选择“下一步”，移动到“摘要”页面 。

   ![总结](./media/publish-summary.png)

1. **目标配置文件** - 可以选择基于所选的设置创建发布配置文件。 例如，可以创建一个配置文件用于测试环境，并创建另一个配置文件用于生产环境。 要保存此配置文件，请选择 **“保存”** 图标。 向导将创建配置文件并将它保存在 Visual Studio 项目中。 要修改配置文件名称，请打开“目标配置文件”列表，并选择“管理…” 。

   > [!Note]
   > 发布配置文件将出现在 Visual Studio 的解决方案资源管理器中，配置文件设置将写入扩展名为.azurePubxml 的文件。 设置将保存为 XML 标记的属性。

1. 配置项目部署的所有设置后，请选择对话框底部的“发布”。 可以在 Visual Studio 的“Azure 活动日志”输出窗口中监视过程状态。

祝贺你！ 已将外延支持云服务项目发布到 Azure。 若要使用相同设置再次进行发布，可以重复使用发布配置文件，或重复这些步骤来创建新的配置文件。

## <a name="clean-up-azure-resources"></a>清理 Azure 资源

若要清理在本教程中创建的 Azure 资源，请转到 [Azure 门户](https://portal.azure.com)，选择“资源组”，找到并打开用于创建服务的资源组，然后选择“删除资源组” 。

## <a name="next-steps"></a>后续步骤

使用“发布”屏幕上的“配置”按钮设置持续集成 (CI) 。 有关详细信息，请参阅 [Azure Pipelines 文档](/azure/devops/pipelines)。