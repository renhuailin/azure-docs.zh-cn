---
title: 自定义 Azure-SSIS Integration Runtime 的安装
description: 本文介绍如何使用 Azure-SSIS Integration Runtime 的自定义安装界面安装其他组件或更改设置
ms.service: data-factory
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 04/30/2021
ms.openlocfilehash: ddd837387de955e04cdf39ec73104dfda98e3cf4
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110470894"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>自定义 Azure-SSIS Integration Runtime 的安装

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

可以通过自定义安装程序在 Azure 数据工厂 (ADF) 中自定义 Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR)。 可以通过它们在预配或重新配置 Azure-SSIS IR 期间添加你自己的步骤。 

使用自定义安装程序可以更改 Azure-SSIS IR 的默认操作配置或环境。 例如，可以启动其它 Windows 服务、保存访问凭据以便进行文件共享，或者仅使用强加密/更安全的网络协议 (TLS 1.2)。 或者，可以在 Azure-SSIS IR 的每个节点上安装其他组件，例如程序集、驱动程序或扩展。 它们可以是自定义的、开源的或第三方的组件。 有关内置/已预安装组件的详细信息，请参阅 [Azure-SSIS IR 上的内置/已预安装组件](./built-in-preinstalled-components-ssis-integration-runtime.md)。

可以通过两种方式在 Azure-SSIS IR 上执行自定义安装： 
* **使用脚本的标准自定义安装**：准备一个脚本及其关联的文件，并将其全部上传到 Azure 存储帐户中的 Blob 容器。 然后在安装或重新配置 Azure-SSIS IR 时，为 Blob 容器提供共享访问签名 (SAS) 统一资源标识符 (URI)。 然后，Azure-SSIS IR 的每个节点将从 Blob 容器下载该脚本及其关联的文件，并使用提升的权限运行自定义安装。 自定义安装完成后，每个节点会将标准执行输出和其他日志上传到 Blob 容器中。
* **无需脚本的快速自定义安装**：运行一些常见的系统配置和 Windows 命令，或安装一些流行的或推荐的其他组件，而无需使用任何脚本。

无论使用标准还是快速自定义安装程序，都可安装免费（无许可证）组件和付费（带许可证）组件。 如果你是独立软件供应商 (ISV)，请参阅[为 Azure-SSIS IR 开发付费或带许可证的组件](how-to-develop-azure-ssis-ir-licensed-components.md)。

> [!IMPORTANT]
> 为了利用将来的增强功能，建议对采用自定义设置的 Azure-SSIS IR 使用 v3 或更高版本的节点系列。

## <a name="current-limitations"></a>当前限制

以下限制仅适用于标准自定义安装：

- 若要在脚本中使用 *gacutil.exe* 将程序集安装到全局程序集缓存 (GAC)，需要在自定义安装过程中提供 *gacutil.exe*。 或者可使用“公共预览版”Blob 容器的“示例”文件夹中提供的副本，请参阅下面的“标准自定义安装示例”部分。

