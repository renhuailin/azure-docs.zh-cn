---
title: 使用 PowerShell 创建共享自承载集成运行时
description: 了解如何在 Azure 数据工厂中创建共享自承载集成运行时，从而允许多个数据工厂访问集成运行时。
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
ms.author: lle
author: lrtoyou1223
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.date: 06/10/2020
ms.openlocfilehash: b59655f343c8b5aa279dae9197c1d7fa41e0c318
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733076"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>在 Azure 数据工厂中创建共享自承载集成运行时

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本指南介绍如何在 Azure 数据工厂中创建共享自承载集成运行时。 然后可在另一个数据工厂中使用共享自托管集成运行时。

## <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>在 Azure 数据工厂中创建共享自承载集成运行时

可以重复使用已在数据工厂中设置的现有自承载集成运行时基础结构。 这种重复使用可以通过引用现有的共享自承载 IR，在不同的数据工厂中创建链接的自承载集成运行时。

有关此功能的介绍和演示，请观看以下时长为 12 分钟的视频：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>术语

- **共享 IR**：在物理基础结构上运行的原始自承载 IR。  
- **链接 IR**：引用另一个共享 IR 的 IR。 链接的 IR 是一个逻辑 IR，它使用另一个共享自承载 IR 的基础结构。

## <a name="create-a-shared-self-hosted-ir-using-azure-data-factory-ui"></a>使用 Azure 数据工厂 UI 创建共享自承载 IR

若要使用 Azure 数据工厂 UI 创建共享自承载 IR，可以执行以下步骤：

1. 在要共享的自承载 IR 中，选择“向另一个数据工厂授予权限”，然后在“集成运行时设置”页中，选择要在其中创建链接 IR 的数据工厂。
      
    ![“共享”选项卡上的授予权限按钮](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)  
    
2. 记下并复制上面要共享的自承载 IR 的“资源 ID”。
         
3. 在已授予权限的数据工厂中，创建新的自承载 IR（链接），并输入资源 ID。
      
    ![用于创建自承载集成运行时的按钮](media/create-self-hosted-integration-runtime/create-linkedir-1.png)
   
    ![用于创建链接的自承载集成运行时的按钮](media/create-self-hosted-integration-runtime/create-linkedir-2.png) 

    ![用于输入名称和资源 ID 的框](media/create-self-hosted-integration-runtime/create-linkedir-3.png)

## <a name="create-a-shared-self-hosted-ir-using-azure-powershell"></a>使用 Azure PowerShell 创建共享自承载 IR

若要使用 Azure PowerShell 创建共享自承载 IR，可以执行以下步骤： 
1. 创建数据工厂。 
1. 创建自我托管的集成运行时。
1. 与其他数据工厂共享自承载集成运行时。
1. 创建链接的集成运行时。
1. 撤消共享。

### <a name="prerequisites"></a>先决条件 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure 订阅**。 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。 

- **Azure PowerShell**。 请遵循[使用 PowerShellGet 在 Windows 上安装 Azure PowerShell](/powershell/azure/install-az-ps) 中的说明。 可使用 PowerShell 运行脚本来创建可与其他数据工厂共享的自承载集成运行时。 

> [!NOTE]  
> 若要查看目前提供数据工厂的 Azure 区域列表，请选择你感兴趣的区域：[可用产品（按区域）](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory)。

### <a name="create-a-data-factory"></a>创建数据工厂

1. 启动 Windows PowerShell 集成脚本环境 (ISE)。

1. 创建变量。 复制并粘贴以下脚本。 将变量（例如 **SubscriptionName** 和 **ResourceGroupName**）替换为实际值： 

    ```powershell
    # If input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
    $SubscriptionName = "[Azure subscription name]" 
    $ResourceGroupName = "[Azure resource group name]" 
    $DataFactoryLocation = "EastUS" 

    # Shared Self-hosted integration runtime information. This is a Data Factory compute resource for running any activities 
    # Data factory name. Must be globally unique 
    $SharedDataFactoryName = "[Shared Data factory name]" 
    $SharedIntegrationRuntimeName = "[Shared Integration Runtime Name]" 
    $SharedIntegrationRuntimeDescription = "[Description for Shared Integration Runtime]"

    # Linked integration runtime information. This is a Data Factory compute resource for running any activities
    # Data factory name. Must be globally unique
    $LinkedDataFactoryName = "[Linked Data factory name]"
    $LinkedIntegrationRuntimeName = "[Linked Integration Runtime Name]"
    $LinkedIntegrationRuntimeDescription = "[Description for Linked Integration Runtime]"
    ```

