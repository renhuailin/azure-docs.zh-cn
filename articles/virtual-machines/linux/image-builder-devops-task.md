---
title: 预览：Azure 映像生成器服务 DevOps 任务
description: Azure DevOps 任务将生成工件注入到 VM 映像中，使你能够安装并配置应用程序和 OS。
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 01/27/2021
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c700170e59792dc6f782ded02960962c7b21d946
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696919"
---
# <a name="azure-image-builder-service-devops-task-preview"></a>Azure 映像生成器服务 DevOps 任务（预览）

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: 灵活规模集

本文介绍如何使用 Azure DevOps 任务将生成工件注入到 VM 映像中，以便能够安装并配置应用程序和 OS。

## <a name="devops-task-versions"></a>DevOps 任务版本
有两个 Azure VM 映像生成器 (AIB) DevOps 任务：

* [“稳定”AIB 任务](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder)：这是已经过测试的最新稳定版本，并且遥测数据未显示出任何问题。 


* [“不稳定”AIB 任务](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder-canary)：在我们将此版本提升为“稳定”任务之前，我们可在此版本中加入最新的更新和功能，并让客户对其进行测试。 如果未报告任何问题，并且在我们的遥测数据未显示出任何问题，则在大约 1 周后，我们就会将任务代码提升为“稳定”。 

## <a name="prerequisites"></a>先决条件

> [!NOTE]
> AIB 任务当前不支持 Windows 重启、以管理员身份运行提升的命令，这意味着它不适用于需要上述操作的 Windows 虚拟桌面场景或 Windows 自定义项。 如果希望将 DevOps 与映像生成器配合使用，应将模板嵌套到 Azure 资源管理器任务中，使用 AZ CLI 或 PowerShell 任务。

