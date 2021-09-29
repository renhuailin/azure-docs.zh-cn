---
title: 将 Azure 开发测试实验室集成到 Azure Pipelines 中
description: 了解如何将 Azure 开发测试实验室集成到 Azure 管道持续集成和交付管道
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: c226523e9239b096cfd04990833d7c6cb1290400
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128641345"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-pipelines-cicd-pipeline"></a>将 Azure 开发测试实验室集成到 Azure Pipelines CI/CD 管道中

可以使用 Azure DevTest Labs Tasks 扩展将 Azure Pipelines 持续集成和持续交付 (CI/CD) 生成与发布管道集成到 Azure 开发测试实验室。 该扩展会安装多个任务，包括： 

- 创建虚拟机 (VM)
- 从 VM 创建自定义映像
- 删除 VM 

通过这些任务可以轻松完成各种操作，例如，为特定测试任务快速部署黄金映像 VM，并在测试完成后删除该 VM。

本文将介绍如何使用 Azure DevTest Labs Tasks 以一个发布管道的形式创建和部署 VM、创建自定义映像，然后删除该 VM。 你通常会在自己的自定义生成、测试和部署管道中单独执行每个任务。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="prerequisites"></a>先决条件

- 注册或登录到 [Azure DevOps](https://dev.azure.com) 组织，然后在该组织中[创建一个项目](/vsts/organizations/projects/create-project)。 
  
- 从 Visual Studio Marketplace 安装 Azure DevTest Labs Tasks 扩展。
  
  1. 转到 [Azure 开发测试实验室任务](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)。
  1. 选择“免费获取”。
  1. 从下拉列表中选择你的 Azure DevOps 组织，然后选择“安装”。 
  
## <a name="create-the-template-to-build-an-azure-vm"></a>创建模板以生成 Azure VM 

本部分介绍如何创建用于按需创建 Azure VM 的 Azure 资源管理器模板。

1. 若要在订阅中创建资源管理器模板，请遵循[使用资源管理器模板](devtest-lab-use-resource-manager-template.md)中的过程。
   
1. 在生成资源管理器模板之前，请在创建 VM 的过程中添加 [WinRM 项目](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm)。 “Azure 文件复制”和“目标计算机上的 PowerShell”等部署任务需要 WinRM 访问权限 。 WinRM 项目需要使用一个主机名作为参数，该主机名应是 VM 的完全限定域名 (FQDN)。 
   
   > [!NOTE]
   > 使用具有共享 IP 地址的 WinRM 时，必须添加 NAT 规则，用于将外部端口映射到 WinRM 端口。 如果创建使用公共 IP 地址的 VM，则不需要添加 NAT 规则。
   
   
1. 使用 CreateVMTemplate.json 作为文件名，将模板保存在计算机上。
   
1. 将该模板签入到源代码管理系统。

## <a name="create-a-script-to-get-vm-properties"></a>创建脚本以获取 VM 属性

在发布管道中运行“Azure 文件复制”或“目标计算机上的 PowerShell”等任务步骤时，以下脚本会收集将应用部署到 VM 时所需的值 。 这些任务通常用于将应用部署到 Azure VM。 这些任务需要 VM 资源组名称、IP 地址和 FQDN 等值。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要创建脚本文件，请执行以下操作：

1. 打开文本编辑器，并在其中粘贴以下脚本。
   
   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. 使用类似于 GetLabVMParams.ps1 的名称保存文件，然后将其签入到源代码管理系统。 

## <a name="create-a-release-pipeline-in-azure-pipelines"></a>在 Azure 管道中创建发布管道

若要创建新的发布管道，请执行以下操作：

1. 在 Azure DevOps 项目页的左侧导航栏中，选择“管道” > “发布” 。
1. 选择“新建管道”。
1. 在“选择模板”下，向下滚动并选择“空作业”，然后选择“应用”  。

### <a name="add-and-set-variables"></a>添加并设置变量

管道任务将使用你在 Azure 门户中创建资源管理器模板时分配给 VM 的值。 

若要为值添加变量，请执行以下操作： 

1. 在管道页上，选择“变量”选项卡。
   
1. 对于每个变量，请选择“添加”并输入名称和值：
   
   |名称|值|
   |---|---|
   |*vmName*|在资源管理器模板中分配的 VM 名称|
   |*userName*|用于访问 VM 的用户名|
   |*password*|用户名的密码。 选择锁形图标来隐藏和保护密码。

### <a name="create-a-devtest-labs-vm"></a>创建开发测试实验室 VM

下一步是创建黄金映像 VM 用于将来的部署。 你将使用“Azure 开发测试实验室 - 创建 VM”任务在 Azure 开发测试实验室实例中创建该 VM。

1. 在发布管道的“管道”选项卡上，选择“阶段 1”中带超链接的文本以查看阶段任务，然后选择“代理作业”旁边的加号 (+) 图标    。 
   
1. 在“添加任务”下，依次选择“Azure 开发测试实验室 - 创建 VM”、“添加”  。 
   
1. 在左侧窗格中选择“创建 Azure 开发测试实验室 VM”。 

1. 在右窗格中填充表单，如下所示：
   
   |字段|值|
   |---|---|
   |Azure RM 订阅|在“可用 Azure 服务连接”或“可用 Azure 订阅”的下拉列表中选择一个服务连接或订阅，并根据需要选择“授权”  。<br /><br />注意：有关与 Azure 订阅建立权限受限程度更高的连接的信息，请参阅 [Azure 资源管理器服务终结点](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)。|
   |实验室名称|选择要在其中创建实验室 VM 的现有实验室的名称。|
   |模板名称|输入已保存到源代码存储库的模板文件的完整路径和名称。 可以使用内置属性来简化路径，例如：<br /><br />`$(System.DefaultWorkingDirectory)/Templates/CreateVMTemplate.json`|
   |模板参数|输入前面定义的变量的参数：<br /><br />`-newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)`|
   |**输出变量** > **实验室 VM ID**|输入创建的实验室 VM ID 的变量。 如果使用默认的 labVMId，可以在后续任务中以 $(labVMId) 形式引用该变量。<br /><br />可以创建非默认的名称，但请记得在后续任务中使用正确的名称。 可以写入以下格式的实验室 VM ID：<br /><br />`/subscriptions/{subscription Id}/resourceGroups/{resource group Name}/providers/Microsoft.DevTestLab/labs/{lab name}/virtualMachines/{vmName}`|

### <a name="collect-the-details-of-the-devtest-labs-vm"></a>收集开发测试实验室 VM 的详细信息

执行之前创建的脚本，收集开发测试实验室 VM 的详细信息。 

1. 在发布管道的“管道”选项卡上，选择“阶段 1”中带超链接的文本以查看阶段任务，然后选择“代理作业”旁边的加号 (+) 图标    。 
   
1. 在“添加任务”下，依次选择“Azure PowerShell”、“添加”  。 
   
1. 在左侧窗格中选择“Azure PowerShell 脚本: FilePath”。 
   
1. 在右窗格中填充表单，如下所示：
   
   |字段|值|
   |---|---|
   |Azure 连接类型|选择“Azure 资源管理器”。|
   |**Azure 订阅**|选择你的服务连接或订阅。| 
   |脚本类型|选择“脚本文件路径”。|
   |脚本路径|输入已保存到源代码存储库的 PowerShell 脚本的完整路径和名称。 可以使用内置属性来简化路径，例如：<br /><br />`$(System.DefaultWorkingDirectory/Scripts/GetLabVMParams.ps1`|
   |**脚本参数**|输入前一个任务填充的 labVmId 变量的名称，例如：<br /><br />`-labVmId '$(labVMId)'`|

该脚本将收集所需的值，并将其存储在发布管道中的环境变量内，因此你可以在后续步骤中轻松引用它们。

### <a name="create-a-vm-image-from-the-devtest-labs-vm"></a>从开发测试实验室 VM 创建 VM 映像

下一个任务是为 Azure 开发测试实验室实例中新部署的 VM 创建映像。 然后，在需要执行开发任务或运行某些测试时，即可随时使用该映像按需创建 VM 的副本。 

1. 在发布管道的“管道”选项卡上，选择“阶段 1”中带超链接的文本以查看阶段任务，然后选择“代理作业”旁边的加号 (+) 图标    。 
   
1. 在“添加任务”下，依次选择“Azure 开发测试实验室 - 创建自定义映像”、“添加”  。 
   
1. 对任务进行如下配置：
   
   |字段|值|
   |---|---|
   |Azure RM 订阅|选择你的服务连接或订阅。|
   |实验室名称|选择要在其中创建映像的现有实验室的名称。|
   |自定义映像名称|输入自定义映像的名称。|
   |说明（可选）|输入说明以方便稍后选择适当的映像。|
   |源实验室 VM > 源实验室 VM ID |如果你更改了 LabVMId 变量的默认名称，请在此处输入所用名称。 默认值为 $(labVMId)。|
   |**输出变量** > **自定义映像 ID**|可以根据需要编辑变量的默认名称。|
   
### <a name="deploy-your-app-to-the-devtest-labs-vm-optional"></a>将应用部署到开发测试实验室 VM（可选）

可以添加任务以将应用部署到新的开发测试实验室 VM。 用于部署应用的任务通常是“Azure 文件复制”和“目标计算机上的 PowerShell” 。

这些任务的参数所需的 VM 信息存储在发布管道中名为 labVmRgName、labVMIpAddress 和 labVMFqdn 的三个配置变量内  。 如果只想试验创建开发测试实验室 VM 和自定义映像，而不向其部署应用，可跳过此步骤。

### <a name="delete-the-vm"></a>删除 VM

最后一个任务是删除在 Azure 开发测试实验室实例中部署的 VM。 在部署的 VM 上执行所需开发任务或运行所需测试后，通常要删除该 VM。 

1. 在发布管道的“管道”选项卡上，选择“阶段 1”中带超链接的文本以查看阶段任务，然后选择“代理作业”旁边的加号 (+) 图标    。 
   
1. 在“添加任务”下，依次选择“Azure 开发测试实验室 - 删除 VM”、“添加”  。 
   
1. 对任务进行如下配置：
   
   - 在“Azure RM 订阅”下，选择你的服务连接或订阅。 
   - 对于“实验室 VM ID”，如果你更改了 LabVMId 变量的默认名称，请在此处输入所用名称。 默认值为 $(labVMId)。
   
### <a name="save-the-release-pipeline"></a>保存发布管道

若要保存新的发布管道，请执行以下操作：

1. 在发布管道页上选择名称“新发布管道”，然后输入管道的新名称。 
   
1. 选择右上方的“保存”图标。 

## <a name="create-and-run-a-release"></a>创建并运行发布

若要使用新管道创建并运行发布，请执行以下操作：

1. 选择发布管道页右上方的“创建发布”。 
   
1. 在“项目”下选择最新版本，然后选择“创建” 。
   
1. 在每个发布阶段，请在 Azure 门户中刷新开发测试实验室实例的视图，以查看 VM 创建、映像创建和 VM 删除操作的状态。

每当需要 VM 时，都可以使用自定义映像创建 VM。

## <a name="next-steps"></a>后续步骤
- 了解如何[使用 Resource Manager 模板创建多 VM 环境](devtest-lab-create-environment-from-arm.md)。
- 若要深入了解适用于开发测试实验室自动化的资源管理器模板快速入门，请参阅[公共开发测试实验室 GitHub 存储库](https://github.com/Azure/azure-quickstart-templates)。
- 如有必要，请参阅 [Azure DevOps 故障排除](/azure/devops/pipelines/troubleshooting)页。
 