1. 登录并选择一个订阅。 在脚本中添加以下代码，以登录并选择 Azure 订阅：

    ```powershell
    Connect-AzAccount
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

1. 创建资源组和数据工厂。

    > [!NOTE]  
    > 此步骤是可选的。 若已有数据工厂，请跳过此步骤。 

    使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令创建 [Azure 资源组](../azure-resource-manager/management/overview.md)。 资源组是在其中以组的形式部署和管理 Azure 资源的逻辑容器。 以下示例在 WestEurope 位置创建名为 `myResourceGroup` 的资源组： 

    ```powershell
    New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
    ```

    运行以下命令以创建数据工厂： 

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                             -Location $DataFactoryLocation `
                             -Name $SharedDataFactoryName
    ```

### <a name="create-a-self-hosted-integration-runtime"></a>创建自承载 Integration Runtime

> [!NOTE]  
> 此步骤是可选的。 若已经拥有要与其他数据工厂共享的自承载集成运行时，请跳过此步骤。

运行以下命令以创建自承载集成运行时：

```powershell
$SharedIR = Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

#### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>获取集成运行时身份验证密钥并注册节点

运行以下命令以获取自承载集成运行时的身份验证密钥：

```powershell
Get-AzDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

响应包含此自承载集成运行时的身份验证密钥。 注册集成运行时节点时使用此密钥。

#### <a name="install-and-register-the-self-hosted-integration-runtime"></a>安装并注册自承载集成运行时

1. 从 [Azure 数据工厂集成运行时](https://aka.ms/dmg)下载自承载集成运行时安装程序。

2. 运行安装程序以在本地计算机上安装自承载集成。

3. 使用你在上一步中检索的身份验证密钥注册新的自承载集成。

### <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>与另一个数据工厂共享自承载集成运行时

#### <a name="create-another-data-factory"></a>创建另一个数据工厂

> [!NOTE]  
> 此步骤是可选的。 若已拥有要共享的数据工厂，请跳过此步骤。 但若要向/从其他数据工厂添加或删除角色分配，你必须具有 `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 权限，例如[用户访问管理员](../role-based-access-control/built-in-roles.md#user-access-administrator)或[所有者](../role-based-access-control/built-in-roles.md#owner)。

```powershell
$factory = Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
#### <a name="grant-permission"></a>授予权限

向需要访问你创建和注册的自承载集成运行时的数据工厂授予权限。

> [!IMPORTANT]  
> 请勿跳过此步骤！

```powershell
New-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

### <a name="create-a-linked-self-hosted-integration-runtime"></a>创建链接的自承载集成运行时

运行以下命令以创建链接的自承载集成运行时：

```powershell
Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

现可在任何链接服务中使用此链接的集成运行时。 链接的集成运行时使用共享集成运行时来运行活动。

### <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>撤消数据工厂中的集成运行时共享

若要从共享集成运行时中撤销数据工厂的访问权限，请运行以下命令：

```powershell
Remove-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

若要删除现有的链接集成运行时，请对共享集成运行时运行以下命令：

```powershell
Remove-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

### <a name="monitoring"></a>监视

#### <a name="shared-ir"></a>共享 IR

![用于查找共享集成运行时的选项](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

![监视共享的集成运行时](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

#### <a name="linked-ir"></a>链接 IR

![用于查找链接集成运行时的选项](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

![监视链接的集成运行时](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)


### <a name="known-limitations-of-self-hosted-ir-sharing"></a>自承载 IR 共享的已知限制

* 要在其中创建链接 IR 的数据工厂必须有一个[托管标识](../active-directory/managed-identities-azure-resources/overview.md)。 默认情况下，在 Azure 门户或 PowerShell cmdlet 中创建的数据工厂已隐式创建了托管标识。 但是，如果数据工厂是通过 Azure 资源管理器模板或 SDK 创建的，则必须显式设置 **Identity** 属性。 此设置确保资源管理器创建包含托管标识的数据工厂。

* 支持此功能的数据工厂 .NET SDK 必须是 1.1.0 或更高版本。

* 若要授予权限，需要在共享 IR 所在的数据工厂中拥有“所有者”角色或继承的“所有者”角色。

* 共享功能仅适用于同一 Azure AD 租户中的数据工厂。

* 对于 Azure AD [来宾用户](../active-directory/governance/manage-guest-access-with-access-reviews.md)，UI 中的搜索功能（通过使用搜索关键字列出所有数据工厂）将不起作用。 但只要来宾用户是数据工厂的所有者，你就可以在不使用搜索功能的情况下共享 IR。 对于需要共享 IR 的数据工厂的托管标识，请在“分配权限”框中输入该托管标识，然后在数据工厂 UI 中选择“添加”。

  > [!NOTE]
  > 此功能只能在数据工厂 V2 中使用。


### <a name="next-steps"></a>后续步骤

- 查看 [Azure 数据工厂中的集成运行时概念](./concepts-integration-runtime.md)。

- 了解如何[在 Azure 门户中创建自承载集成运行时](./create-self-hosted-integration-runtime.md)。
