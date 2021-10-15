---
title: 从门户使用“执行 SSIS 包”活动
description: 本文介绍如何使用“执行 SSIS 包”活动和 Azure 数据工厂工作室门户在 Azure 数据工厂管道中运行 SQL Server Integration Services (SSIS) 包。
ms.service: data-factory
ms.subservice: integration-services
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.date: 10/01/2021
ms.openlocfilehash: 1e8c1777c8f2a95bd63ab9f03abc9c65fe0b1b6f
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129357961"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-the-azure-data-factory-studio-portal"></a>在 Azure 数据工厂工作室门户中使用“执行 SSIS 包”活动运行 SSIS 包

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文介绍如何使用“执行 SSIS 包”活动和 Azure 数据工厂工作室门户 UI 在 Azure 数据工厂管道中运行 SQL Server Integration Services (SSIS) 包。 

## <a name="prerequisites"></a>先决条件

如果还没有 Azure-SSIS Integration Runtime (IR)，请按照以下文章中的分步说明创建 IR：[教程：预配 Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md)。

## <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>使用“执行 SSIS 包”活动创建管道
此步骤使用数据工厂 UI 或应用创建管道。 将“执行 SSIS 包”活动添加到管道，并将该活动配置为运行 SSIS 包。 

1. 在 Azure 门户中的数据工厂概述或主页上，选择“创作和监视”磁贴，在单独的选项卡中启动数据工厂 UI 或应用。 

   :::image type="content" source="./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png" alt-text="数据工厂主页":::

   在主页上，选择“协调”。 

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="显示 ADF 主页的屏幕截图。":::

1. 在“活动”工具箱中，展开“常规”。 然后将“执行 SSIS 包”活动拖到管道设计图面上。 

   :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png" alt-text="将“执行 SSIS 包”活动拖到设计面"::: 

   选择“执行 SSIS 包”活动对象，以配置其“常规”、“设置”、“SSIS 参数”、“连接管理器”和“属性替代”选项卡。

### <a name="general-tab"></a>“常规”选项卡

在“执行 SSIS 包”活动的“常规”选项卡上，完成以下步骤。

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png" alt-text="在“常规”选项卡上设置属性":::

   1. 对于“名称”，请输入你的“执行 SSIS 包”活动的名称。

   1. 对于“说明”，请输入你的“执行 SSIS 包”活动的说明。

   1. 对于“超时”，请输入你的“执行 SSIS 包”活动可以运行的最长时间。 默认值为 7 天，格式为 D.HH:MM:SS。

   1. 对于“重试”，请输入你的“执行 SSIS 包”活动的最大重试次数。

   1. 对于“重试间隔”，请输入你的“执行 SSIS 包”活动的每次重试之间的秒数。 默认值为 30 秒。

   1. 选中“安全输出”复选框，以选择是否要从日志记录中排除“执行 SSIS 包”活动的输出。

   1. 选中“安全输入”复选框，以选择是否要从日志记录中排除“执行 SSIS 包”活动的输入。

### <a name="settings-tab"></a>“设置”选项卡

在“执行 SSIS 包”活动的“设置”选项卡上，完成以下步骤。

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png" alt-text="在“设置”选项卡上设置属性 - 自动":::

   1. 对于“Azure-SSIS IR”，请选择指定的 Azure-SSIS IR 以运行“执行 SSIS 包”活动。

   1. 对于“说明”，请输入你的“执行 SSIS 包”活动的说明。

   1. 选中“Windows 身份验证”复选框以选择是否要使用 Windows 身份验证来访问数据存储，例如本地的 SQL Server/文件共享，或 Azure 文件存储。
   
      如果选中此复选框，请在“域”、“用户名”和“密码”框中输入包执行凭据的值。   例如，若要访问 Azure 文件存储，则域为 `Azure`，用户名为 `<storage account name>`，密码为 `<storage account key>`。

      另外，还可以将 Azure Key Vault 中存储的机密用作其值。 为此，请选中它们旁边的“AZURE KEY VAULT”复选框。 选择或编辑现有的 Key Vault 链接服务，或创建新的链接服务。 然后，针对你的值选择机密名称和版本。 创建或编辑 Key Vault 链接服务时，可以选择或编辑现有的 Key Vault，或创建新的 Key Vault。 请务必授予数据工厂托管标识对 Key Vault 的访问权限（如果尚未这样做）。 此外，还可以采用以下格式直接输入机密：`<key vault linked service name>/<secret name>/<secret version>`。
      
   1. 选中“32 位运行时”复选框以选择你的包是否需要 32 位运行时才能运行。

   1. 对于“包位置”，请选择“SSISDB”、“文件系统(包)”、“文件系统(项目)”、“嵌入包”或“包存储”。      

