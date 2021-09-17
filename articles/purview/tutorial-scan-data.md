---
title: 教程：使用 Azure Purview（预览版）扫描数据
description: 在本教程中，你将运行初学者工具包来设置数据属性，然后将数据源中的数据扫描到 Azure Purview 目录中。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 723136a47dec007d16a6ab1a8be0400dbb34ae8c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736348"
---
# <a name="tutorial-scan-data-with-azure-purview-preview"></a>教程：使用 Azure Purview（预览版）扫描数据

> [!IMPORTANT]
> Azure Purview 当前以预览版提供。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

在“五部分教程系列”中，你将了解如何使用 Azure Purview（预览版）跨数据资产管理数据治理的基础知识。 在本教程的此部分中创建的数据状态将用于本系列的其余部分。

在本教程系列的第 1 部分中，你将执行以下操作：

> [!div class="checklist"]
>
> * 使用各种 Azure 数据资源创建数据属性。
> * 将数据扫描到目录中。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* [Azure Purview 帐户](create-catalog-portal.md)。
* 将部署数据资产的[初学者工具包](https://github.com/Azure/Purview-Samples/blob/master/PurviewStarterKitV4.zip)。

> [!NOTE]
> 初学者工具包仅适用于 Windows。

## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-data-estate"></a>创建数据资产

本部分将运行初学者工具包脚本来创建一个模拟的数据资产。 数据资产是公司拥有的所有数据的组合。 初学者工具包脚本可执行以下操作：

* 创建 Azure Blob 存储帐户并用数据填充该帐户。
* 创建 Azure Data Lake Storage Gen2 帐户。
* 创建 Azure 数据工厂实例并将该实例与 Azure Purview 关联。
* 在 Azure Blob 存储和 Azure Data Lake Storage Gen2 帐户之间设置并触发复制活动管道。
* 将关联的世系从 Azure 数据工厂推送到 Azure Purview。

### <a name="prepare-to-run-the-starter-kit"></a>准备运行初学者工具包

按照以下步骤在 Windows 计算机上设置初学者工具包客户端软件：

1. [下载初学者工具包](https://github.com/Azure/Purview-Samples/blob/master/PurviewStarterKitV4.zip)，并将其内容提取到所选择的位置。


1. 在计算机上，在 Windows 任务栏的搜索框中输入 PowerShell。 在搜索列表中，右键单击“Windows PowerShell”，然后选择“以管理员身份运行” 。

1. 在 PowerShell 窗口中，输入以下命令，将 `<path-to-starter-kit>` 替换为提取的初学者工具包文件的文件夹路径。

   ```powershell
   dir -Path <path-to-starter-kit> -Recurse | Unblock-File
   ```

1. 输入以下命令来安装 Azure cmdlet。

   ```powershell
   Install-Module -Name Az -AllowClobber -Scope CurrentUser
   ```

1. 如果看到警告提示“需要 NuGet 提供程序才能继续”，请输入“Y”，然后按 Enter。

1. 如果看到警告提示“不受信任的存储库”，请输入“A”，然后按 Enter。

PowerShell 安装所需的模块可能最多需要一分钟时间。

### <a name="collect-data-needed-to-run-the-scripts"></a>收集运行脚本所需的数据

在运行 PowerShell 脚本以启动目录之前，请获取要在脚本中使用的以下参数值：

* TenantID
   1. 在[Azure 门户](https://portal.azure.com)中，选择“Azure Active Directory”。
   1. 在左侧导航窗格的“管理”部分中，选择“属性” 。 然后选择“租户 ID”的复制图标，将值保存到剪贴板。 将值粘贴到文本编辑器中以供以后使用。

* 订阅 ID
   1. 在 Azure 门户中，搜索并选择作为先决条件创建的Azure Purview 实例的名称。
   1. 选择“概述”部分并保存“订阅 ID”的 GUID 。

   > [!NOTE]
   > - 请确保你使用的订阅与创建 Azure Purview 帐户时使用的订阅相同。 这是放置在允许列表中的同一订阅。
   > - 在运行初学者工具包后，有时在 Azure Purview 中可能会缺少世系。 这是因为由初学者工具包创建的数据工厂在 Purview 中缺少权限。 请选择[此文档链接](how-to-link-azure-data-factory.md#view-existing-data-factory-connections)，以确保在 Purview 中正确配置数据工厂并为其分配适当的角色


* CatalogName：在[创建 Azure Purview 帐户](create-catalog-portal.md)中创建的 Azure Purview 帐户的名称。

* CatalogResourceGroupName：在其中创建 Azure Purview 帐户的资源组的名称。

### <a name="verify-permissions"></a>验证权限

按照以下步骤将运行脚本的用户添加到作为先决条件创建的 Azure Purview 帐户。 用户需要“Purview Data Curator”和“Purview 数据源管理器”角色 。 

如果你自行创建了 Azure Purview 帐户，则会自动获取访问权限，并可以跳过此部分。

1. 转到 Azure 门户中的“Purview 帐户”页面，然后选择要修改的 Azure Purview 帐户。

1. 在帐户页面上，选择“访问控制 (IAM)”选项卡和“+ 添加” 。

1. 选择“添加角色分配”。

1. 为“角色”输入“Purview Data Curator 角色”。
 
1. 对“访问权限分配对象”使用默认值。 默认值应为“用户、组或服务主体”。

1. 在“选择”中输入运行脚本的用户的名称。

1. 选择“保存”。

1. 重复前面的步骤，将“角色”设置为“Purview 数据源管理器角色”。

有关详细信息，请参阅[目录权限](catalog-permissions.md)。

### <a name="run-the-client-side-setup-scripts"></a>运行客户端安装脚本

目录配置完成后，在 PowerShell 窗口中运行以下脚本以创建资产，并将占位符替换为以前收集的值。

1. 使用以下命令导航到初学者工具包目录。 将 `path-to-starter-kit` 替换为提取文件的文件夹路径。

   ```powershell
   cd <path-to-starter-kit>
   ```

1. 以下命令设置本地计算机的执行策略。 当系统提示你更改执行策略时，输入“A”表示全部更改。

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   ```

1. 使用以下命令连接到 Azure。 替换 `TenantID` 和 `SubscriptionID` 占位符。

   ```powershell
   .\RunStarterKit.ps1 -ConnectToAzure -TenantId <TenantID> `
   -SubscriptionId <SubscriptionID>
   ```

   运行该命令时，可能会出现一个弹出窗口，供你使用 Azure Active Directory 凭据登录。

    > [!TIP]
    > 如果跨租户启用 MFA，则此步骤中可能会遇到 MFA 错误。 如果遇到了 MFA 错误，请针对运行此脚本的帐户禁用 MFA。 然后再次运行。


1. 使用以下命令运行该初学者工具包。 替换 `CatalogName`、`TenantID`、`SubscriptionID`、`NewResourceGroupName` 和 `CatalogResourceGroupName` 占位符。 对于 `NewResourceGroupName`，请为将包含数据资产的资源组使用唯一的名称（仅限小写字母数字字符）。

   > [!IMPORTANT]
   > “newresourcegroupname”只使用数字和小写字母，并且必须少于 17 个字符。 **不允许使用大写字母和特殊字符。** 此约束来自存储帐户命名规则。

   ```powershell
   .\RunStarterKit.ps1 -CatalogName <CatalogName> -TenantId <TenantID>`
   -ResourceGroup <newresourcegroupname> `
   -SubscriptionId <SubscriptionID> `
   -CatalogResourceGroup <CatalogResourceGroupName>
   ```

设置环境最多需要 10 分钟。 在此期间，你可能会看到各种弹出窗口，可忽略这些窗口。 请勿关闭“BlobDataCreator.exe”窗口；当它完成时会自动关闭。

看到消息 `Executing Copy pipeline xxxxxxxxxx-487e-4fc4-9628-92dd8c2c732b` 时，请等待“BlobDataCreator.exe”的另一个实例启动并完成运行。

> [!IMPORTANT]
> 如果“活动任务数”停止减少，则可以退出 blob creator 窗口，然后在 PowerShell 窗口中按 Enter

此过程完成后，将创建一个资源组并以提供的名称命名。 Azure 数据工厂、Azure Blob 存储和 Azure Data Lake Storage Gen2 帐户都包含在此资源组中。 此资源组包含在指定的订阅中。

## <a name="scan-data-into-the-catalog"></a>将数据扫描到目录中

扫描过程是指目录按照用户指定的计划直接连接到数据源。 目录通过扫描、世系、门户和 API 反映公司的数据状态。 目标包括检查内部内容、提取架构和尝试理解语义。 本部分将设置对使用初学者工具包生成的数据源的扫描。

运行的初学者工具包脚本创建了两个数据源：Azure Blob 存储和 Azure Data Lake Storage Gen2。 你可以一次将这些数据源扫描到目录中。

### <a name="authenticate-to-your-storage-with-managed-identity"></a>使用托管标识对存储进行身份验证

创建帐户时，将自动创建与 Azure Purview 帐户同名的托管标识。 在扫描数据之前，需要为存储帐户授予 Azure Purview 角色权限。

1. 导航到由初学者工具包创建的资源组，然后选择 blob 存储帐户。

1. 在左侧导航栏菜单中，选择“访问控制 (IAM)”。 然后选择“+ 添加”。

1. 将“角色”设置为“存储 Blob 数据读取者”，然后在“选择”中输入 Azure Purview 帐户名称 。 然后，选择“保存”，将此角色分配给 Purview 帐户。

   :::image type="content" source="media/tutorial-scan-data/add-role-assignment.png" alt-text="添加角色分配":::

1. 对 Azure Data Lake Storage Gen2 重复前面的步骤。

### <a name="scan-your-data-sources"></a>扫描数据源

1. 在 [Azure 门户](https://portal.azure.com)中，搜索并选择作为先决条件创建的 Azure Purview 实例的名称，然后选择“打开 Purview Studio”。 你将自动转到 Purview Studio 的主页。

1. 在左侧导航区域中选择“数据映射”，然后选择“注册” 。 然后依次选择“Azure Blob 存储”、“继续” 。

   :::image type="content" source="media/tutorial-scan-data/add-blob-storage.png" alt-text="注册 blob 存储资源":::

1. 在“注册源”页面上，输入“名称” 。 选择先前使用初学者工具包创建的 Azure Blob 存储帐户的“存储帐户名称”。 帐户的名称为 `<YourResourceGroupName>adcblob`。 选择“完成”。

   :::image type="content" source="./media/tutorial-scan-data/register-azure-blob-storage.png" alt-text="显示注册 Azure Blob 存储数据源的设置的屏幕截图。" border="true":::

1. 在“数据源”映射视图上，选择 Azure Blob 存储磁贴上的“新建扫描” 。

   :::image type="content" source="./media/tutorial-scan-data/select-setup-scan.png" alt-text="显示如何从数据源选择扫描设置的屏幕截图。" border="true":::

1. 在“扫描”页面上，输入扫描名称，从“凭证”下拉列表中选择托管标识，然后选择“继续”  。

   :::image type="content" source="media/tutorial-scan-data/scan-blob.png" alt-text="在 Azure Purview 中扫描 blob 存储":::

1. 可以将扫描范围限定为单个 blob。 在本教程中，检查所有资产以扫描所有内容并“继续”。

   :::image type="content" source="media/tutorial-scan-data/scope-your-scan.png" alt-text="限定存储扫描范围":::

1. 选择默认扫描规则集，再选择“继续”。

1. 可以为定期扫描设置扫描触发器。 对于本教程，请选择“一次”和“继续” 。

1. 选择“保存并运行”完成扫描。

1. 重复前面的步骤扫描 Azure Data Lake Storage Gen2 帐户。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：
> [!div class="checklist"]
>
> * 运行初学者工具包脚本以设置数据状态环境。
> * 将数据扫描到 Azure Purview 目录中。

进入下一个教程，了解如何在主页中导航并搜索资产。

> [!div class="nextstepaction"]
> [在主页中导航并搜索资产](tutorial-asset-search.md)
