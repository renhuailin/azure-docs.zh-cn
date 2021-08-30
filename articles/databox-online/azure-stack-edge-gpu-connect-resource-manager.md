---
title: 连接到 Azure Stack Edge GPU 设备上的 Azure 资源管理器
description: 介绍如何使用 Azure PowerShell 连接到 Azure Stack Edge Pro GPU 上运行的 Azure 资源管理器。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/09/2021
ms.author: alkohli
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5ac064d53f4d85d8f517ef7bba67e88abe625f16
ms.sourcegitcommit: 555ea0d06da38dea1de6ecbe0ed746cddd4566f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2021
ms.locfileid: "113515663"
---
# <a name="connect-to-azure-resource-manager-on-your-azure-stack-edge-device"></a>连接到 Azure Stack Edge 设备上的 Azure 资源管理器

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure 资源管理器提供一个管理层，用于在 Azure 订阅中创建、更新和删除资源。 Azure Stack Edge 设备支持用相同的 Azure 资源管理器 API 在本地订阅中创建、更新和删除虚拟机 (VM)。 通过此支持，你能够以与云一致的方式管理设备。 

本文介绍如何使用 Azure PowerShell 通过 Azure 资源管理器连接到 Azure Stack Edge 设备上的本地 API。


## <a name="endpoints-on-azure-stack-edge-device"></a>Azure Stack Edge 设备上的终结点

下表总结了设备上公开的各种终结点、支持的协议以及用于访问这些终结点的端口。 在本文中，你会发现对这些终结点的引用。

| # | 端点 | 支持的协议 | 使用的端口 | 用于 |
| --- | --- | --- | --- | --- |
| 1. | Azure 资源管理器 | https | 443 | 连接到 Azure 资源管理器以实现自动化 |
| 2. | 安全令牌服务 | https | 443 | 通过访问和刷新令牌进行身份验证 |
| 3. | Blob* | https | 443 | 通过 REST 连接到 Blob 存储 |

\* 无需连接到 Azure 资源管理器即可连接到 Blob 存储终结点。
 
## <a name="connecting-to-azure-resource-manager-workflow"></a>连接到 Azure 资源管理器工作流

在使用 Azure 资源管理器连接到设备的本地 API 的过程中，需要执行以下步骤：