#### <a name="package-location-ssisdb"></a>包位置：SSISDB

如果为 Azure-SSIS IR 预配了 Azure SQL 数据库服务器/托管实例托管的 SSIS 目录 (SSISDB)，则会自动选择“SSISDB”作为包位置，否则，你可以自己选择位置。 如果选择了该位置，请完成以下步骤。

   1. 如果 Azure-SSIS IR 正在运行且“手动输入内容”复选框处于已清除状态，可以从 SSISDB 浏览并选择现有的文件夹、项目、包和环境。 选择“刷新”以从 SSISDB 获取新添加的文件夹、项目、包或环境，以便可以浏览和选择这些内容。 若要浏览并选择用于包执行的环境，必须事先配置项目，以便从 SSISDB 下的相同文件夹中添加这些环境作为引用。 有关详细信息，请参阅[创建和映射 SSIS 环境](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages)。

   1. 对于“日志记录级别”，请为包执行选择预定义的日志记录范围。 如果要改为输入自定义日志记录名称，请选中“自定义”复选框。 

   1. 如果 Azure-SSIS IR 未在运行或“手动输入内容”复选框处于选中状态，请采用以下格式直接在 SSISDB 中输入你的包和环境路径：`<folder name>/<project name>/<package name>.dtsx` 和 `<folder name>/<environment name>`。

      :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png" alt-text="在“设置”选项卡上设置属性 - 手动":::

#### <a name="package-location-file-system-package"></a>包位置：文件系统(包)