- 若要在脚本中引用某个子文件夹，请注意 *msiexec.exe* 不支持使用 `.\` 表示法引用根文件夹。 请使用类似 `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` 的命令，而不要使用 `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`。

- Azure-SSIS IR 当前不支持管理共享，或 Windows 自动创建的隐藏的网络共享。

- Azure-SSIS IR 不支持 IBM iSeries Access ODBC 驱动程序。 在自定义安装过程中，可能会出现安装错误。 如果出现，请联系 IBM 支持人员获取帮助。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要自定义 Azure-SSIS IR，需要准备好以下各项：

- [Azure 订阅](https://azure.microsoft.com/)

- [预配 Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md)

- [Azure 存储帐户](https://azure.microsoft.com/services/storage/)。 对于快速自定义安装并非必需。 对于标准自定义安装，请在 Blob 容器中上传并存储自定义安装脚本及其关联文件。 自定义安装进程还会将其执行日志上传到相同的 Blob 容器。

## <a name="instructions"></a>说明

可以使用 ADF UI 上的自定义安装来预配或重新配置 Azure-SSIS IR。 若要使用 PowerShell 执行相同操作，请下载并安装 [Azure PowerShell](/powershell/azure/install-az-ps)。

### <a name="standard-custom-setup"></a>标准自定义安装

若要在 ADF UI 上为 Azure-SSIS IR 预配或重新配置标准自定义安装程序，请完成以下步骤。

1. 准备自定义安装脚本及其关联的文件（例如 .bat、.cmd、.exe、.dll、.msi 或 .ps1 文件）。

   * 必须创建一个名为 *main.cmd* 的脚本文件，即自定义安装程序的入口点。  
   * 为了确保脚本能够以无提示方式执行，你应该先在本地计算机上对其进行测试。  
   * 若要将其他工具（例如“msiexec.exe”）生成的其他日志上传到 Blob 容器，请在脚本中指定预定义的环境变量 `CUSTOM_SETUP_SCRIPT_LOG_DIR` 作为日志文件夹（例如“msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log”）。

1. 下载、安装并打开 [Azure 存储资源管理器](https://storageexplorer.com/)。

   a. 在“本地和附加”下面，右键单击“存储帐户”并选择“连接到 Azure 存储”。

      ![连接到 Azure 存储](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. 依次选择“存储帐户或服务”、“帐户名称和密钥”，然后选择“下一步”。

   c. 输入 Azure 存储帐户名和密钥，并依次选择“下一步”、“连接”。 

      ![提供存储帐户名称和密钥](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. 在已连接的 Azure 存储帐户下面，右键单击“Blob 容器”，选择“创建 Blob 容器”，并为新 Blob 容器命名。 

      ![创建 Blob 容器](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. 选择新 Blob 容器并上传自定义安装脚本及其关联的文件。 请务必将“main.cmd”上传到 Blob 容器的顶级目录，而不要上传到任何文件夹中。 Blob 容器应该仅包含所需的自定义安装程序文件，以便稍后将这些文件下载到 Azure-SSIS IR 时不会花费太长的时间。 自定义安装的最大持续时间目前设置为 45 分钟，45 分钟后会超时。这包括从 Blob 容器下载所有文件并将其安装在 Azure-SSIS IR 上所花费的时间。 如果安装需要更长的时间，请提交支持票证。

      ![将文件上传到 Blob 容器](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. 右键单击 Blob 容器，然后选择“获取共享访问签名”。

      ![获取 Blob 容器的共享访问签名](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. 为 Blob 容器创建一个过期时间足够长且拥有读取/写入/列出权限的 SAS URI。 你需要使用该 SAS URI 来下载并运行自定义安装程序脚本及其关联文件。 每当将 Azure-SSIS IR 的任何节点重置映像或重启时，都会发生这种情况。 你还需要拥有写入权限才能上传安装执行日志。

      > [!IMPORTANT]
      > 确保在 Azure-SSIS IR 的整个生命周期内（从创建到删除），尤其是在此期间你经常停止并启动 Azure-SSIS IR 时，SAS URI 不会过期，并且自定义安装资源始终可用。

      ![生成 Blob 容器的共享访问签名](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. 复制并保存 Blob 容器的 SAS URI。

      ![复制并保存共享访问签名](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. 在“Integration Runtime 安装程序”窗格的“高级设置”页上选中“使用其他系统配置/组件安装来自定义 Azure-SSIS Integration Runtime”复选框。 接下来，请在“自定义安装程序容器 SAS URI”文本框中输入 Blob 容器的 SAS URI。

   ![包含自定义安装的高级设置](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

标准自定义安装程序完成并且 Azure-SSIS IR 启动之后，可在 Blob 容器的“main.cmd.log”文件夹中找到所有自定义安装程序日志。 它们包括 main.cmd 的标准输出和其他执行日志。

### <a name="express-custom-setup"></a>快速自定义安装

若要在 ADF UI 上为 Azure-SSIS IR 预配或重新配置快速自定义安装程序，请完成以下步骤。

1. 在“Integration Runtime 安装程序”窗格的“高级设置”页上选中“使用其他系统配置/组件安装来自定义 Azure-SSIS Integration Runtime”复选框。 

1. 选择“新建”打开“添加快速自定义安装程序”窗格，然后在“快速自定义安装程序类型”下拉列表中选择一种类型。   我们目前提供的快速自定义安装程序用于运行 cmdkey 命令、添加环境变量、安装 Azure PowerShell 以及安装许可的组件。

#### <a name="running-cmdkey-command"></a>运行 cmdkey 命令

如果为快速自定义安装程序选择“运行 cmdkey 命令”类型，则可在 Azure-SSIS IR 上运行 Windows cmdkey 命令。 为此，请分别在“/Add”、“/User”和“/Pass”文本框中输入目标计算机名或域名、用户名或帐户名以及密码或帐户密钥。  。 这样即可为 Azure-SSIS IR 上的 SQL Server、文件共享或 Azure 文件存储持久保留访问凭据。 例如，若要访问 Azure 文件存储，可以为“/Add”、“/User”和“/Pass”分别输入 `YourAzureStorageAccountName.file.core.windows.net`、 `azure\YourAzureStorageAccountName` 和 `YourAzureStorageAccountKey`。  。 这类似于在本地计算机上运行 Windows [cmdkey](/windows-server/administration/windows-commands/cmdkey) 命令。 目前仅支持一种运行 cmdkey 命令的快速自定义设置。 若要运行多个 cmdkey 命令，请改用标准自定义设置。

#### <a name="adding-environment-variables"></a>添加环境变量

如果为快速自定义安装程序选择“添加环境变量”类型，则可在 Azure-SSIS IR 上添加 Windows 环境变量。 为此，请分别在“变量名称”和“变量值”文本框中输入环境变量名称和值。  这样，便可在 Azure-SSIS IR 上运行的包中使用环境变量，例如，在脚本组件/任务中。 这类似于在本地计算机上运行 Windows [set](/windows-server/administration/windows-commands/set_1) 命令。

#### <a name="installing-azure-powershell"></a>安装 Azure PowerShell

如果为快速自定义安装程序选择“安装 Azure PowerShell”类型，则可在 Azure-SSIS IR 上安装 PowerShell 的 Az 模块。 为此，请在[支持的版本列表](https://www.powershellgallery.com/packages/az)中输入所需的 Az 模块版本号 (x.y.z)。 这样即可在包中运行 Azure PowerShell cmdlet/脚本来管理 Azure 资源，例如 [Azure Analysis Services (AAS)](../analysis-services/analysis-services-powershell.md)。

#### <a name="installing-licensed-components"></a>安装许可的组件

如果为快速自定义安装程序选择“安装许可的组件”类型，则可在“组件名”下拉列表中选择来自我们的 ISV 合作伙伴的集成组件： 

* 如果选择“SentryOne Task Factory”组件，可通过在“许可证密钥”框中输入从 SentryOne 购买的产品许可证密钥，在 Azure-SSIS IR 上安装 SentryOne 提供的 [Task Factory](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) 组件套件。  当前的集成版本是“2020.21.2”。

* 如果选择“oh22 的 HEDDA.IO”组件，可在 Azure-SSIS IR 上安装 oh22 的 [HEDDA.IO](https://github.com/oh22is/HEDDA.IO/tree/master/SSIS-IR) 数据质量/清理组件。 为此，需要预先购买其服务。 当前的集成版本是 1.0.14。

* 如果选择“oh22 的 SQLPhonetics.NET”组件，可在 Azure-SSIS IR 上安装 oh22 的 [SQLPhonetics.NET](https://appsource.microsoft.com/product/web-apps/oh22.sqlphonetics-ssis) 数据质量/匹配组件。 为此，请在“许可证密钥”文本框中输入预先从他们那里购买的产品许可证密钥。 当前的集成版本是 1.0.45。
   
* 如果选择“KingswaySoft 的 SSIS Integration Toolkit”组件，可以通过在“许可密钥”框中输入从 KingswaySoft 购买的产品许可密钥，在 Azure-SSIS IR 上安装适用于 KingswaySoft 所提供的 CRM/ERP/营销/协作应用（例如 Microsoft Dynamics/SharePoint/Project Server、Oracle/Salesforce Marketing Cloud 等）的 [SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) 套件。  当前的集成版本是“20.2”。

* 如果选择“KingswaySoft 的 SSIS Productivity Pack”组件，可以通过在“许可密钥”框中输入从 KingswaySoft 购买的产品许可密钥，在 Azure-SSIS IR 上安装 KingswaySoft 所提供的组件的 [SSIS Productivity Pack](https://www.kingswaysoft.com/products/ssis-productivity-pack) 套件。  当前的集成版本是“20.2”。

* 如果选择“Theobald Software 的 Xtract IS”组件，可以通过将从 Theobald Software 购买的产品许可证文件拖放/上传到“许可证文件”框中，在 Azure-SSIS IR 上安装 Theobald Software 所提供的 SAP 系统（ERP、S/4HANA、BW）的 [Xtract IS](https://theobald-software.com/en/xtract-is/) 连接器套件。 当前的集成版本是“6.5.13.18”。

* 如果选择“AecorSoft 的 Integration Service”组件，可在 Azure-SSIS IR 上安装 AecorSoft 提供的适用于 SAP 和 Salesforce 系统的 [Integration Service](https://www.aecorsoft.com/en/products/integrationservice) 连接器套件。 为此，请在“许可证密钥”文本框中输入预先从他们那里购买的产品许可证密钥。 当前的集成版本是 3.0.00。

* 如果选择“CData 的 SSIS 标准包”组件，则可以在 Azure-SSIS IR 上安装来自 CData 的 [SSIS 标准包](https://www.cdata.com/kb/entries/ssis-adf-packages.rst#standard)套件，其中包括最常用组件（例如 Microsoft SharePoint 连接器）。 为此，请在“许可证密钥”文本框中输入预先从他们那里购买的产品许可证密钥。 当前的集成版本是 19.7354。

* 如果选择“CData 的 SSIS 扩展包”组件，则可以在 Azure-SSIS IR 上安装包含 CData 所有组件（例如 Microsoft Dynamics 365 Business Central 连接器和“SSIS 标准包”中的其他组件）的 [SSIS 扩展包](https://www.cdata.com/kb/entries/ssis-adf-packages.rst#extended)套件。 为此，请在“许可证密钥”文本框中输入预先从他们那里购买的产品许可证密钥。 当前的集成版本是 19.7354。 由于其太大，为避免安装超时，请确保 Azure-SSIS IR 为每个节点设置至少 4 个 CPU 核心。

添加的快速自定义安装将显示在“高级设置”页上。 若要删除它们，请选中其复选框，然后选择“删除”。

### <a name="azure-powershell"></a>Azure PowerShell

若要通过 Azure PowerShell 使用自定义安装来预配或重新配置 Azure-SSIS IR，请完成以下步骤。

1. 如果 Azure-SSIS IR 已启动/正在运行，请先将其停止。

1. 然后，可以在启动 Azure-SSIS IR 之前，通过运行 `Set-AzDataFactoryV2IntegrationRuntime` cmdlet 来添加或删除自定义设置。

   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script

   # Add custom setup parameters if you use standard/express custom setups
   if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
   {
       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -SetupScriptContainerSasUri $SetupScriptContainerSasUri
   }
   if(![string]::IsNullOrEmpty($ExpressCustomSetup))
   {
       if($ExpressCustomSetup -eq "RunCmdkey")
       {
           $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
           $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
           $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
       }
       if($ExpressCustomSetup -eq "SetEnvironmentVariable")
       {
           $variableName = "YourVariableName"
           $variableValue = "YourVariableValue"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
       }
       if($ExpressCustomSetup -eq "InstallAzurePowerShell")
       {
           $moduleVersion = "YourAzModuleVersion"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
       }
       if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
       {
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       if($ExpressCustomSetup -eq "Theobald.XtractIS")
       {
           $jsonData = Get-Content -Raw -Path YourLicenseFile.json
           $jsonData = $jsonData -replace '\s',''
           $jsonData = $jsonData.replace('"','\"')
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "CData.Standard")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "CData.Extended")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       # Create an array of one or more express custom setups
       $setups = New-Object System.Collections.ArrayList
       $setups.Add($setup)

       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -ExpressCustomSetup $setups
   }
   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
       -DataFactoryName $DataFactoryName `
       -Name $AzureSSISName `
       -Force
   ```

