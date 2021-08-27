---
title: 教程：通过 GitHub 实现 Azure Policy as Code
description: 本教程介绍如何通过导出、GitHub 操作和 GitHub 工作流实现 Azure Policy as Code 工作流
ms.date: 08/17/2021
ms.topic: tutorial
ms.openlocfilehash: 4bfbdf20fb0189f36832b40b1a924c5fbdc4fed9
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324707"
---
# <a name="tutorial-implement-azure-policy-as-code-with-github"></a>教程：通过 GitHub 实现 Azure Policy as Code

通过使用 Azure Policy as Code 工作流，可以将策略定义和分配作为代码进行管理，控制更新这些定义的生命周期并自动验证符合性结果。 在本教程中，你将了解如何通过 GitHub 使用 Azure Policy 功能来生成生命周期过程。 这些任务包括：

> [!div class="checklist"]
> - 将策略定义和分配导出到 GitHub
> - 将 GitHub 中更新的策略对象推送到 Azure
> - 通过 GitHub 操作触发符合性扫描

要分配一个策略用于识别现有资源的当前符合性状态，请参阅快速入门文章。

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
- 查看[设计 Azure Policy as Code 工作流](../concepts/policy-as-code.md)，以了解本教程中使用的设计模式。

### <a name="export-azure-policy-objects-from-the-azure-portal"></a>从 Azure 门户导出 Azure Policy 对象

要从 Azure 门户导出策略定义，请按照以下步骤操作：

1. 在 Azure 门户中单击“所有服务”，然后搜索并选择“策略”，启动 Azure Policy 服务。 

1. 选择“Azure Policy”页左侧的“定义”。

1. 使用“导出定义”按钮或选择策略定义行中的省略号，然后选择“导出定义” 。

1. 选择“使用 GitHub 登录”按钮。 如果尚未通过 GitHub 进行身份验证并授权 Azure Policy 导出资源，请在打开的新窗口中查看 [GitHub 操作](https://github.com/features/actions)所需的访问权限，然后选择“授权 AzureGitHubActions”以继续执行导出流程。 完成后，新窗口会自行关闭。

1. 在“基本信息”选项卡上，设置以下选项，然后选择“策略”选项卡或页面底部的“下一步:  策略”按钮。

   - **存储库筛选器**：设置为“我的存储库”以仅查看自己拥有的存储库，或设置为“所有存储库”以查看为 GitHub 操作授予了访问权限的所有存储库 。
   - **存储库**：设置为要将 Azure Policy 资源导出到的存储库。
   - **分支**：在存储库中设置分支。 在将更新进一步合并到源代码中之前，非常适合使用非默认分支来验证更新。
   - **目录**：要将 Azure Policy 资源导出到的根级别文件夹。 此目录下的子文件夹基于导出的资源而创建。

1. 在“策略”选项卡上，选择省略号，然后选择管理组、订阅或资源组的组合，以设置搜索范围。

1. 使用“添加策略定义”按钮在相关范围中搜索要导出的对象。 在打开的侧窗口中，选择要导出的每个对象。 根据搜索框或类型筛选选定内容。 选择所有要导出的对象后，使用页面底部的“添加”按钮。

1. 对于每个选定对象，为策略定义选择所需的导出选项，例如“仅定义”或“定义和分配” 。 然后选择“查看 + 导出”选项卡或页面底部的“下一步: 查看 + 导出”按钮。

   > [!NOTE]
   > 如果选择“定义和分配”选项，则仅导出添加策略定义时通过筛选器设置的范围内的策略分配。

1. 在“查看 + 导出”选项卡上，检查详细信息是否匹配，然后使用页面底部的“导出”按钮 。

1. 检查 GitHub 存储库、分支和根级别文件夹，查看所选资源现是否已导出到源代码管理。

将 Azure Policy 资源导出到所选 GitHub 存储库和根级别文件夹内的以下结构中：

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

### <a name="push-policy-objects-updated-in-github-to-azure"></a>将 GitHub 中更新的策略对象推送到 Azure

1. 导出策略对象时，系统还会创建一个名为 `.github/workflows/manage-azure-policy-<randomLetters>.yml` 的 [GitHub 工作流](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows)文件，以便你开始操作。

   > [!NOTE]
   > 每次使用导出都会创建 GitHub 工作流文件。 文件的每个实例都特定于该导出操作期间的选项。

1. 此工作流文件使用[管理 Azure Policy](https://github.com/marketplace/actions/manage-azure-policy) 操作将对 GitHub 存储库中导出的策略对象所做的更改推送回 Azure Policy。 默认情况下，该操作仅考虑并同步那些与 Azure 中的现有文件不同的文件。 还可以在操作中使用 `assignments` 参数，以只同步对特定分配文件所做的更改。 此参数可用于仅对特定环境应用策略分配。 有关详细信息，请参阅[管理 Azure Policy 存储库自述文档](https://github.com/Azure/manage-azure-policy)。

1. 默认情况下，必须手动触发工作流。 为此，请使用 GitHub 中的“操作”，依次选择 `manage-azure-policy-<randomLetters>` 工作流和“运行工作流”，然后再次选择“运行工作流”  。

   :::image type="content" source="../media/policy-as-code-github/manually-trigger-workflow.png" alt-text="GitHub Web 界面中的“操作”选项卡、工作流和“运行工作流”按钮的屏幕截图。":::

   > [!NOTE]
   > 只能检测和手动运行位于默认分支中的工作流文件。

1. 工作流通过 Azure 同步对策略对象的更改，然后在日志中显示状态。

   :::image type="content" source="../media/policy-as-code-github/workflow-logging.png" alt-text="运行中的工作流和记录到日志中的详细信息的屏幕截图。":::

1. 工作流还会将详细信息添加到 Azure Policy 对象 `properties.metadata` 中，以便用户跟踪。

   :::image type="content" source="../media/policy-as-code-github/updated-definition-metadata.png" alt-text="Azure 门户中 Azure Policy 定义的屏幕截图，其中更新了特定于 GitHub 操作的元数据。":::

### <a name="trigger-compliance-scans-using-github-action"></a>使用 GitHub 操作触发符合性扫描

通过使用 [Azure Policy 符合性扫描操作](https://github.com/marketplace/actions/azure-policy-compliance-scan)，可以从 [GitHub 工作流](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows)触发对一个或多个资源、资源组或订阅的按需符合性评估扫描，并基于这些资源的符合性状态更改工作流路径。 还可以将工作流配置为在计划的时间运行，以在方便时获取最新的符合性状态。 （可选）此 GitHub 操作还可以生成有关已扫描资源的符合性状态报告，以用于进一步分析或存档。

以下示例对订阅运行符合性扫描。

```yaml

on:
  schedule:
    - cron:  '0 8 * * *'  # runs every morning 8am
jobs:
  assess-policy-compliance:
    runs-on: ubuntu-latest
    steps:
    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{secrets.AZURE_CREDENTIALS}}

    - name: Check for resource compliance
      uses: azure/policy-compliance-scan@v0
      with:
        scopes: |
          /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

## <a name="review"></a>审阅

在本教程中，你已成功完成以下任务：

> [!div class="checklist"]
> - 将策略定义和分配导出到 GitHub
> - 将 GitHub 中更新的策略对象推送到 Azure
> - 从 GitHub 操作触发合规性扫描

## <a name="next-steps"></a>后续步骤

若要了解有关策略定义结构的详细信息，请查看以下文章：

> [!div class="nextstepaction"]
> [Azure Policy 定义结构](../concepts/definition-structure.md)