如果没有为 Azure-SSIS IR 预配 SSISDB，则会自动选择“文件系统(包)”作为包位置，否则，你可以自己选择它。 如果选择了该位置，请完成以下步骤。

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png" alt-text="在“设置”选项卡上设置属性 - 文件系统(包)":::
   
   1. 指定要运行的包，方法是在“包路径”框中提供包文件（带扩展名 `.dtsx`）的通用命名约定 (UNC) 路径。 你可以通过选择“浏览文件存储”来浏览并选择包，也可以手动输入其路径。 例如，如果你将包存储在 Azure 文件存储中，则其路径为 `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`。 
   
   1. 如果在单独的文件中配置包，则还需要在“配置路径”框中提供配置文件（带扩展名 `.dtsConfig`）的 UNC 路径。 你可以通过选择“浏览文件存储”来浏览并选择配置，也可以手动输入其路径。 例如，如果将配置存储在 Azure 文件存储中，则其路径为 `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`。

   1. 指定用于访问你的包和配置文件的凭据。 如果你先前已输入包执行凭据的值（对于 **Windows 身份验证**），则可以通过选中“与包执行凭据相同”复选框来重复使用这些值。 否则，请在“域”、“用户名”和“密码”框中输入包访问凭据的值。   例如，如果你将包和配置存储在 Azure 文件存储中，则域为 `Azure`，用户名为 `<storage account name>`，密码为 `<storage account key>`。 

      另外，还可以将 Azure Key Vault 中存储的机密用作其值。 为此，请选中它们旁边的“AZURE KEY VAULT”复选框。 选择或编辑现有的 Key Vault 链接服务，或创建新的链接服务。 然后，针对你的值选择机密名称和版本。 创建或编辑 Key Vault 链接服务时，可以选择或编辑现有的 Key Vault，或创建新的 Key Vault。 请务必授予数据工厂托管标识对 Key Vault 的访问权限（如果尚未这样做）。 此外，还可以采用以下格式直接输入机密：`<key vault linked service name>/<secret name>/<secret version>`。 

      这些凭据还用于访问“执行包任务”中通过其自己的路径和包中指定的其他配置引用的子包。 

   1. 如果在通过 SQL Server Data Tools (SSDT) 创建包时使用了 **EncryptAllWithPassword** 或 **EncryptSensitiveWithPassword** 保护级别，请在“加密密码”框中输入密码的值。 另外，还可以将 Azure Key Vault 中存储的机密用作其值（参阅上文）。
      
      如果使用了 **EncryptSensitiveWithUserKey** 保护级别，请在配置文件中或在“SSIS 参数”、“连接管理器”或“属性替代”选项卡上重新输入敏感值（参阅下文）。  
      
      不支持使用 **EncryptAllWithUserKey** 保护级别。 需要通过 SSDT 或 `dtutil` 命令行实用程序将包重新配置为使用另一保护级别。 

   1. 对于“日志记录级别”，请为包执行选择预定义的日志记录范围。 如果要改为输入自定义日志记录名称，请选中“自定义”复选框。 
   
   1. 若要记录包执行但不使用可在包中指定的标准日志提供程序，请通过在“日志记录路径”框中提供其 UNC 路径来指定日志文件夹。 你可以通过选择“浏览文件存储”来浏览并选择日志文件夹，也可以手动输入其路径。 例如，如果将日志存储在 Azure 文件存储中，则日志记录路径为 `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`。 将在此路径中为每个包运行创建一个与“执行 SSIS 包”活动运行 ID 同名的子文件夹，每隔 5 分钟在其中生成一次日志文件。 
   
   1. 指定用于访问日志文件夹的凭据。 如果先前已输入包访问凭据的值（参阅上文），则可以通过选中“与包访问凭据相同”复选框来重复使用这些值。 否则，请在“域”、“用户名”和“密码”框中输入日志记录访问凭据的值。   例如，如果将日志存储在 Azure 文件存储中，则域为 `Azure`，用户名为 `<storage account name>`，密码为 `<storage account key>`。 另外，还可以将 Azure Key Vault 中存储的机密用作其值（参阅上文）。
   
对于上述所有 UNC 路径，完全限定的文件名必须短于 260 个字符。 目录名称必须短于 248 个字符。

#### <a name="package-location-file-system-project"></a>包位置：文件系统(项目)