### <a name="standard-custom-setup-samples"></a>标准自定义安装示例

若要查看和重复使用某些标准自定义安装示例，请完成以下步骤。

1. 使用 Azure 存储资源管理器连接到公共预览版 Blob 容器。

   a. 在“本地和附加”下面，右键单击“存储帐户”并选择“连接到 Azure 存储”。

      ![连接到 Azure 存储](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. 依次选择“Blob 容器”、“共享访问签名 URL (SAS)”，然后选择“下一步”。

   c. 在“Blob 容器 SAS URL”文本框中，输入下面公共预览版 Blob 容器的 SAS URI，选择“下一步”，然后选择“连接”。

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

   d. 在左侧窗格中选择已连接的“publicpreview”Blob 容器，然后双击“CustomSetupScript”文件夹。 此文件夹包含以下项：

      * 一个 *Sample* 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装基本任务的自定义安装程序。 该任务不会执行任何操作，而是休眠几秒。 该文件夹还包含“gacutil”文件夹，其整个内容（“gacutil.exe”、“gacutil.exe.config”和“1033\gacutlrc.dll”）都可以按原样复制到 Blob 容器中。

      * 一个 *UserScenarios* 文件夹，其中包含实际用户方案中的多个自定义安装示例。 若要在 Azure-SSIS IR 上安装多个示例，则可以将其自定义安装脚本 (main.cmd) 文件合并到一个文件中，并将其与所有相关文件一起上传到 Blob 容器中。

        ![公共预览版 Blob 容器的内容](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. 双击“UserScenarios”文件夹以查找以下项：

      * 一个 .NET FRAMEWORK 3.5 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 .NET Framework 早期版本的自定义安装程序脚本 (main.cmd)。 某些自定义组件可能需要此版本。

      * 一个 BCP 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 SQL Server 命令行实用工具 (MsSqlCmdLnUtils.msi) 的自定义安装程序脚本 (main.cmd)。 其中一个实用工具是大容量复制程序 (bcp)。

      * DNS 后缀文件夹，其中包含一个自定义设置脚本 (main.cmd)，用于将你自己的 DNS 后缀（例如“test.com”）追加到任何未限定的单标签域名，并将其转换为完全限定的域名 (FQDN)，然后在来自 Azure-SSIS IR 的 DNS 查询中使用它。

      * 一个 EXCEL 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装一些 C# 程序集和库的自定义安装程序脚本 (main.cmd)。 可在“脚本任务”中使用它们来动态读取和写入 Excel 文件。 
      
        首先，请下载 [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) 和 [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/)，然后将它们连同 “main.cmd”一起上传到 Blob 容器中。 另外，如果只想使用标准的 Excel 连接器（连接管理器、源和目标），则由于包含它们的 Access 可再发行程序包已预先安装在 Azure-SSIS IR 上，因此无需任何自定义安装程序。
      
      * 一个 *MYSQL ODBC* 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 MySQL ODBC 驱动程序的自定义安装脚本 (*main.cmd*)。 可以通过此安装程序使用 ODBC 连接器（连接管理器、源和目标）连接到 MySQL 服务器。 
     
        首先，[下载最新的 64 位和 32 位版 MySQL ODBC 驱动程序安装程序](https://dev.mysql.com/downloads/connector/odbc/)（例如“mysql-connector-odbc-8.0.13-winx64.msi”和“mysql-connector-odbc-8.0.13-win32.msi”），然后将它们连同“main.cmd”一起上传到 Blob 容器中。
        
        如果在连接中使用数据源名称 (DSN)，则需要在安装脚本中使用 DSN 配置。 例如：C:\Windows\SysWOW64\odbcconf.exe /A {CONFIGSYSDSN "MySQL ODBC 8.0 Unicode Driver" "DSN=\<dsnname\>|PORT=3306|SERVER=\<servername\>"}

      * 一个 *ORACLE ENTERPRISE* 文件夹，其中包含用于在 Azure-SSIS IR 企业版的每个节点上安装 Oracle 连接器和 OCI 驱动程序的自定义安装脚本 (*main.cmd*) 和无提示安装配置文件 (*client.rsp*)。 可以通过此安装程序使用 Oracle 连接管理器、源和目标连接到 Oracle 服务器。 
      
        首先，从 [Microsoft 下载中心](https://www.microsoft.com/en-us/download/details.aspx?id=55179)下载适用于 Oracle 的 Microsoft 连接器 v5.0（AttunitySSISOraAdaptersSetup.msi 和 AttunitySSISOraAdaptersSetup64.msi），并从 [Oracle](https://www.oracle.com/database/technologies/oracle19c-windows-downloads.html) 下载最新的 Oracle 客户端（例如 winx64_12102_client.zip）。 接下来，将它们连同“main.cmd”和“client.rsp”一起上传到 Blob 容器中。 如果使用 TNS 连接到 Oracle，则还需要下载“tnsnames.ora”，对其进行编辑，然后将其上传到 Blob 容器。 这样，就可以在安装期间将其复制到 Oracle 安装文件夹中。

      * 一个 *ORACLE STANDARD ADO.NET* 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 Oracle ODP.NET 驱动程序的自定义安装脚本 (*main.cmd*)。 可以通过此安装程序使用 ADO.NET 连接管理器、源和目标连接到 Oracle 服务器。 
      
        首先，[下载最新的 Oracle ODP.NET 驱动程序](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)（例如“ODP.NET_Managed_ODAC122cR1.zip”），然后将其连同“main.cmd”一起上传到 Blob 容器中。
       
      * 一个 ORACLE STANDARD ODBC 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 Oracle ODBC 驱动程序的自定义安装程序脚本 (main.cmd)。 该脚本还配置数据源名称 (DSN)。 借助此安装程序，可以使用 ODBC 连接管理器、源和目标或数据源类型为 ODBC 的 Power Query 连接管理器和源连接到 Oracle 服务器。 
      
        首先，下载最新的 Oracle Instant Client（基本包或基本精简包）和 ODBC 包，然后将它们连同“main.cmd”一起上传到 Blob 容器中：
        * [下载 64 位包](https://www.oracle.com/technetwork/topics/winx64soft-089540.html)（基本包：*instantclient-basic-windows.x64-18.3.0.0.0dbru.zip*；基本精简包：*instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip*；ODBC 包：*instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*） 
        * [下载 32 位包](https://www.oracle.com/technetwork/topics/winsoft-085727.html)（基本包：*instantclient-basic-nt-18.3.0.0.0dbru.zip*；基本精简包：*instantclient-basiclite-nt-18.3.0.0.0dbru.zip*；ODBC 包：*instantclient-odbc-nt-18.3.0.0.0dbru.zip*）

      * 一个 *ORACLE STANDARD OLEDB* 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 Oracle OLEDB 驱动程序的自定义安装脚本 (*main.cmd*)。 可以通过此安装程序使用 OLEDB 连接管理器、源和目标连接到 Oracle 服务器。 
     
        首先，[下载最新的 Oracle OLEDB 驱动程序](https://www.oracle.com/partners/campaign/index-090165.html)（例如“ODAC122010Xcopy_x64.zip”），然后将其连同“main.cmd”一起上传到 Blob 容器中。

      * 一个 *POSTGRESQL ODBC* 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 PostgreSQL ODBC 驱动程序的自定义安装脚本 (*main.cmd*)。 可以通过此安装程序使用 ODBC 连接管理器、源和目标连接到 PostgreSQL 服务器。 
     
        首先，[下载最新的 64 位和 32 位版本的 PostgreSQL ODBC 驱动程序安装程序](https://www.postgresql.org/ftp/odbc/versions/msi/)（例如“psqlodbc_x64.msi”和“psqlodbc_x86.msi”），然后将它们连同“main.cmd”一起上传到 Blob 容器中。

      * 一个 *SAP BW* 文件夹，其中包含用于在 Azure-SSIS IR 企业版的每个节点上安装 SAP .NET 连接器程序集 (*librfc32.dll*) 的自定义安装脚本 (*main.cmd*)。 可以通过此安装程序使用 SAP BW 连接管理器、源和目标连接到 SAP BW 服务器。 
      
        首先，将 64 位或 32 位版本的“librfc32.dll”连同“main.cmd”一起从 SAP 安装文件夹上传到 Blob 容器中。 然后，该脚本会在安装期间将 SAP 程序集复制到 *%windir%\SysWow64* 或 *%windir%\System32* 文件夹中。

      * 一个 STORAGE 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 Azure PowerShell 的自定义安装程序脚本 (main.cmd)。 可以通过此安装程序部署并运行 SSIS 包，以便运行[用于管理 Azure 存储的 Azure PowerShell cmdlet/脚本](../storage/blobs/storage-quickstart-blobs-powershell.md)。 
      
        将“main.cmd”、示例“AzurePowerShell.msi”（或使用最新版本）和“storage.ps1”复制到 Blob 容器。 使用 *PowerShell.dtsx* 作为包的模板。 包模板中合并了 [Azure Blob 下载任务](/sql/integration-services/control-flow/azure-blob-download-task)（用于下载可修改的 PowerShell 脚本 (storage.ps1)）和[执行进程任务](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/)（用于在每个节点上执行脚本）。

      * 一个 *TERADATA* 文件夹，其中包含自定义安装脚本 (*main.cmd*)、其关联的文件 (*install.cmd*) 和安装程序包 ( *.msi*)。 这些文件将在 Azure-SSIS IR 企业版的每个节点上安装 Teradata 连接器、Teradata 并行传输程序 (TPT) API 和 ODBC 驱动程序。 可以通过此安装程序使用 Teradata 连接管理器、源和目标连接到 Teradata 服务器。 
      
        首先，[下载 Teradata 工具和实用工具 15.x zip 文件](http://partnerintelligence.teradata.com)（例如“TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip”），然后将其连同上述“.cmd”和“.msi”文件一起上传到 Blob 容器中。

      * 一个“TLS 1.2”文件夹，其中包含一个自定义安装程序脚本 (main.cmd)，用于在 Azure-SSIS IR 的每个节点上仅使用强加密/更安全的网络协议 (TLS 1.2)。 此脚本同时还禁用较旧的 SSL/TLS 版本（SSL 3.0、TLS 1.0、TLS 1.1）。

      * 一个 *ZULU OPENJDK* 文件夹，其中包含用于在 Azure-SSIS IR 的每个节点上安装 Zulu OpenJDK 的自定义安装脚本 (*main.cmd*) 和 PowerShell 文件 (*install_openjdk.ps1*)。 借助此安装程序，可使用 Azure Data Lake Store 和 Flexible File 连接器来处理 ORC 和 Parquet 文件。 有关详细信息，请参阅 [Azure Feature Pack for Integration Services](/sql/integration-services/azure-feature-pack-for-integration-services-ssis#dependency-on-java)。 
      
        首先，[下载最新的 Zulu OpenJDK](https://www.azul.com/downloads/zulu/zulu-windows/)（例如“zulu8.33.0.1-jdk8.0.192-win_x64.zip”），然后将其连同“main.cmd”和“install_openjdk.ps1”一起上传到 Blob 容器中。

        ![用户方案文件夹中的文件夹](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. 若要重用这些标准自定义安装示例，请将选定文件夹的内容复制到 Blob 容器中。

1. 使用 ADF UI 预配或重新配置 Azure-SSIS IR 时，在“Integration Runtime 安装程序”窗格的“高级设置”页上选中“使用其他系统配置/组件安装来自定义 Azure-SSIS Integration Runtime”复选框。 接下来，请在“自定义安装程序容器 SAS URI”文本框中输入 Blob 容器的 SAS URI。
   
1. 使用 Azure PowerShell 预配或重新配置 Azure-SSIS IR 时，如果它已经启动/正在运行，则将其停止，使用 Blob 容器的 SAS URI 作为 `SetupScriptContainerSasUri` 参数的值运行 `Set-AzDataFactoryV2IntegrationRuntime` cmdlet，然后启动 Azure-SSIS IR。

1. 标准自定义安装程序完成并且 Azure-SSIS IR 启动之后，可在 Blob 容器的“main.cmd.log”文件夹中找到所有自定义安装程序日志。 它们包括 main.cmd 的标准输出和其他执行日志。

## <a name="next-steps"></a>后续步骤

- [安装 Azure-SSIS IR 企业版](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [开发 Azure-SSIS IR 的付费或许可组件](how-to-develop-azure-ssis-ir-licensed-components.md)