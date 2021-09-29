---
title: 使用模板创建多 VM 环境和 PaaS 资源
description: 了解如何在 Azure 开发测试实验室中通过 Azure 资源管理器模板创建多 VM 环境和 PaaS 资源
ms.topic: how-to
ms.date: 08/12/2020
ms.openlocfilehash: c1a83a5e8f471b68d1c512443371a9a592cfbc88
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587984"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板创建多 VM 环境和 PaaS 资源

Azure 开发测试实验室环境允许用户在实验室范围内以一致的方式轻松部署复杂的基础结构。 可以使用 [Azure 资源管理器模板](../azure-resource-manager/templates/syntax.md)在开发测试实验室中创建具有资源集合的环境。 这些环境可以包含资源管理器模板可以创建的任何 Azure 资源。

通过使用 [Azure 门户](https://portal.azure.com)，你可以轻松地[一次将一个虚拟机 (VM) 添加](devtest-lab-add-vm.md)到实验室。 不过，多层级 web 应用或 SharePoint 场等方案需要一种机制才能一步创建多个 VM。 可以使用 Azure 资源管理器模板定义 Azure 解决方案的基础结构和配置，以一致的状态重复部署多个 VM。

Azure 资源管理器模板还具有以下优势：

- Azure 资源管理器模板是从 GitHub 或 Azure Repos 源代码管理存储库直接加载的。
- 用户可以在 Azure 门户中通过选择配置的 Azure 资源管理器模板来创建环境，就像对其他类型的 [VM 库](devtest-lab-comparing-vm-base-image-types.md)所做的一样。
- 可以通过 Azure 资源管理器模板在环境中预配 Azure PaaS 资源以及 IaaS VM。
- 除了其他类型的库创建的单个 VM 以外，还可以在实验室中跟踪环境的成本。 将创建 PaaS 资源并且显示在成本跟踪中。 不过，VM 自动关闭不适用于 PaaS 资源。

要了解有关使用资源管理器模板在单个操作中部署、更新或删除多个实验室资源的优势的详细信息，请参阅[使用资源管理器模板的好处](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager)。

> [!NOTE]
> 使用资源管理器模板作为创建实验室 VM 的基础时，创建多个 VM 还是创建单个 VM 之间存在一些差异。 有关详细信息，请参阅[使用虚拟机的 Azure 资源管理器模板](devtest-lab-use-resource-manager-template.md)。
>

## <a name="use-devtest-labs-public-environments"></a>使用开发测试实验室公共环境
Azure 开发测试实验室包含 [Azure 资源管理器模板的公共存储库](https://github.com/Azure/azure-devtestlab/tree/master/Environments)，可以使用此存储库来创建环境，而无需自行连接到外部 GitHub 源。 此公共存储库类似于你创建的每个实验室 Azure 门户中提供的工件的公共存储库。 利用环境存储库，你可以快速开始使用预创作环境模板，这些模板包含很少的参数。 这些模板提供了实验室内 PaaS 资源的流畅入门体验。

在公共存储库中，开发测试实验室团队和其他人已创建和共享了常用模板，如 Azure Web 应用、Service Fabric 群集和开发 SharePoint 场环境。 您可以直接使用这些模板，也可以根据您的需要进行自定义。 有关详细信息，请参阅[在开发测试实验室中配置和使用公共环境](devtest-lab-configure-use-public-environments.md)。 创建自己的模板后，可以将其存储在此存储库中，以便与他人共享，也可以设置自己的 Git 存储库。

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>配置自己的模板存储库

在基础结构即代码和配置即代码方面，最佳做法之一是在源代码管理中管理环境模板。 Azure 开发测试实验室遵循这种做法，它直接从 GitHub 或 Azure Repos 存储库加载所有 Azure 资源管理器模板。 因此，可在整个发布周期内使用资源管理器模板，无论是测试环境还是生产环境。

在存储库中整理 Azure 资源管理器模板时需遵循几条规则：

- 必须将主模板文件命名为 azuredeploy.json。

- 如果想要使用参数文件中定义的参数值，必须将参数文件命名为 azuredeploy.parameters.json。

  可以使用参数 `_artifactsLocation` 和 `_artifactsLocationSasToken` 来构造 parametersLink URI 的值，以允许开发测试实验室自动管理嵌套的模板。 有关详细信息，请参阅[部署用于测试环境的嵌套 Azure 资源管理器模板](deploy-nested-template-environments.md)。

- 你可以定义元数据以在名为 metadata.json 的文件中指定模板显示名称和说明，如下所示：

  ```json
  {
    "itemDisplayName": "<your template name>",
    "description": "<description of the template>"
  }
  ```

![关键的 Azure 资源管理器模板文件](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>将模板存储库添加到实验室

创建并配置存储库后，可以使用 Azure 门户将存储库添加到实验室：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 选择“所有服务”，并从列表中选择“开发测试实验室”。
1. 从实验室列表，选择所需的实验室。
1. 在实验室的“概览”窗格中，选择“配置和策略”。

   ![配置和策略](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. 从“配置和策略”设置列表中选择“存储库”。  将自动为所有实验室生成“公共项目存储库”存储库，并连接到[开发测试实验室公共 GitHub 存储库](https://github.com/Azure/azure-devtestlab)。

1. 要添加 Azure 资源管理器模板存储库，请选择“添加”。

   ![公共存储库](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. 在“存储库”窗格中，输入以下信息：

   - 名称：输入要在实验室中使用的存储库名称。
   - Git 克隆 URI：输入 GitHub 或 Azure 存储库中的 Git HTTPS 克隆 URL。
   - 分支（可选）：输入用于访问 Azure 资源管理器模板定义的分支名称。
   - 个人访问令牌：输入用于安全访问存储库的个人访问令牌。
     - 要从 Azure Repos 获取令牌，请在配置文件下选择“用户设置” > “安全” > “个人访问令牌”。  
     - 要从 GitHub 获取令牌，请在配置文件中选择“设置” > “开发人员设置” > “个人访问令牌”。  
   - 文件夹路径：输入文件夹路径，该路径相对于工件定义的 Git 克隆 URI，或者相对于 Azure 资源管理器模板定义。

1. 选择“保存”。

   ![添加新存储库](./media/devtest-lab-create-environment-from-arm/repo-values.png)

向实验室添加 Azure 资源管理器模板后，实验室用户可以使用模板创建环境。

## <a name="configure-access-rights-for-lab-users"></a>配置实验室用户的访问权限

默认情况下，实验室用户具有“读者”角色，因此无法更改环境资源组中的资源。 例如，他们无法停止或启动其资源。

要为实验室用户授予“参与者”角色以便用户能够编辑其环境中的资源，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中，在实验室的“概述”窗格中选择“配置和策略”，然后选择“实验室设置"。  

1. 在“实验室设置”窗格中，选择“参与者”，然后选择“保存”以向实验室用户授予写入权限。  

   ![可配置的实验室用户访问权限](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

下一部分将逐步讲解如何通过 Azure 资源管理器模板创建环境。

## <a name="create-environments-from-templates-in-the-azure-portal"></a>在 Azure 门户中通过模板创建环境

向实验室添加 Azure 资源管理器模板后，实验室用户可以通过以下步骤在 Azure 门户中创建环境：

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 选择“所有服务”，并从列表中选择“开发测试实验室”。

1. 从实验室列表，选择所需的实验室。

1. 在实验室的页面中，选择“添加”。

1. “选择库”窗格显示了可与最前面列出的 Azure 资源管理器模板配合使用的基本映像。 选择所需的 Azure 资源管理器模板。

   ![选择一个库](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. 在“添加”窗格上，输入要向环境用户显示的“环境名称”值。 

   Azure 资源管理器模板定义其余的输入字段。 如果该模板 azuredeploy.parameter.json 文件定义了默认值，则输入字段将显示这些值。

   对于安全字符串类型的参数，可以使用来自 Azure 密钥保管库的机密。 要了解如何在密钥保管库中存储机密并在创建实验室资源时使用这些机密，请参阅[在 Azure 密钥保管库中存储机密](devtest-lab-store-secrets-in-key-vault.md)。  

   ![添加窗格](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > 以下参数值不会出现在输入字段中（即使模板指定了这些值）。 相反，表单将会显示空白的输入字段，实验室用户在创建环境时必须在这些字段中输入值。
   >
   > - GEN-UNIQUE
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-KEY
   > - GEN-PASSWORD

1. 选择“添加”创建环境。

   环境随即会开始预配，其状态显示在“我的虚拟机”列表中。 实验室会自动创建一个新资源组，用于预配 Azure 资源管理器模板中定义的所有资源。

1. 创建环境后，请在“我的虚拟机”列表中选择该环境，打开资源组窗格并浏览该环境预配的所有资源。

   ![环境资源](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   还可以展开环境仅查看环境预配的 VM 列表。

   ![“我的虚拟机”列表](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. 选择任一环境可以查看可用的操作，例如，应用项目、附加数据磁盘、更改自动关闭时间，等等。

   ![环境操作](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>利用 PowerShell 自动执行环境创建

可以使用 Azure 门户向实验室添加单个环境，但当开发或测试方案必须创建多个环境时，自动部署的体验更好。

在继续操作之前，请确保你具有用于定义要创建的资源的 Azure 资源管理器模板。 [在 Git 存储库中添加并配置模板](#configure-your-own-template-repositories)，然后[将存储库添加到实验室](#add-template-repositories-to-the-lab)。

下面的示例脚本可在实验室中创建一个环境。 注释有助于你更好地理解脚本。

1. 将下面的示例 PowerShell 脚本保存到硬盘，并命名为 deployenv.ps1。

   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

   ```powershell
   #Requires -Module Az.Resources

   [CmdletBinding()]

   param (
   # ID of the Azure Subscription for the lab
   [string] [Parameter(Mandatory=$true)] $SubscriptionId,

   # Name of the existing lab in which to create the environment
   [string] [Parameter(Mandatory=$true)] $LabName,

   # Name of the connected repository in the lab
   [string] [Parameter(Mandatory=$true)] $RepositoryName,

   # Name of the template (folder name in the Git repository)
   [string] [Parameter(Mandatory=$true)] $TemplateName,

   # Name of the environment to be created in the lab
   [string] [Parameter(Mandatory=$true)] $EnvironmentName,

   # The parameters to be passed to the template. Each parameter is prefixed with "-param_".
   # For example, if the template has a parameter named "TestVMName" with a value of "MyVMName",
   # the string in $Params will have the form: -param_TestVMName MyVMName.
   # This convention allows the script to dynamically handle different templates.
   [Parameter(ValueFromRemainingArguments=$true)]
       $Params
   )

   # Sign in to Azure.
   # Comment out the following statement to completely automate the environment creation.
   Connect-AzAccount

   # Select the subscription that has the lab.  
   Set-AzContext -SubscriptionId $SubscriptionId | Out-Null

   # Get information about the user, specifically the user ID, which is used later in the script.  
   $UserId = $((Get-AzADUser -UserPrincipalName ((Get-AzContext).Account).Id).Id)

   # Get information about the lab, such as lab location.
   $lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName
   if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." }

   # Get information about the repository in the lab.
   $repository = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
       -ResourceName $LabName `
       -ApiVersion 2016-05-15 `
       | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." }

   # Get information about the Resource Manager template base for the environment.
   $template = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
       -ResourceName "$LabName/$($repository.Name)" `
       -ApiVersion 2016-05-15 `
       | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." }

   # Build the template parameters with parameter name and values.  
   $parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
   $templateParameters = @()

   # Extract the custom parameters from $Params and format as name/value pairs.
   $Params | ForEach-Object {
       if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
           $name = $Matches[1]                
       } elseif ( $name ) {
           $templateParameters += @{ "name" = "$name"; "value" = "$_" }
           $name = $null #reset name variable
       }
   }

   # Once name/value pairs are isolated, create an object to hold the necessary template properties.
   $templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; }

   # Now, create or deploy the environment in the lab by using the New-AzResource command.
   New-AzResource -Location $Lab.Location `
       -ResourceGroupName $lab.ResourceGroupName `
       -Properties $templateProperties `
       -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
       -ResourceName "$LabName/$UserId/$EnvironmentName" `
       -ApiVersion '2016-05-15' -Force

   Write-Output "Environment $EnvironmentName completed."
   ```

1. 按如下所示运行该脚本，并使用 Git 存储库中的 SubscriptionId、LabName、ResourceGroupName、Event.pushnotification.repositoryname、TemplateName（Git 存储库中的文件夹）和 EnvironmentName。

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

还可以使用 Azure CLI 通过资源管理器模板部署资源。 有关详细信息，请参阅[使用资源管理器模板和 Azure CLI 部署资源](../azure-resource-manager/templates/deploy-cli.md)。

> [!NOTE]
> 只有具有实验室所有者权限的用户才能使用 Azure PowerShell 根据 Resource Manager 模板创建 VM。 如果要使用资源管理器模板自动创建 VM，并且只具有用户权限，则可以使用 CLI 命令 [az lab vm create](/cli/azure/lab/vm#az_lab_vm_create)。

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>资源管理器模板在开发测试实验室中的限制

在开发测试实验室中使用资源管理器模板时，请注意以下限制：

- 如果实验室 VM 是通过资源管理器模板创建的，则无法通过此类实验室 VM 来创建公式或自定义图像。

- 在部署资源管理器模板时，大多数策略不会被评估。

例如，你可能有一个实验室策略，规定用户只能创建五个 VM。 但是，用户可以部署创建数十个 VM 的资源管理器模板。 不被评估的策略包括：

  - 每个用户的 VM 数

  - 每个实验室用户的高级 VM 数

  - 每个实验室用户的高级磁盘数

## <a name="next-steps"></a>后续步骤
- 创建 VM 后，可以通过在 VM 的管理窗格中选择“连接”来连接到该 VM。
- 在实验室的“我的虚拟机”列表中选择环境，查看和管理该环境中的资源。
- 浏览 [Azure 快速入门模板库中的 Azure 资源管理器模板](https://github.com/Azure/azure-quickstart-templates)。