如果选择“文件系统(项目)”作为包位置，请完成以下步骤。

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png" alt-text="在“设置”选项卡上设置属性 - 文件系统(项目)":::

   1. 通过在“项目路径”框中提供项目文件（带 `.ispac` 扩展名）的 UNC 路径，并在“包名称”框中提供项目中某个包文件（带 `.dtsx` 扩展名）的 UNC 路径，来指定要运行的包。  你可以通过选择“浏览文件存储”来浏览并选择项目，也可以手动输入其路径。 例如，如果将项目存储在 Azure 文件存储中，则其路径为 `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`。

   1. 指定用于访问项目和包文件的凭据。 如果你先前已输入包执行凭据的值（对于 **Windows 身份验证**），则可以通过选中“与包执行凭据相同”复选框来重复使用这些值。 否则，请在“域”、“用户名”和“密码”框中输入包访问凭据的值。   例如，如果你将项目和包存储在 Azure 文件存储中，则域为 `Azure`，用户名为 `<storage account name>`，密码为 `<storage account key>`。 

      另外，还可以将 Azure Key Vault 中存储的机密用作其值。 为此，请选中它们旁边的“AZURE KEY VAULT”复选框。 选择或编辑现有的 Key Vault 链接服务，或创建新的链接服务。 然后，针对你的值选择机密名称和版本。 创建或编辑 Key Vault 链接服务时，可以选择或编辑现有的 Key Vault，或创建新的 Key Vault。 请务必授予数据工厂托管标识对 Key Vault 的访问权限（如果尚未这样做）。 此外，还可以采用以下格式直接输入机密：`<key vault linked service name>/<secret name>/<secret version>`。 

      这些凭据还用于访问“执行包任务”中在同一项目中引用的子包。 

   1. 如果在通过 SSDT 创建包时使用了 **EncryptAllWithPassword** 或 **EncryptSensitiveWithPassword** 保护级别，请在“加密密码”框中输入密码的值。 另外，还可以将 Azure Key Vault 中存储的机密用作其值（参阅上文）。
      
      如果使用了 **EncryptSensitiveWithUserKey** 保护级别，请在“SSIS 参数”、“连接管理器”或“属性替代”选项卡上重新输入敏感值（参阅下文）。  
      
      不支持使用 **EncryptAllWithUserKey** 保护级别。 需要通过 SSDT 或 `dtutil` 命令行实用程序将包重新配置为使用另一保护级别。 

   1. 对于“日志记录级别”，请为包执行选择预定义的日志记录范围。 如果要改为输入自定义日志记录名称，请选中“自定义”复选框。 
   
   1. 若要记录包执行但不使用可在包中指定的标准日志提供程序，请通过在“日志记录路径”框中提供其 UNC 路径来指定日志文件夹。 你可以通过选择“浏览文件存储”来浏览并选择日志文件夹，也可以手动输入其路径。 例如，如果将日志存储在 Azure 文件存储中，则日志记录路径为 `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`。 将在此路径中为每个包运行创建一个与“执行 SSIS 包”活动运行 ID 同名的子文件夹，每隔 5 分钟在其中生成一次日志文件。 
   
   1. 指定用于访问日志文件夹的凭据。 如果先前已输入包访问凭据的值（参阅上文），则可以通过选中“与包访问凭据相同”复选框来重复使用这些值。 否则，请在“域”、“用户名”和“密码”框中输入日志记录访问凭据的值。   例如，如果将日志存储在 Azure 文件存储中，则域为 `Azure`，用户名为 `<storage account name>`，密码为 `<storage account key>`。 另外，还可以将 Azure Key Vault 中存储的机密用作其值（参阅上文）。
   
对于上述所有 UNC 路径，完全限定的文件名必须短于 260 个字符。 目录名称必须短于 248 个字符。

#### <a name="package-location-embedded-package"></a>包位置：嵌入包