| 步骤编号 | 你将在此位置执行步骤... | .. 。 |
| --- | --- | --- |
| 1. | [配置 Azure Stack Edge 设备](#step-1-configure-azure-stack-edge-device) | 本地 Web UI |
| 2. | [创建和安装证书](#step-2-create-and-install-certificates) | Windows 客户端/本地 Web UI |
| 3. | [查看和配置先决条件](#step-3-install-powershell-on-the-client) | Windows 客户端 |
| 4. | [在客户端上设置 Azure PowerShell](#step-4-set-up-azure-powershell-on-the-client) | Windows 客户端 |
| 5. | [修改主机文件以进行终结点名称解析](#step-5-modify-host-file-for-endpoint-name-resolution) | Windows 客户端或 DNS 服务器 |
| 6. | [检查是否解析了终结点名称](#step-6-verify-endpoint-name-resolution-on-the-client) | Windows 客户端 |
| 7. | [使用 Azure PowerShell cmdlet 验证与 Azure 资源管理器的连接](#step-7-set-azure-resource-manager-environment) | Windows 客户端 |

以下部分详细介绍了连接到 Azure 资源管理器涉及的上述各个步骤。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保用于通过 Azure 资源管理器连接到设备的客户端使用的是 TLS 1.2。 有关详细信息，请转到[在访问 Azure Stack Edge 设备的 Windows 客户端上配置 TLS 1.2](azure-stack-edge-gpu-configure-tls-settings.md)。

## <a name="step-1-configure-azure-stack-edge-device"></a>步骤 1：配置 Azure Stack Edge 设备 

在 Azure Stack Edge 设备的本地 Web UI 中执行以下步骤。

1. 完成 Azure Stack Edge 设备的网络设置。 

    ![本地 Web UI“网络设置”页](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)


    记下设备 IP 地址。 稍后将会使用此 IP。

2. 在“设备”页中，配置设备名称和 DNS 域。 记下设备名称和 DNS 域，因为稍后将会使用它们。

    ![本地 Web UI“设备”页](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

    > [!IMPORTANT]
    > 设备名称和 DNS 域将用于构成公开的终结点。
    > 在本地 Web UI 的“设备”页中使用 Azure 资源管理器和 Blob 终结点。


## <a name="step-2-create-and-install-certificates"></a>步骤 2：创建和安装证书

证书可确保通信受信任。 在 Azure Stack Edge 设备上，会自动生成自签名的设备、Blob 和 Azure 资源管理器证书。 此外，也可以选择自带签名的 blob 和 Azure 资源管理器证书。

如果自带签名证书，则还需要证书的相应签名链。 对于签名链、Azure 资源管理器和设备上的 blob 证书，你将需要在客户端计算机上使用相应的证书，以便对设备进行身份验证和与之通信。

若要连接到 Azure 资源管理器，需要创建或获取签名链和终结点证书，将这些证书导入 Windows 客户端，最后在设备上上传这些证书。

### <a name="create-certificates"></a>创建证书

对于仅测试和开发用途，可以使用 Windows PowerShell 在本地系统上创建证书。 为客户端创建证书时，请遵循以下准则：

1. 首先，需要为签名链创建根证书。 有关详细信息，请参阅[创建签名链证书](azure-stack-edge-gpu-create-certificates-powershell.md#create-signing-chain-certificate)的步骤。

2. 接下来，可为 Azure 资源管理器和 Blob（可选）创建终结点证书。 可以从本地 Web UI 中的“设备”页获取这些终结点。 请参阅[创建终结点证书](azure-stack-edge-gpu-create-certificates-powershell.md#create-signed-endpoint-certificates)的步骤。

3. 对于所有这些证书，请确保使用者名称和使用者可选名称符合以下准则：

    |类型 |使用者名称 (SN)  |使用者可选名称 (SAN)  |使用者名称示例 |
    |---------|---------|---------|---------|
    |Azure 资源管理器|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob 存储*|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |两个终结点的多个 SAN 单一证书|`<Device name>.<dnsdomain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |

\* 无需 Blob 存储即可连接到 Azure 资源管理器。 如果在设备上创建本地存储帐户，该证书将在此处列出。

有关证书的详细信息，请参阅如何[在设备中上传证书，以及如何在访问设备的客户端中导入证书](azure-stack-edge-gpu-manage-certificates.md)。

### <a name="upload-certificates-on-the-device"></a>在设备上上传证书

在上一步骤中创建的证书将存储在客户端的“个人”存储中。 这些证书需要在你的客户端上导出为合适的格式文件，然后才能上传到设备。

1. 根证书必须导出为 DER 格式的文件，文件扩展名为 .cer。 有关详细步骤，请参阅[将证书导出为 .cer 格式的文件](azure-stack-edge-gpu-prepare-certificates-device-upload.md#export-certificates-as-der-format)。

2. 终结点证书必须导出为包含私钥的 .pfx 文件。 有关详细步骤，请参阅[将证书导出为包含私钥的 .pfx 文件](azure-stack-edge-gpu-prepare-certificates-device-upload.md#export-certificates-as-pfx-format-with-private-key)。

3. 然后，在本地 Web UI 中使用“证书”页上的“+ 添加证书”选项，在设备上上传根证书和终结点证书 。 若要上传证书，请按照[上传证书](azure-stack-edge-gpu-manage-certificates.md#upload-certificates)中的步骤操作。


### <a name="import-certificates-on-the-client-running-azure-powershell"></a>在运行 Azure PowerShell 的客户端上导入证书

将在其中调用 Azure 资源管理器 API 的 Windows 客户端需要与设备建立信任。 为此，必须在 Windows 客户端上将在上一步骤中创建的证书导入到相应的证书存储中。

1. 现在，应该将导出的扩展名为 .cer 的 DER 格式根证书导入到客户端系统上的“受信任的根证书颁发机构”中。 有关详细步骤，请参阅[将证书导入到“受信任的根证书颁发机构”存储](azure-stack-edge-gpu-manage-certificates.md#import-certificates-as-der-format)。

2. 导出为 .pfx 的终结点证书必须导出为 .cer 。 然后，将该 .cer 导入到系统上的“个人”证书存储中。 有关详细步骤，请参阅[证书导入到个人存储](azure-stack-edge-gpu-manage-certificates.md#import-certificates-as-der-format)。

## <a name="step-3-install-powershell-on-the-client"></a>步骤 3：在客户端安装 PowerShell 

### <a name="az"></a>[Az](#tab/Az)

Windows 客户端必须满足以下先决条件：


1. 运行 PowerShell 版本 5.0。 必须使用 PowerShell 版本 5.0。 若要查看系统上的 PowerShell 的版本，请运行以下 cmdlet：

    ```powershell
    $PSVersionTable.PSVersion
    ```

    比较主版本，并确保其为 5.0 或更高版本。

    如果版本已过时，请参阅[升级现有的 Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true#upgrading-existing-windows-powershell)。

    如果没有 PowerShell 5.0，请按照[安装 Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true) 中所述进行操作。

    输出示例如下所示。

    ```output
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved. 
    Try the new cross-platform PowerShell https://aka.ms/pscore6
    PS C:\windows\system32> $PSVersionTable.PSVersion
    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      19041  906
    ```
    
1. 可以访问 PowerShell 库。

    以管理员身份运行 PowerShell。 验证 PowerShellGet 版本是否低于 2.2.3。 此外，验证 `PSGallery` 是否已注册为存储库。

    ```powershell
    Install-Module PowerShellGet –MinimumVersion 2.2.3
    Import-Module -Name PackageManagement -ErrorAction Stop
    Get-PSRepository -Name "PSGallery"
    ```
    
    输出示例如下所示。
    
    ```output
    PS C:\windows\system32> Install-Module PowerShellGet –MinimumVersion 2.2.3
    PS C:\windows\system32> Import-Module -Name PackageManagement -ErrorAction Stop
    PS C:\windows\system32> Get-PSRepository -Name "PSGallery"
    Name                      InstallationPolicy   SourceLocation
    ----                      ------------------   --------------
    PSGallery                 Trusted              https://www.powershellgallery.com/api/v2
    ```    

### <a name="azurerm"></a>[AzureRM](#tab/AzureRM)

Windows 客户端必须满足以下先决条件：


1. 运行 PowerShell 版本 5.0。 必须使用 PowerShell 版本 5.0。 不支持 PowerShell Core。 若要查看系统上的 PowerShell 的版本，请运行以下 cmdlet：

    ```powershell
    $PSVersionTable.PSVersion
    ```

    比较主版本，并确保其为 5.0 或更高版本。

    如果版本已过时，请参阅[升级现有的 Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true#upgrading-existing-windows-powershell)。

    如果没有 PowerShell 5.0，请按照[安装 Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true) 中所述进行操作。

    输出示例如下所示。

    ```output
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved. 
    Try the new cross-platform PowerShell https://aka.ms/pscore6
    PS C:\windows\system32> $PSVersionTable.PSVersion
    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      18362  145
    ```
    
2. 可以访问 PowerShell 库。

    以管理员身份运行 PowerShell。 验证 `PSGallery` 是否已注册为存储库。

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop
    Get-PSRepository -Name "PSGallery"
    ```
    
    输出示例如下所示。
    
    ```output
    PS C:\windows\system32> Import-Module -Name PowerShellGet -ErrorAction Stop
    PS C:\windows\system32> Import-Module -Name PackageManagement -ErrorAction Stop
    PS C:\windows\system32> Get-PSRepository -Name "PSGallery"
    Name                      InstallationPolicy   SourceLocation
    ----                      ------------------   --------------
    PSGallery                 Trusted              https://www.powershellgallery.com/api/v2
    ```
---    
如果存储库不受信任，或需要了解详细信息，请参阅[验证 PowerShell 库的可访问性](/azure-stack/operator/azure-stack-powershell-install?view=azs-1908&preserve-view=true&preserve-view=true#2-validate-the-powershell-gallery-accessibility)。

## <a name="step-4-set-up-azure-powershell-on-the-client"></a>步骤 4：在客户端上设置 Azure PowerShell 

### <a name="az"></a>[Az](#tab/Az)

你将在客户端上安装 Azure PowerShell 模块（将与设备一起使用）。

1. 以管理员身份运行 PowerShell。 需访问 PowerShell 库。 


1. 首先验证客户端上是否有现有的 `AzureRM` 和 `Az` 模块版本。 要进行检查，请运行以下命令：

    ```powershell
    # Check existing versions of AzureRM modules
    Get-InstalledModule -Name AzureRM -AllVersions

    # Check existing versions of Az modules
    Get-InstalledModule -Name Az -AllVersions
    ```

    如果有现有版本，请使用 `Uninstall-Module` cmdlet 将其卸载。 有关详细信息，请参阅 
    - [卸载 AzureRM 模块](/powershell/azure/uninstall-az-ps?view=azps-6.0.0&preserve-view=true#uninstall-the-az-module)。
    - [卸载 Az 模块](/powershell/azure/uninstall-az-ps?view=azps-6.0.0&preserve-view=true#uninstall-the-azurerm-module)。

1. 若要从 PowerShell 库安装所需的 Azure PowerShell 模块，请运行以下命令：

    - 如果客户端使用 PowerShell Core 7.0 和更高版本：

        ```powershell
        # Install the Az.BootStrapper module. Select Yes when prompted to install NuGet.
        
        Install-Module -Name Az.BootStrapper
        
        # Install and import the API Version Profile into the current PowerShell session.
        
        Use-AzProfile -Profile 2020-09-01-hybrid -Force
        
        # Confirm the installation of PowerShell
        Get-Module -Name "Az*" -ListAvailable
        ```
    
    - 如果客户端使用 PowerShell 5.1 和更高版本：
        
        ```powershell
        #Install the Az module version 1.10.0
        
        Install-Module –Name Az –RequiredVersion 1.10.0    
        ```

3.  确保在安装结束时运行 Az 模块版本 1.10.0。 
   

    如果使用了 PowerShell Core 7.0 和更高版本，则以下示例输出将指示已成功安装 Az 版本 1.10.0 模块。
    
    ```output
   
    PS C:\windows\system32> Install-Module -Name Az.BootStrapper
    PS C:\windows\system32> Use-AzProfile -Profile 2020-09-01-hybrid -Force
    Loading Profile 2020-09-01-hybrid
    PS C:\windows\system32> Get-Module -Name "Az*" -ListAvailable
    ```

    如果使用了 PowerShell 5.1 和更高版本，则以下示例输出将指示已成功安装 Az 版本 1.10.0 模块。
     
    ```powershell
    PS C:\WINDOWS\system32> Get-InstalledModule -Name Az -AllVersions
    Version              Name                                Repository           Description
    -------              ----                                ----------           ------
    1.10.0               Az                                  PSGallery            Mic...  
    
    PS C:\WINDOWS\system32>
    ```

### <a name="azurerm"></a>[AzureRM](#tab/AzureRM)

你将在客户端上安装 Azure PowerShell 模块（将与设备一起使用）。

1. 以管理员身份运行 PowerShell。 需访问 PowerShell 库。 


2. 若要从 PowerShell 库安装所需的 Azure PowerShell 模块，请运行以下命令：

    ```powershell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet.
    
    Install-Module -Name AzureRM.BootStrapper
    
    # Install and import the API Version Profile into the current PowerShell session.
    
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    
    # Confirm the installation of PowerShell
    Get-Module -Name "Azure*" -ListAvailable
    ```
    
    确保在安装结束时运行了版本 2.5.0 的 Azure-RM 模块。 
    如果现有 Azure-RM 模块的版本与所需版本不匹配，请使用以下命令进行卸载：

    `Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose`

    现在需要重新安装所需的版本。
   

    下面显示的示例输出指示已成功安装 AzureRM 版本 2.5.0 模块。
    
    ```powershell
    PS C:\windows\system32> Install-Module -Name AzureRM.BootStrapper
    PS C:\windows\system32> Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    Loading Profile 2019-03-01-hybrid
    PS C:\windows\system32> Get-Module -Name "Azure*" -ListAvailable
     
        Directory: C:\Program Files\WindowsPowerShell\Modules
     
    ModuleType Version    Name                          ExportedCommands
    ---------- -------    ----                          ----------------
    Script     4.5.0      Azure.Storage                       {Get-AzureStorageTable, New-AzureStorageTableSASToken, New...
    Script     2.5.0      AzureRM
    Script     0.5.0      AzureRM.BootStrapper                {Update-AzureRmProfile, Uninstall-AzureRmProfile, Install-...
    Script     4.6.1      AzureRM.Compute                     {Remove-AzureRmAvailabilitySet, Get-AzureRmAvailabilitySet...
    Script     3.5.1      AzureRM.Dns                         {Get-AzureRmDnsRecordSet, New-AzureRmDnsRecordConfig, Remo...
    Script     5.1.5      AzureRM.Insights                    {Get-AzureRmMetricDefinition, Get-AzureRmMetric, Remove-Az...
    Script     4.2.0      AzureRM.KeyVault                    {Add-AzureKeyVaultCertificate, Set-AzureKeyVaultCertificat...
    Script     5.0.1      AzureRM.Network                     {Add-AzureRmApplicationGatewayAuthenticationCertificate, G...
    Script     5.8.3      AzureRM.profile                     {Disable-AzureRmDataCollection, Disable-AzureRmContextAuto...
    Script     6.4.3      AzureRM.Resources                   {Get-AzureRmProviderOperation, Remove-AzureRmRoleAssignmen...
    Script     5.0.4      AzureRM.Storage                     {Get-AzureRmStorageAccount, Get-AzureRmStorageAccountKey, ...
    Script     4.0.2      AzureRM.Tags                        {Remove-AzureRmTag, Get-AzureRmTag, New-AzureRmTag}
    Script     4.0.3      AzureRM.UsageAggregates             Get-UsageAggregates
    Script     5.0.1      AzureRM.Websites                    {Get-AzureRmAppServicePlan, Set-AzureRmAppServicePlan, New...
    
     
        Directory: C:\Program Files (x86)\Microsoft Azure Information Protection\Powershell
     
    ModuleType Version    Name                            ExportedCommands
    ---------- -------    ----                           ----------------
    Binary     1.48.204.0 AzureInformationProtection          {Clear-RMSAuthentication, Get-RMSFileStatus, Get-RMSServer...
    ```
---   

## <a name="step-5-modify-host-file-for-endpoint-name-resolution"></a>步骤 5：修改主机文件以进行终结点名称解析 

现在将设备 IP 地址添加到：

- 客户端上的主机文件，或
- DNS 服务器配置

> [!IMPORTANT]
> 建议修改终结点名称解析的 DNS 服务器配置。

请在用于连接到设备的 Windows 客户端上执行以下步骤：

1. 以管理员身份启动记事本，然后打开位于 C:\Windows\System32\Drivers\etc 的 hosts 文件 。

    ![Windows 资源管理器 hosts 文件](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file.png)

2. 将以下条目添加到 hosts 文件，替换为设备的适当值： 

    ```
    <Device IP> login.<appliance name>.<DNS domain>
    <Device IP> management.<appliance name>.<DNS domain>
    <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
    ```

    > [!IMPORTANT]
    > hosts 文件中的条目应与提供用于在后面的步骤中连接到 Azure 资源管理器的条目完全匹配。 确保此处的 DNS 域条目均采用小写形式。 若要获取 `<appliance name>` 和 `<DNS domain>` 的值，请在设备的本地 UI 中转到“设备”页。

    在前面的步骤中，你已从本地 Web UI 保存了设备 IP。

    `login.<appliance name>.<DNS domain>` 条目是安全令牌服务 (STS) 的终结点。 STS 负责创建、验证、续订和取消安全令牌。 安全令牌服务用于创建访问令牌和刷新令牌，这些令牌用于在设备和客户端之间进行连续通信。

    连接到 Azure 资源管理器时，Blob 存储的终结点是可选的。 通过存储帐户将数据传输到 Azure 时需要此终结点。

3. 请使用以下图像作为参考。 保存 **hosts** 文件。

    ![记事本中的 hosts 文件](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file-notepad.png)

## <a name="step-6-verify-endpoint-name-resolution-on-the-client"></a>步骤 6：验证客户端上的终结点名称解析

检查在用于连接到设备的客户端上是否解析了终结点名称。

1. 可以使用 `ping.exe` 命令行实用工具来检查是否解析了终结点名称。 指定 IP 地址后，`ping` 命令将返回所跟踪的计算机的 TCP/IP 主机名。

    如下例所示，将 `-a` 开关添加到命令行。 如果主机名是可返回的，则还会在回复中返回此可能有价值的信息。

    ![命令提示符中的 ping](media/azure-stack-edge-gpu-connect-resource-manager/ping-command-prompt.png)



## <a name="step-7-set-azure-resource-manager-environment"></a>步骤 7：设置 Azure 资源管理器环境

### <a name="az"></a>[Az](#tab/Az)

设置 Azure 资源管理器环境，并验证设备通过 Azure 资源管理器与客户端进行的通信是否正常工作。 请执行以下步骤进行此验证：


1. 使用 `Add-AzEnvironment` cmdlet 进一步确保通过 Azure 资源管理器进行的通信正常工作，并且 API 调用通过 Azure 资源管理器专用端口 443。

    `Add-AzEnvironment` cmdlet 会添加终结点和元数据，使 Azure 资源管理器 cmdlet 能够与新的 Azure 资源管理器实例连接。 


    > [!IMPORTANT]
    > 在下面的 cmdlet 中提供的 Azure 资源管理器终结点 URL 是区分大小写的。 请确保终结点 URL 全部采用小写形式，并与 hosts 文件中提供的 URL 匹配。 如果大小写不匹配，则会出现错误。

    ```powershell
    Add-AzEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>/"
    ```

    下面显示了示例输出：
    
    ```output
    PS C:\WINDOWS\system32> Add-AzEnvironment -Name AzASE -ARMEndpoint "https://management.myasegpu.wdshcsso.com/"
    
    Name  Resource Manager Url                      ActiveDirectory Authority
    ----  --------------------                      -------------------------
    AzASE https://management.myasegpu.wdshcsso.com/ https://login.myasegpu.wdshcsso.c...
    ```

2. 将环境设置为 Azure Stack Edge，将用于 Azure 资源管理器调用的端口设置为 443。 可以通过两种方式定义环境：

    - 设置环境。 键入以下命令：

        ```powershell
        Set-AzEnvironment -Name <Environment Name>
        ```
        
        下面是示例输出。

        ```output
        PS C:\WINDOWS\system32> Set-AzEnvironment -Name AzASE

        Name  Resource Manager Url                      ActiveDirectory Authority
        ----  --------------------                      -------------------------
        AzASE https://management.myasegpu.wdshcsso.com/ https://login.myasegpu.wdshcsso.c...     
        ```
        有关详细信息，请转到 [Set-AzEnvironment](/powershell/module/azurerm.profile/set-azurermenvironment?view=azurermps-6.13.0&preserve-view=true)。    

    - 针对执行的每个 cmdlet 定义内联环境。 这可确保通过正确的环境进行所有 API 调用。 默认情况下，调用将通过 Azure 公有云，但你希望这些调用通过为 Azure Stack Edge 设备设置的环境。

    - 有关详细信息，请参阅如何[切换 Az 环境](#switch-environments)。

2. 调用本地设备 API 来对与 Azure 资源管理器的连接进行身份验证。 

    1. 这些凭据适用于本地计算机帐户，并且仅用于 API 访问。

    2. 可以通过 `login-AzAccount` 或 `Connect-AzAccount` 命令进行连接。 

        1. 若要登录，请键入以下命令。 
        
            ```powershell
            $pass = ConvertTo-SecureString "<Your password>" -AsPlainText -Force;
            $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
            Connect-AzAccount -EnvironmentName AzASE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred
            ```

            使用租户 ID c0257de7-538f-415c-993a-1b87a031879d，因为在此实例中，该 ID 已经过硬编码。
            使用以下用户名和密码。

            - 用户名 - EdgeArmUser

            - 密码 - [设置 Azure 资源管理器的密码](azure-stack-edge-gpu-set-azure-resource-manager-password.md)，然后使用此密码登录。 
       


            下面是 `Connect-AzAccount` 的示例输出：

            ```output
            PS C:\windows\system32> $pass = ConvertTo-SecureString "<Your password>" -AsPlainText -Force;
            PS C:\windows\system32> $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
            PS C:\windows\system32> Connect-AzAccount -EnvironmentName AzASE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred
            
            Account       SubscriptionName   TenantId            Environment
            -------       ----------------   --------            -----------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzASE
            
            PS C:\windows\system32>
            ```                   
        
            另一种登录方式是使用 `login-AzAccount` cmdlet。 
            
            `login-AzAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d` 

            下面是示例输出。 
         
            ```output
            PS C:\WINDOWS\system32> login-AzAccount -EnvironmentName AzASE -TenantId c0257de7-538f-415c-993a-1b87a031879d
            
            Account               SubscriptionName              TenantId
            -------               ----------------              --------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a...            
            
            PS C:\WINDOWS\system32>
            ```
3. 若要验证是否能够正常连接到设备，请使用 `Get-AzResource` 命令。 此命令应返回设备本地存在的所有资源。

    下面是示例输出。

    ```output
    PS C:\WINDOWS\system32> Get-AzResource
    
    Name              : aseimagestorageaccount
    ResourceGroupName : ase-image-resourcegroup
    ResourceType      : Microsoft.Storage/storageaccounts
    Location          : dbelocal
    ResourceId        : /subscriptions/.../resourceGroups/ase-image-resourcegroup/providers/Microsoft.Storage/storageac
                        counts/aseimagestorageaccount
    Tags              :
    
    Name              : myaselinuxvmimage1
    ResourceGroupName : ASERG
    ResourceType      : Microsoft.Compute/images
    Location          : dbelocal
    ResourceId        : /subscriptions/.../resourceGroups/ASERG/providers/Microsoft.Compute/images/myaselinuxvmimage1
    Tags              :
    
    Name              : ASEVNET
    ResourceGroupName : ASERG
    ResourceType      : Microsoft.Network/virtualNetworks
    Location          : dbelocal
    ResourceId        : /subscriptions/.../resourceGroups/ASERG/providers/Microsoft.Network/virtualNetworks/ASEVNET
    Tags              :
    
    PS C:\WINDOWS\system32>  
    ```

   

### <a name="azurerm"></a>[AzureRM](#tab/AzureRM)

设置 Azure 资源管理器环境，并验证设备通过 Azure 资源管理器与客户端进行的通信是否正常工作。 请执行以下步骤进行此验证：


1. 使用 `Add-AzureRmEnvironment` cmdlet 进一步确保通过 Azure 资源管理器进行的通信正常工作，并且 API 调用通过 Azure 资源管理器专用端口 443。

    `Add-AzureRmEnvironment` cmdlet 会添加终结点和元数据，使 Azure 资源管理器 cmdlet 能够与新的 Azure 资源管理器实例连接。 


    > [!IMPORTANT]
    > 在下面的 cmdlet 中提供的 Azure 资源管理器终结点 URL 是区分大小写的。 请确保终结点 URL 全部采用小写形式，并与 hosts 文件中提供的 URL 匹配。 如果大小写不匹配，则会出现错误。

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>/"
    ```

    下面显示了示例输出：
    
    ```output
    PS C:\windows\system32> Add-AzureRmEnvironment -Name AzDBE -ARMEndpoint https://management.dbe-n6hugc2ra.microsoftdatabox.com/
    
    Name  Resource Manager Url                    ActiveDirectory Authority
    ----  --------------------                   -------------------------
    AzDBE https://management.dbe-n6hugc2ra.microsoftdatabox.com https://login.dbe-n6hugc2ra.microsoftdatabox.com/adfs/
    ```

2. 将环境设置为 Azure Stack Edge，将用于 Azure 资源管理器调用的端口设置为 443。 可以通过两种方式定义环境：

    - 设置环境。 键入以下命令：

    ```powershell
    Set-AzureRMEnvironment -Name <Environment Name>
    ```
    
    有关详细信息，请转到 [Set-AzureRMEnvironment](/powershell/module/azurerm.profile/set-azurermenvironment?view=azurermps-6.13.0&preserve-view=true)。

    - 针对执行的每个 cmdlet 定义内联环境。 这可确保通过正确的环境进行所有 API 调用。 默认情况下，调用将通过 Azure 公有云，但你希望这些调用通过为 Azure Stack Edge 设备设置的环境。

    - 有关详细信息，请参阅[如何切换 AzureRM 环境](#switch-environments)。

2. 调用本地设备 API 来对与 Azure 资源管理器的连接进行身份验证。 

    1. 这些凭据适用于本地计算机帐户，并且仅用于 API 访问。

    2. 可以通过 `login-AzureRMAccount` 或 `Connect-AzureRMAccount` 命令进行连接。 

        1. 若要登录，请键入以下命令。 此实例中的租户 ID 采用硬编码 - c0257de7-538f-415c-993a-1b87a031879d。 使用以下用户名和密码。

            - 用户名 - EdgeArmUser

            - 密码 - [设置 Azure 资源管理器的密码](azure-stack-edge-gpu-set-azure-resource-manager-password.md)，然后使用此密码登录。 

            ```output
            PS C:\windows\system32> $pass = ConvertTo-SecureString "<Your password>" -AsPlainText -Force;
            PS C:\windows\system32> $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
            PS C:\windows\system32> Connect-AzureRmAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred
            
            Account       SubscriptionName   TenantId            Environment
            -------       ----------------   --------            -----------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            
            PS C:\windows\system32>
            ```
                   
        
            另一种登录方式是使用 `login-AzureRmAccount` cmdlet。 
            
            `login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d` 

            下面是该命令的示例输出。 
         
            ```output
            PS C:\Users\Administrator> login-AzureRMAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d
            
            Account         SubscriptionName  TenantId              Environment
            -------         ----------------  --------              -------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            PS C:\Users\Administrator>
            ```
---

如果遇到 Azure 资源管理器连接问题，请参阅[排查 Azure 资源管理器问题](azure-stack-edge-gpu-troubleshoot-azure-resource-manager.md)以获取指导。 

> [!IMPORTANT]
> 与 Azure 资源管理器的连接每 1.5 小时或每当 Azure Stack Edge 设备重启时过期一次。 如果发生这种情况，所执行的任何 cmdlet 都将返回错误消息，表示不再连接到 Azure。 需重新登录。

## <a name="switch-environments"></a>切换环境

你可能需要在两个环境之间切换。

### <a name="az"></a>[Az](#tab/Az)

运行 `Disconnect-AzAccount` 命令以切换到其他 `AzEnvironment`。 如果在不使用 `Disconnect-AzAccount` 的情况下使用 `Set-AzEnvironment` 和 `Login-AzAccount`，则实际上不会切换环境。  

下面的示例演示如何在 `AzASE1` 和 `AzASE2` 这两个环境之间进行切换。

首先，列出客户端上所有的现有环境。


```output
PS C:\WINDOWS\system32> Get-AzEnvironment
Name    Resource Manager Url     ActiveDirectory Authority
----    --------------------      -------------------------
AzureChinaCloud   https://management.chinacloudapi.cn/                 https://login.chinacloudapi.cn/
AzureCloud        https://management.azure.com/                        https://login.microsoftonline.com/
AzureGermanCloud  https://management.microsoftazure.de/                https://login.microsoftonline.de/
AzDBE1            https://management.HVTG1T2-Test.microsoftdatabox.com https://login.hvtg1t2-test.microsoftdatabox.com/adfs/
AzureUSGovernment https://management.usgovcloudapi.net/                https://login.microsoftonline.us/
AzDBE2            https://management.CVV4PX2-Test.microsoftdatabox.com https://login.cvv4px2-test.microsoftdatabox.com/adfs/
```

接下来，通过 Azure 资源管理器获取当前所连接到的环境。

```output
PS C:\WINDOWS\system32> Get-AzContext |fl *

Name               : Default Provider Subscription (...) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE2
Subscription       : ...
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```

现在，应与当前环境断开连接，然后再切换到其他环境。


```output
PS C:\WINDOWS\system32> Disconnect-AzAccount

Id                    : EdgeArmUser@localhost
Type                  : User
Tenants               : {c0257de7-538f-415c-993a-1b87a031879d}
AccessToken           :
Credential            :
TenantMap             : {}
CertificateThumbprint :
ExtendedProperties    : {[Subscriptions, ...], [Tenants, c0257de7-538f-415c-993a-1b87a031879d]}
```

登录到其他环境。 下面显示了示例输出。

```output
PS C:\WINDOWS\system32> Login-AzAccount -Environment "AzDBE1" -TenantId $ArmTenantId

Account     SubscriptionName   TenantId        Environment
-------     ----------------   --------        -----------
EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE1
```

运行此 cmdlet，以确认所连接到的环境。

```output
PS C:\WINDOWS\system32> Get-AzContext |fl *

Name               : Default Provider Subscription (...) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE1
Subscription       : ...
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```
现已切换到预期的环境。

### <a name="azurerm"></a>[AzureRM](#tab/AzureRM)

运行 `Disconnect-AzureRmAccount` 命令以切换到其他 `AzureRmEnvironment`。 如果在不使用 `Disconnect-AzureRmAccount` 的情况下使用 `Set-AzureRmEnvironment` 和 `Login-AzureRmAccount`，则实际上不会切换环境。  

下面的示例演示如何在 `AzDBE1` 和 `AzDBE2` 这两个环境之间进行切换。

首先，列出客户端上所有的现有环境。


```output
PS C:\WINDOWS\system32> Get-AzureRmEnvironment
Name    Resource Manager Url     ActiveDirectory Authority
----    --------------------      -------------------------
AzureChinaCloud   https://management.chinacloudapi.cn/                 https://login.chinacloudapi.cn/
AzureCloud        https://management.azure.com/                        https://login.microsoftonline.com/
AzureGermanCloud  https://management.microsoftazure.de/                https://login.microsoftonline.de/
AzDBE1            https://management.HVTG1T2-Test.microsoftdatabox.com https://login.hvtg1t2-test.microsoftdatabox.com/adfs/
AzureUSGovernment https://management.usgovcloudapi.net/                https://login.microsoftonline.us/
AzDBE2            https://management.CVV4PX2-Test.microsoftdatabox.com https://login.cvv4px2-test.microsoftdatabox.com/adfs/
```

接下来，通过 Azure 资源管理器获取当前所连接到的环境。

```output
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE2
Subscription       : ...
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```

现在，应与当前环境断开连接，然后再切换到其他环境。


```output
PS C:\WINDOWS\system32> Disconnect-AzureRmAccount

Id                    : EdgeArmUser@localhost
Type                  : User
Tenants               : {c0257de7-538f-415c-993a-1b87a031879d}
AccessToken           :
Credential            :
TenantMap             : {}
CertificateThumbprint :
ExtendedProperties    : {[Subscriptions, ...], [Tenants, c0257de7-538f-415c-993a-1b87a031879d]}
```

登录到其他环境。 下面显示了示例输出。

```output
PS C:\WINDOWS\system32> Login-AzureRmAccount -Environment "AzDBE1" -TenantId $ArmTenantId

Account     SubscriptionName   TenantId        Environment
-------     ----------------   --------        -----------
EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE1
```

运行此 cmdlet，以确认所连接到的环境。

```output
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (...) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE1
Subscription       : ...
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```
---

现已切换到预期的环境。

## <a name="next-steps"></a>后续步骤

- [排查 Azure 资源管理器问题](azure-stack-edge-gpu-troubleshoot-azure-resource-manager.md)。
- [在 Azure Stack Edge Pro 设备上部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)。
