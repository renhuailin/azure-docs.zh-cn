---
title: 使用 Azure PowerShell 配置文件上传 | Microsoft Docs
description: 如何使用 Azure PowerShell cmdlet 配置 IoT 中心，以便从连接的设备上传文件。 包括有关配置目标 Azure 存储帐户的信息。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/20/2021
ms.author: robinsh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: dd108bfd61a84e4c25b1ab59e9f24e23048ada2d
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015621"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>使用 PowerShell 配置 IoT 中心文件上传

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

本文介绍如何使用 PowerShell 在 IoT 中心配置文件上传。 

若要使用 [IoT 中心的文件上传功能](iot-hub-devguide-file-upload.md)，必须先将 Azure 存储帐户和 Blob 容器与 IoT 中心关联。 IoT 中心会自动生成对此 Blob 容器具有写入权限的 SAS URI，以供设备上传文件时使用。 除了存储帐户和 Blob 容器外，还可以设置 SAS URI 的生存时间，以及配置可由 IoT 中心传送给后端服务的可选文件上传通知的设置。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件

* 有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

* Azure IoT 中心。 如果没有 IoT 中心，可以使用 [New-AzIoTHub cmdlet](/powershell/module/az.iothub/new-aziothub) 创建一个，或者使用门户[创建一个 IoT 中心](iot-hub-create-through-portal.md)。

* 一个 Azure 存储帐户。 如果没有 Azure 存储帐户，可以使用 [Azure 存储 PowerShell cmdlet](/powershell/module/az.storage/) 创建一个，或使用门户[创建存储帐户](../storage/common/storage-account-create.md)

* 在 [Azure Cloud Shell](../cloud-shell/quickstart-powershell.md) 中使用 PowerShell 环境。

   [![在新窗口中启动 Cloud Shell](./media/iot-hub-configure-file-upload-powershell/hdi-launch-cloud-shell.png)](https://shell.azure.com)

* 如果你愿意，可以在本地[安装](/powershell/scripting/install/installing-powershell) PowerShell。

  * [安装 Azure Az PowerShell 模块](/powershell/azure/install-az-ps)。 （此模块默认安装在 Azure Cloud Shell PowerShell 环境中。） 
  * 使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 命令登录到 PowerShell。  若要完成身份验证过程，请遵循终端中显示的步骤。  有关其他登录选项，请参阅[使用 Azure PowerShell 登录](/powershell/azure/authenticate-azureps)。


## <a name="sign-in-and-set-your-azure-account"></a>登录并设置 Azure 帐户

登录到 Azure 帐户，并选择订阅。 如果你使用的是 Azure Cloud Shell，则应该已登录；但是，如果你有多个订阅，则仍可能需要选择 Azure 订阅。

1. 在 PowerShell 提示符下，运行 **Connect-AzAccount**：

    ```powershell
    Connect-AzAccount
    ```

2. 如果有多个 Azure 订阅，则访问 Azure 即有权访问与凭据关联的所有 Azure 订阅。 使用 [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) 命令列出可供使用的 Azure 订阅：

    ```powershell
    Get-AzSubscription
    ```

    使用以下命令，选择想要用于运行命令以管理 IoT 中心的订阅。 可使用上一命令输出中的订阅名称或 ID：

    ```powershell
    Select-AzSubscription `
        -Name "{your subscription name}"
    ```

    > [!NOTE]
    > Select-AzSubscription 命令是 [Select-AzContext](/powershell/module/az.accounts/select-azcontext) 的别名，让你可以使用 Get-AzSubscription 命令返回的订阅名称 (Name) 或订阅 ID (Id)，而不必使用 Select-AzContext 命令所需的较复杂的上下文名称。

## <a name="retrieve-your-storage-account-details"></a>检索存储帐户详细信息

以下步骤假设已使用 **Resource Manager** 部署模型而不 **经典** 部署模型创建了存储帐户。

若要从设备配置文件上传，需要 Azure 存储帐户的连接字符串。 存储帐户必须与 IoT 中心位于同一订阅中。 还需要存储帐户中 Blob 容器的名称。 使用 [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) 命令检索存储帐户密钥：

```powershell
Get-AzStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

记下 **key1** 存储帐户密钥值。 在后续步骤中需要用到它。

可将现有的 Blob 容器用于文件上传，或新建一个容器：

* 若要列出存储帐户中的现有 Blob 容器，请使用 [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) 和 [Get-AzStorageContainer](/powershell/module/az.storage/get-azstoragecontainer) 命令：

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzStorageContainer -Context $ctx
    ```

* 若要在存储帐户中创建 Blob 容器，请使用 [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) 和 [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer) 命令：

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>配置 IoT 中心

现在可以使用存储帐户详细信息配置 IoT 中心以[将文件上传到 IoT 中心](iot-hub-devguide-file-upload.md)。

配置需要以下值：

* **存储容器**：当前 Azure 订阅中要与 IoT 中心关联的 Azure 存储帐户中的 Blob 容器。 检索在上一部分中必要的存储帐户信息。 IoT 中心会自动生成对此 Blob 容器具有写入权限的 SAS URI，以供设备上传文件时使用。

* 接收已上传文件的通知：启用或禁用文件上传通知。 

* **SAS TTL**：此设置是 IoT 中心返回给设备的 SAS URI 生存时间。 默认设置为一小时。

* 文件通知设置默认 TTL：文件上传通知到期前的生存时间。 默认设置为一天。

* **文件通知最大传送数**：IoT 中心将尝试传送文件上传通知的次数。 默认设置为 10。

使用 [Set-AzIotHub](/powershell/module/az.iothub/set-aziothub) 命令在 IoT 中心配置文件上传设置：

```powershell
Set-AzIotHub `
    -ResourceGroupName "{your iot hub resource group}" `
    -Name "{your iot hub name}" `
    -FileUploadNotificationTtl "01:00:00" `
    -FileUploadSasUriTtl "01:00:00" `
    -EnableFileUploadNotifications $true `
    -FileUploadStorageConnectionString "DefaultEndpointsProtocol=https;AccountName={your storage account name};AccountKey={your storage account key};EndpointSuffix=core.windows.net" `
    -FileUploadContainerName "{your blob container name}" `
    -FileUploadNotificationMaxDeliveryCount 10
```

> [!NOTE]
> 默认情况下，IoT 中心使用连接字符串中的帐户密钥通过 Azure 存储进行身份验证。 也可以使用系统分配的或用户分配的托管标识进行身份验证。 托管标识在 Azure AD 中以安全的方式为 Azure 服务提供自动托管标识。 若要了解详细信息，请参阅 [IoT 中心对托管标识的支持](./iot-hub-managed-identity.md)。 Set-AzIotHub 命令中当前没有用于设置身份验证类型的参数。 你可以改用 [Azure 门户](./iot-hub-configure-file-upload.md)或 [Azure CLI](./iot-hub-configure-file-upload-cli.md)。 

## <a name="next-steps"></a>后续步骤

* [从设备上传文件概述](iot-hub-devguide-file-upload.md)
* [IoT 中心对托管标识的支持](./iot-hub-managed-identity.md)
* [文件上传操作指南](./iot-hub-csharp-csharp-file-upload.md)