如果选择“嵌入包”作为包位置，请完成以下步骤。

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png" alt-text="在“设置”选项卡上设置属性 - 嵌入包":::

   1. 拖放你的包文件（带有 `.dtsx`），或将其从文件夹“上传”到提供的框中。 包会自动压缩并嵌入到活动有效负载中。 嵌入后，可以“下载”包供以后编辑。 你还可以通过将嵌入包分配给可在多个活动中使用的管道参数来 **参数化** 它，从而优化管道有效负载的大小。 当前不支持嵌入项目文件（带有 `.ispac`），因此无法在嵌入包中将 SSIS 参数/连接管理器与项目级范围一起使用。
   
   1. 如果嵌入包不是全部加密的，并且我们检测到其中使用了“执行包任务 (EPT)”，则系统会自动选中“执行包任务”复选框，并自动添加通过其文件系统路径引用的子包，以便你也可嵌入它们。
   
      如果无法检测到 EPT 的使用，则需要手动选中“执行包任务”复选框，并添加通过其文件系统路径逐个引用的子包，以便你也可以嵌入它们。 如果子包存储在 SQL Server 数据库 (MSDB) 中，则无法嵌入它们，因此需要确保 Azure-SSIS IR 可以访问 MSDB，以便使用其 SQL Server 引用提取它们。 当前不支持嵌入项目文件（带有 `.ispac`），因此无法对子包使用基于项目的引用。
   
   1. 如果在通过 SSDT 创建包时使用了 **EncryptAllWithPassword** 或 **EncryptSensitiveWithPassword** 保护级别，请在“加密密码”框中输入密码的值。 
   
      另外，还可以将 Azure Key Vault 中存储的机密用作其值。 为此，请选中其旁边的“AZURE KEY VAULT”复选框。 选择或编辑现有的 Key Vault 链接服务，或创建新的链接服务。 然后，针对你的值选择机密名称和版本。 创建或编辑 Key Vault 链接服务时，可以选择或编辑现有的 Key Vault，或创建新的 Key Vault。 请务必授予数据工厂托管标识对 Key Vault 的访问权限（如果尚未这样做）。 此外，还可以采用以下格式直接输入机密：`<key vault linked service name>/<secret name>/<secret version>`。
      
      如果使用了 **EncryptSensitiveWithUserKey** 保护级别，请在配置文件中或在“SSIS 参数”、“连接管理器”或“属性替代”选项卡上重新输入敏感值（参阅下文）。  
      
      不支持使用 **EncryptAllWithUserKey** 保护级别。 需要通过 SSDT 或 `dtutil` 命令行实用程序将包重新配置为使用另一保护级别。

   1. 对于“日志记录级别”，请为包执行选择预定义的日志记录范围。 如果要改为输入自定义日志记录名称，请选中“自定义”复选框。 
   
   1. 若要记录包执行但不使用可在包中指定的标准日志提供程序，请通过在“日志记录路径”框中提供其 UNC 路径来指定日志文件夹。 你可以通过选择“浏览文件存储”来浏览并选择日志文件夹，也可以手动输入其路径。 例如，如果将日志存储在 Azure 文件存储中，则日志记录路径为 `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`。 将在此路径中为每个包运行创建一个与“执行 SSIS 包”活动运行 ID 同名的子文件夹，每隔 5 分钟在其中生成一次日志文件。 
   
   1. 通过在“域”、“用户名”和“密码”框中输入值来指定用于访问日志文件夹的凭据。 例如，如果将日志存储在 Azure 文件存储中，则域为 `Azure`，用户名为 `<storage account name>`，密码为 `<storage account key>`。 另外，还可以将 Azure Key Vault 中存储的机密用作其值（参阅上文）。
   
对于上述所有 UNC 路径，完全限定的文件名必须短于 260 个字符。 目录名称必须短于 248 个字符。

#### <a name="package-location-package-store"></a>包位置：包存储