* [从 Visual Studio Marketplace 安装稳定 DevOps 任务](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder)。
* 必须有一个 VSTS DevOps 帐户，并已创建生成管道
* 在管道使用的订阅中注册并启用映像生成器功能要求：
    * [Az PowerShell](../windows/image-builder-powershell.md#register-features)
    * [Az CLI](../windows/image-builder.md#register-the-features)
    
* 在源映像资源组中创建标准 Azure 存储帐户；可以使用其他资源组/存储帐户。 该存储帐户用于将生成工件从 DevOps 任务传输到映像。

    ```powerShell
    # Az PowerShell
    $timeInt=$(get-date -UFormat "%s")
    $storageAccName="aibstorage"+$timeInt
    $location=westus
    # create storage account and blob in resource group
    New-AzStorageAccount -ResourceGroupName $strResourceGroup -Name $storageAccName -Location $location -SkuName Standard_LRS
    ```

    ```bash
    # Az CLI
    location=westus
    scriptStorageAcc=aibstordot$(date +'%s')
    # create storage account and blob in resource group
    az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS
    ```

## <a name="add-task-to-release-pipeline"></a>将任务添加到发布管道

选择“发布管道” > “编辑” 

在“用户代理”中选择“+”（添加），然后搜索“映像生成器”。 选择 **添加** 。

设置以下任务属性：

### <a name="azure-subscription"></a>Azure 订阅

从下拉菜单中选择要让映像生成器运行的订阅。 请使用源映像所在的并且要在其中分发映像的同一订阅。 需要授权映像生成器参与者访问订阅或资源组。

### <a name="resource-group"></a>资源组

请使用要将临时映像模板工件存储到的资源组。 创建模板工件时，将创建一个附加的临时映像生成器资源组 `IT_<DestinationResourceGroup>_<TemplateName>_guid`。 该临时资源组存储映像元数据，例如脚本。 任务结束时，将删除映像模板工件和临时映像生成器资源组。
 
### <a name="location"></a>位置

此位置是运行映像生成器的区域。 仅支持特定数量的[区域](../image-builder-overview.md#regions)。 源映像必须位于此位置。 例如，如果你使用共享映像库，则该区域中必须存在一个副本。

### <a name="managed-identity-required"></a>托管标识（必需）
映像生成器需要一个托管标识，该标识用于读取源自定义映像，连接到 Azure 存储，以及创建自定义映像。 有关更多详细信息，请参阅[了解 Azure 映像生成器](../image-builder-overview.md#permissions)。

### <a name="vnet-support"></a>VNET 支持

DevOps 任务目前不支持指定现有子网（此功能正在规划中），但是，如果你想要利用现有 VNET，可以使用嵌套了映像生成器模板的 ARM 模板，请参阅 Windows 映像生成器模板示例了解具体方法；或者，也可以使用 [AZ AIB PowerShell](../windows/image-builder-powershell.md)。

### <a name="source"></a>源

源映像必须采用支持的映像生成器 OS。 可以在映像生成器从中运行的区域中选择现有的自定义映像：
* 托管映像 - 需要传入 resourceId，例如：
    ```json
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```
* Azure 共享映像库 - 需要传入映像版本的 resourceId，例如：
    ```json
    /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/<versionNumber>
    ```

    如果需要获取最新的共享映像库版本，可以先使用一个 AZ PowerShell 或 AZ CLI 任务获取最新版本，并设置 DevOps 变量。 在 AZ VM 映像生成器 DevOps 任务中使用该变量。 有关详细信息，请参阅[示例](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/8_Getting_Latest_SIG_Version_ResID#getting-the-latest-image-version-resourceid-from-shared-image-gallery)。

* （市场）基础映像：提供了常用映像的下拉列表，这些映像始终使用受支持 OS 的“最新”版本。 

    如果该列表中不包含基础映像，你可以使用 `Publisher:Offer:Sku` 指定确切的映像。

    基础映像版本（可选）- 可以提供要使用的映像版本，默认值为 `latest`。

### <a name="customize"></a>自定义

#### <a name="provisioner"></a>预配程序

最初支持两个自定义程序 - Shell 和 PowerShell 。 仅支持内联。 如果你要下载脚本，可以传递内联命令。

请根据你的 OS 选择“PowerShell”或“Shell”。

#### <a name="windows-update-task"></a>Windows 更新任务

（仅适用于 Windows）自定义结束时，该任务将运行 Windows 更新。 它会处理所需的重新启动操作。

将执行以下 Windows 更新配置：
```json
    "type": "WindowsUpdate",
    "searchCriteria": "IsInstalled=0",
    "filters": [
        "exclude:$_.Title -like '*Preview*'",
        "include:$true"
```
该任务将安装非预览版的重要 Windows 更新和建议的 Windows 更新。

#### <a name="handling-reboots"></a>处理重新启动
DevOps 任务目前不支持重新启动 Windows 版本，如果你尝试使用 PowerShell 代码重新启动，生成将会失败。 不过，可以使用代码重新启动 Linux 生成。

#### <a name="build-path"></a>生成路径

该任务能够将 DevOps 生成发布工件注入到映像中。 若要正常完成此过程，需要设置一个生成管道。 在发布管道的设置中，必须添加生成工件的存储库。

:::image type="content" source="./media/image-builder-devops-task/add-artifact.png" alt-text="选择在发布管道中添加工件。":::

选择“生成路径”按钮，以选择要放在映像中的生成文件夹。 映像生成器任务将复制该文件夹中的所有文件和目录。 创建映像时，映像生成器会根据 OS 将这些文件和目录部署到不同的路径。

> [!IMPORTANT]
> 添加存储库工件时，你可能发现目录带有下划线“_”前缀。 下划线可能会导致内联命令出现问题。 请在命令中适当使用引号。
> 

以下示例显示了这种命名结构：

:::image type="content" source="./media/image-builder-devops-task/build-artifacts.png" alt-text="显示层次结构的目录结构。":::


* Windows - 文件存在于 `C:\` 中。 将创建名为 `buildArtifacts` 的目录，其中包含 `webapp` 目录。

* Linux - 文件存在于 `/tmp` 中。 将创建 `webapp` 目录，其中包含所有文件和目录。 必须移动此目录中的文件。 否则，这些文件将被删除，因为它们位于临时目录中。

#### <a name="inline-customization-script"></a>内联自定义脚本

* Windows - 可以输入逗号分隔的 PowerShell 内联命令。 如果你要在生成目录中运行脚本，可以使用：

    ```PowerShell
    & 'c:\buildArtifacts\webapp\webconfig.ps1'
    ```

   可以引用多个脚本，或添加更多命令，例如：

    ```PowerShell
    & 'c:\buildArtifacts\webapp\webconfig.ps1'
    & 'c:\buildArtifacts\webapp\installAgent.ps1'
    ```
* Linux - 在 Linux 系统上，生成工件将放在 `/tmp` 目录中。 但是，在许多 Linux OS 上，重新启动后会删除 /tmp 目录内容。 如果你希望将工件放在映像中，则必须创建另一个目录，并将工件复制到该目录。  例如：

    ```bash
    sudo mkdir /lib/buildArtifacts
    sudo cp -r "/tmp/_ImageBuilding/webapp" /lib/buildArtifacts/.
    ```
    
    如果你乐意使用“/tmp”目录，则可以使用以下代码来执行脚本。
    
    ```bash
    # grant execute permissions to execute scripts
    sudo chmod +x "/tmp/_ImageBuilding/webapp/coreConfig.sh"
    echo "running script"
    sudo . "/tmp/AppsAndImageBuilderLinux/_WebApp/coreConfig.sh"
    ```
    
#### <a name="what-happens-to-the-build-artifacts-after-the-image-build"></a>生成映像后，生成工件会发生什么情况？

> [!NOTE]
> 映像生成器不会自动删除生成工件，因此我们强烈建议始终通过代码删除生成工件。
> 

* Windows - 映像生成器将文件部署到 `c:\buildArtifacts` 目录。 该目录是永久保存的，必须将其删除。 可以在执行的脚本中将其删除。 例如：

    ```PowerShell
    # Clean up buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts\*" -Force -Recurse
    
    # Delete the buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts" -Force 
    ```
    
* Linux - 生成工件放在 `/tmp` 目录中。 但是，在许多 Linux OS 上，重新启动后会删除 `/tmp` 目录内容。 建议通过代码删除这些内容，而不要依赖 OS 来删除。 例如：

    ```bash
    sudo rm -R "/tmp/AppsAndImageBuilderLinux"
    ```
    
#### <a name="total-length-of-image-build"></a>映像生成的总时长

目前无法在 DevOps 管道任务中更改总时长。 映像生成使用的默认值为 240 分钟。 如果你要增大 [buildTimeoutInMinutes](./image-builder-json.md#properties-buildtimeoutinminutes)，可以在发布管道中使用 AZ CLI 任务。 将该任务配置为复制并提交某个模板。 有关示例，请参阅此[解决方案](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/4_Using_ENV_Variables#using-environment-variables-and-parameters-with-image-builder)；或者，可以使用 Az PowerShell。


#### <a name="storage-account"></a>存储帐户

选择在先决条件部分创建的存储帐户。 如果列表中未显示该存储帐户，则表示映像生成器对该存储帐户没有权限。

生成开始时，映像生成器将创建名为 `imagebuilder-vststask` 的容器。 该容器是要将存储库中的生成工件存储到的位置。

> [!NOTE]
> 每次生成后，都需要手动删除存储帐户或容器。
>

### <a name="distribute"></a>分发

支持 3 种分发类型。

#### <a name="managed-image"></a>托管映像

* ResourceID：
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```

* 位置

#### <a name="azure-shared-image-gallery"></a>Azure 共享映像库

共享映像库必须已存在。

* ResourceID： 
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>
    ```

* Regions：逗号分隔的区域列表。 例如 westus、eastus、centralus

#### <a name="vhd"></a>VHD

不能将任何值传递到 VHD，映像生成器会将 VHD 发送到临时映像生成器资源组 `IT_<DestinationResourceGroup>_<TemplateName>` 的 vhds 容器中。 开始发布生成时，映像生成器将发出日志。 完成后，它将发出 VHD URL。

### <a name="optional-settings"></a>可选设置

* [VM 大小](image-builder-json.md#vmprofile) - 可以替代默认的 VM 大小 Standard_D1_v2。 若要缩减自定义总时长，或者要创建依赖于某些 VM 大小的映像（例如 GPU/HPC 等），可以替代大小。

## <a name="how-it-works"></a>工作原理

创建发布时，该任务将在存储帐户中创建名为 imagebuilder-vststask 的容器。 该任务将以 zip 格式压缩并上传生成工件，并为 zip 文件创建 SAS 令牌。

该任务使用传递给它的属性创建映像生成器模板工件。 该任务执行以下操作：
* 下载生成工件 zip 文件以及任何其他关联的脚本。 文件保存在临时映像生成器资源组 `IT_<DestinationResourceGroup>_<TemplateName>` 的某个存储帐户中。
* 创建名称前缀为 t_ 后接 10 位数单调整数的模板。 该模板保存到所选的资源组中。 在整个生成持续时间内，该模板会一直保存在该资源组中。 

示例输出：

```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
starting put template...
```

当映像生成开始时，发布日志中会报告运行状态：

```text
starting run template...
```

当映像生成完成时，你将看到类似于以下文本的输出：

```text
2019-05-06T12:49:52.0558229Z starting run template...
2019-05-06T13:36:33.8863094Z run template:  Succeeded
2019-05-06T13:36:33.8867768Z getting runOutput for  SharedImage_distribute
2019-05-06T13:36:34.6652541Z ==============================================================================
2019-05-06T13:36:34.6652925Z ## task output variables ##
2019-05-06T13:36:34.6658728Z $(imageUri) =  /subscriptions/<subscriptionID>/resourceGroups/aibwinsig/providers/Microsoft.Compute/galleries/my22stSIG/images/winWAppimages/versions/0.23760.13763
2019-05-06T13:36:34.6659989Z ==============================================================================
2019-05-06T13:36:34.6663500Z deleting template t_1557146959485...
2019-05-06T13:36:34.6673713Z deleting storage blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip
2019-05-06T13:36:34.9786039Z blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip is deleted
2019-05-06T13:38:37.4884068Z delete template:  Succeeded
```

将删除映像模板和 `IT_<DestinationResourceGroup>_<TemplateName>`。

可以提取“$(imageUri)”VSTS 变量并在下一任务中使用，或者只是使用值并生成 VM。

## <a name="output-devops-variables"></a>输出 DevOps 变量

源市场映像的发布者/产品（服务）/SKU/版本：
* $(pirPublisher)
* $(pirOffer)
* $(pirSku)
* $(pirVersion)

映像 URI - 分发的映像的 ResourceID：
* $(imageUri)

## <a name="faq"></a>FAQ

### <a name="can-i-use-an-existing-image-template-i-have-already-created-outside-of-devops"></a>是否可以使用我在 DevOps 外部创建的现有映像模板？

目前不可以。

### <a name="can-i-specify-the-image-template-name"></a>是否可以指定映像模板名称？

不知道。 系统使用唯一的模板名称，生成后将删除模板。

### <a name="the-image-builder-failed-how-can-i-troubleshoot"></a>映像生成器失败。 如何进行故障排除？

如果生成失败，DevOps 任务不会删除暂存资源组。 你可以访问包含生成自定义日志的暂存资源组。

你会在 VM 映像生成器任务的 DevOps 日志中看到错误，还会看到 customization.log 位置。 例如：

:::image type="content" source="./media/image-builder-devops-task/devops-task-error.png" alt-text="显示失败的示例 DevOps 任务错误。":::

有关故障排除的详细信息，请参阅[排查 Azure 映像生成器服务的问题](image-builder-troubleshoot.md)。 

调查失败原因后，可以删除暂存资源组。 首先请删除映像模板资源工件。 该工件的前缀为 t_，可以在 DevOps 任务生成日志中找到：

```text
...
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
...
template name:  t_1556938436xxx
...

```

映像模板资源工件位于最初在任务中指定的资源组内。 完成故障排除后，请删除该工件。 如果要使用 Azure 门户删除工件，请在资源组中选择“显示隐藏的类型”以查看该工件。


## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [Azure 映像生成器概述](../image-builder-overview.md)。