如果选择“包存储”作为包位置，请完成以下步骤。

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings6.png" alt-text="在“设置”选项卡上设置属性 - 包存储":::
   
   1. 对于“包存储名称”，请选择附加到你的 Azure-SSIS IR 的一个现有包存储。

   1. 通过在“包路径”框中提供你的包在所选包存储中的路径（不带 `.dtsx` 扩展名），指定要运行的包。 如果所选包存储位于文件系统/Azure 文件存储上，则可以通过选择“浏览文件存储”来浏览并选择包，否则，可以采用 `<folder name>\<package name>` 格式输入其路径。 还可以通过 SQL Server Management Studio (SSMS) 将新包导入到所选的包存储中，这与[旧版 SSIS 包存储](/sql/integration-services/service/package-management-ssis-service)类似。 有关详细信息，请参阅[使用 Azure-SSIS IR 包存储管理 SSIS 包](./azure-ssis-integration-runtime-package-store.md)。

   1. 如果在单独的文件中配置包，则需要在“配置路径”框中提供配置文件（带扩展名 `.dtsConfig`）的 UNC 路径。 你可以通过选择“浏览文件存储”来浏览并选择配置，也可以手动输入其路径。 例如，如果将配置存储在 Azure 文件存储中，则其路径为 `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`。

   1. 选中“配置访问凭据”复选框，以选择是否要单独指定用于访问配置文件的凭据。 当所选的包存储位于由 Azure SQL 托管实例承载的 SQL Server 数据库 (MSDB) 上或未同时存储你的配置文件时，需要进行此配置。
   
      如果你先前已输入包执行凭据的值（对于 **Windows 身份验证**），则可以通过选中“与包执行凭据相同”复选框来重复使用这些值。 否则，请在“域”、“用户名”和“密码”框中输入配置访问凭据的值。   例如，如果你将配置存储在 Azure 文件存储中，则域为 `Azure`，用户名为 `<storage account name>`，密码为 `<storage account key>`。 

      另外，还可以将 Azure Key Vault 中存储的机密用作其值。 为此，请选中它们旁边的“AZURE KEY VAULT”复选框。 选择或编辑现有的 Key Vault 链接服务，或创建新的链接服务。 然后，针对你的值选择机密名称和版本。 创建或编辑 Key Vault 链接服务时，可以选择或编辑现有的 Key Vault，或创建新的 Key Vault。 请务必授予数据工厂托管标识对 Key Vault 的访问权限（如果尚未这样做）。 此外，还可以采用以下格式直接输入机密：`<key vault linked service name>/<secret name>/<secret version>`。

   1. 如果在通过 SSDT 创建包时使用了 **EncryptAllWithPassword** 或 **EncryptSensitiveWithPassword** 保护级别，请在“加密密码”框中输入密码的值。 另外，还可以将 Azure Key Vault 中存储的机密用作其值（参阅上文）。
      
      如果使用了 **EncryptSensitiveWithUserKey** 保护级别，请在配置文件中或在“SSIS 参数”、“连接管理器”或“属性替代”选项卡上重新输入敏感值（参阅下文）。  
      
      不支持使用 **EncryptAllWithUserKey** 保护级别。 需要通过 SSDT 或 `dtutil` 命令行实用程序将包重新配置为使用另一保护级别。 

   1. 对于“日志记录级别”，请为包执行选择预定义的日志记录范围。 如果要改为输入自定义日志记录名称，请选中“自定义”复选框。 
   
   1. 若要记录包执行但不使用可在包中指定的标准日志提供程序，请通过在“日志记录路径”框中提供其 UNC 路径来指定日志文件夹。 你可以通过选择“浏览文件存储”来浏览并选择日志文件夹，也可以手动输入其路径。 例如，如果将日志存储在 Azure 文件存储中，则日志记录路径为 `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`。 将在此路径中为每个包运行创建一个与“执行 SSIS 包”活动运行 ID 同名的子文件夹，每隔 5 分钟在其中生成一次日志文件。 
   
   1. 通过在“域”、“用户名”和“密码”框中输入值来指定用于访问日志文件夹的凭据。 例如，如果将日志存储在 Azure 文件存储中，则域为 `Azure`，用户名为 `<storage account name>`，密码为 `<storage account key>`。 另外，还可以将 Azure Key Vault 中存储的机密用作其值（参阅上文）。
   
对于上述所有 UNC 路径，完全限定的文件名必须短于 260 个字符。 目录名称必须短于 248 个字符。

### <a name="ssis-parameters-tab"></a>“SSIS 参数”选项卡

在“执行 SSIS 包”活动的“SSIS 参数”选项卡上，完成以下步骤。

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png" alt-text="在“SSIS 参数”选项卡上设置属性":::

   1. 如果 Azure-SSIS IR 正在运行，而你已选择“SSISDB”作为包位置，并已清除“设置”选项卡上的“手动输入内容”复选框，则会显示 SSISDB 中选定项目和包中现有的 SSIS 参数，方便你为它们赋值  。 否则，可以逐个输入以便手动为它们赋值。 为了使包成功执行，请确保它们存在并已正确输入。 
   
   1. 如果通过 SSDT 创建包时使用了 **EncryptSensitiveWithUserKey** 保护级别，并且选择了“文件系统(包)”、“文件系统(项目)”、“嵌入包”或“包存储”作为包位置，则还需要重新输入敏感参数，以便在此选项卡上为它们赋值。    
   
为参数赋值时，可以使用表达式、函数、数据工厂系统变量和数据工厂管道参数或变量添加动态内容。

另外，还可以将 Azure Key Vault 中存储的机密用作其值。 为此，请选中它们旁边的“AZURE KEY VAULT”复选框。 选择或编辑现有的 Key Vault 链接服务，或创建新的链接服务。 然后，针对你的值选择机密名称和版本。 创建或编辑 Key Vault 链接服务时，可以选择或编辑现有的 Key Vault，或创建新的 Key Vault。 请务必授予数据工厂托管标识对 Key Vault 的访问权限（如果尚未这样做）。 此外，还可以采用以下格式直接输入机密：`<key vault linked service name>/<secret name>/<secret version>`。 

### <a name="connection-managers-tab"></a>“连接管理器”选项卡

在“执行 SSIS 包”活动的“连接管理器”选项卡上，完成以下步骤。

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png" alt-text="在“连接管理器”选项卡上设置属性":::

   1. 如果 Azure-SSIS IR 正在运行，而你已选择“SSISDB”作为包位置，并已清除“设置”选项卡上的“手动输入内容”复选框，则会显示 SSISDB 中选定项目和包中现有的连接管理器，方便你为它们的属性赋值  。 否则，可以逐个输入以便手动为其属性赋值。 为了使包成功执行，请确保它们存在并已正确输入。 
   
      可以获取任何连接管理器的正确 SCOPE、NAME 和 PROPERTY 名称，具体方法是在 SSDT 上打开包含相应连接管理器的包。 打开包之后，选择相关连接管理器，以在 SSDT 的“属性”窗口中显示其所有属性的名称和值。 利用此信息，可以在运行时重写任何连接管理器属性的值。 

      :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssdt-connection-manager-properties.png" alt-text="从 SSDT 获取连接管理器属性":::

      例如，在不修改 SSDT 上的原始包的情况下，可以通过在运行时覆盖现有连接管理器中的 ConnectByProxy、ConnectionString 和 ConnectUsingManagedIdentity 属性的值，将其在 SQL Server 上运行的本地到本地数据流转换为在 ADF 中的 SSIS IR 上运行的本地到云数据流  。
      
      这些运行时重写可以在本地访问数据时启用自承载 IR (SHIR) 作为 SSIS IR 的代理（请参阅[将 SHIR 配置为 SSIS IR 的代理](./self-hosted-integration-runtime-proxy-ssis.md)），以及启用使用最新 MSOLEDBSQL 驱动程序、进而允许使用 ADF 托管标识进行 Azure Active Directory (AAD) 身份验证的 Azure SQL 数据库/托管实例连接（请参阅[为 OLEDB 连接配置使用 ADF 托管标识的 AAD 身份验证](/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)）。

      :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers2.png" alt-text="在“连接管理器”选项卡上通过 SSDT 设置属性":::
   
   1. 如果通过 SSDT 创建包时使用了 **EncryptSensitiveWithUserKey** 保护级别，并且选择了“文件系统(包)”、“文件系统(项目)”、“嵌入包”或“包存储”作为包位置，则还需要重新输入敏感的连接管理器属性，以便在此选项卡上为它们赋值。    

为连接管理器属性赋值时，可以使用表达式、函数、数据工厂系统变量和数据工厂管道参数或变量添加动态内容。 

另外，还可以将 Azure Key Vault 中存储的机密用作其值。 为此，请选中它们旁边的“AZURE KEY VAULT”复选框。 选择或编辑现有的 Key Vault 链接服务，或创建新的链接服务。 然后，针对你的值选择机密名称和版本。 创建或编辑 Key Vault 链接服务时，可以选择或编辑现有的 Key Vault，或创建新的 Key Vault。 请务必授予数据工厂托管标识对 Key Vault 的访问权限（如果尚未这样做）。 此外，还可以采用以下格式直接输入机密：`<key vault linked service name>/<secret name>/<secret version>`。 

### <a name="property-overrides-tab"></a>“属性替代”选项卡

在“执行 SSIS 包”活动的“属性替代”选项卡上，完成以下步骤。

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png" alt-text="在“属性替代”选项卡上设置属性":::

   1. 逐个输入选定包的现有属性的路径，以便手动为其赋值。 为了使包成功执行，请确保它们存在并已正确输入。 例如，若要替代用户变量的值，请按以下格式输入其路径：`\Package.Variables[User::<variable name>].Value`。 

      可以通过在 SSDT 上打开包含任何包属性的包，获取相应包属性的正确 PROPERTY PATH。 打开包之后，在 SSDT 的“属性”窗口中选择其控制流和“配置”属性。 接下来，选择其“配置”属性旁的省略号 (...) 按钮，以打开“包配置组织程序”，它通常用于[在包部署模型中创建包配置](/sql/integration-services/packages/legacy-package-deployment-ssis#create-package-configurations)。 

      :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties.png" alt-text="从 SSDT 获取包属性 - 配置属性":::

      在“包配置组织程序”中，选中“启用包配置”复选框和“添加...”按钮以打开“包配置向导”。 
      
      在“包配置向导”上，在“配置类型”下拉菜单中选择“XML 配置文件”项，选择“直接指定配置设置”按钮，输入配置文件名，然后选择“下一步 >”按钮。 

      :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties2.png" alt-text="从 SSDT 获取包属性 - 配置组织程序":::

      最后，选择需要其路径的包属性，然后选择“下一步 >”按钮。  现在可以查看、复制和粘贴所需的包属性路径，并将它们保存在配置文件中。 利用此信息，可以在运行时重写任何包属性的值。 

      :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties3.png" alt-text="从 SSDT 获取包属性 - 配置向导":::
   
   1. 如果通过 SSDT 创建包时使用了 **EncryptSensitiveWithUserKey** 保护级别，并且选择了“文件系统(包)”、“文件系统(项目)”、“嵌入包”或“包存储”作为包位置，则还需要重新输入敏感的包属性，以便在此选项卡上为它们赋值。    
   
为包属性赋值时，可以使用表达式、函数、数据工厂系统变量和数据工厂管道参数或变量添加动态内容。

可以使用“连接管理器”或“属性替代”选项卡替代配置文件中和“SSIS 参数”选项卡上的赋值。   还可以使用“属性替代”选项卡替代“连接管理器”选项卡上的赋值。 

若要验证管道配置，请在工具栏中选择“验证”。 若要关闭“管道验证报告”，请选择 **>>** 。

若要将管道发布到数据工厂，请选择“全部发布”。 

## <a name="run-the-pipeline"></a>运行管道
在此步骤中，将触发管道运行。 

1. 若要触发某个管道运行，请在工具栏中选择“触发器”，然后选择“立即触发”。 

   :::image type="content" source="./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png" alt-text="立即触发":::

2. 在“管道运行”窗口中选择“完成”。  

## <a name="monitor-the-pipeline"></a>监视管道

1. 在左侧切换到“监视”选项卡。 随即显示管道运行及其状态和其他信息（例如“运行开始”时间）。 若要刷新视图，请选择“刷新”。

   :::image type="content" source="./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png" alt-text="管道运行":::

2. 在“操作”列中选择“查看活动运行”链接。  此时只显示一个活动运行，因为管道只有一个活动。 该活动为“执行 SSIS 包”活动。

   :::image type="content" source="./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png" alt-text="活动运行":::

3. 在 SQL Server 中针对 SSISDB 数据库运行以下查询，验证是否执行了该包。 

   ```sql
   select * from catalog.executions
   ```

   :::image type="content" source="./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png" alt-text="验证包执行":::

4. 还可以从管道活动运行的输出中获取 SSISDB 执行 ID，并使用此 ID 在 SQL Server Management Studio 中查看更全面的执行日志和错误消息。

   :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png" alt-text="获取执行 ID。":::

## <a name="schedule-the-pipeline-with-a-trigger"></a>使用触发器计划管道

还可以为管道创建一个计划触发器，以便按计划（例如每小时或每天）运行管道。 有关示例，请参阅[创建数据工厂 - 数据工厂 UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)。

## <a name="next-steps"></a>后续步骤
- [通过 PowerShell 在 Azure 数据工厂中使用“执行 SSIS 包”活动运行 SSIS 包](how-to-invoke-ssis-package-ssis-activity-powershell.md)
- [使用 Azure 数据工厂管道中的 SSIS 活动来实现 ETL/ELT 工作流的现代化并对其进行扩展](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)