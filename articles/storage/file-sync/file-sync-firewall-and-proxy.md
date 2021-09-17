---
title: Azure 文件同步本地防火墙和代理设置 | Microsoft Docs
description: 了解 Azure 文件同步本地代理和防火墙设置。 查看端口、网络以及到 Azure 的特殊连接的配置详细信息。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6af10befe614ecd353bd5bd2185fcd9c7097f058
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122445072"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Azure 文件同步代理和防火墙设置
Azure 文件同步可以将本地服务器连接到 Azure 文件，启用多站点同步和云分层功能。 因此，本地服务器必须连接到 Internet。 IT 管理员需确定服务器访问 Azure 云服务的最佳路径。

本文介绍需要完成哪些具体的要求和选项才能成功地将服务器安全地连接到 Azure 文件同步。

在阅读本操作指南之前，我们建议先阅读 [Azure 文件同步的网络注意事项](file-sync-networking-overview.md)。

## <a name="overview"></a>概述
Azure 文件同步在 Windows Server、Azure 文件共享和多项其他的 Azure 服务之间充当业务流程服务，用于同步同步组中所述的数据。 需将服务器配置为与以下 Azure 服务通信，确保 Azure 文件同步正常工作：

- Azure 存储
- Azure 文件同步
- Azure 资源管理器
- 身份验证服务

> [!Note]  
> Windows Server 上的 Azure 文件同步代理会启动到云服务的所有请求，因此从防火墙的角度来看，只需考虑出站流量。 <br /> 没有任何 Azure 服务会启动到 Azure 文件同步代理的连接。

## <a name="ports"></a>端口
Azure 文件同步以独占方式通过 HTTPS 移动文件数据和元数据，要求端口 443 对外开放。
因此，所有流量都是加密的。

## <a name="networks-and-special-connections-to-azure"></a>网络以及到 Azure 的特殊连接
对于到 Azure 的特殊通道（例如 [ExpressRoute](../../expressroute/expressroute-introduction.md) 等），Azure 文件同步代理没有任何要求。

Azure 文件同步会通过任何可用方式来访问 Azure，自动适应各种网络特征（例如带宽、延迟）并提供进行微调所需的管理员控制。 目前并没有提供所有功能。 如果需要配置特定的行为，请通过 [Azure 文件 UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670) 告知我们。

## <a name="proxy"></a>代理
Azure 文件同步支持特定于应用和计算机范围的代理设置。

**特定于应用的代理设置** 可以配置专用于 Azure 文件同步流量的代理。 代理版本 4.0.1.0 或更高版本支持特定于应用的代理设置，可以在代理安装期间或使用 Set-StorageSyncProxyConfiguration PowerShell cmdlet 进行配置。

用于配置特定于应用的代理设置的 PowerShell 命令：
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
例如，如果代理服务器要求使用用户名和密码进行身份验证，请运行以下 PowerShell 命令：

```powershell
# IP address or name of the proxy server.
$Address="127.0.0.1"  

# The port to use for the connection to the proxy.
$Port=8080

# The user name for a proxy.
$UserName="user_name" 

# Please type or paste a string with a password for the proxy.
$SecurePassword = Read-Host -AsSecureString

$Creds = New-Object System.Management.Automation.PSCredential ($UserName, $SecurePassword)

# Please verify that you have entered the password correctly.
Write-Host $Creds.GetNetworkCredential().Password

Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"

Set-StorageSyncProxyConfiguration -Address $Address -Port $Port -ProxyCredential $Creds
```
**计算机范围的代理设置** 对 Azure 文件同步代理来说是透明的，因为服务器的整个流量都通过该代理路由。

若要配置计算机范围的代理设置，请执行以下步骤： 

1. 配置 .NET 应用程序的代理设置 

   - 编辑以下这两个文件：  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - 在 machine.config 文件中添加 <system.net> 节（在 <system.serviceModel> 节下）。  将 127.0.01:8888 更改为代理服务器的 IP 地址和端口。 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. 设置 WinHTTP 代理设置 

   > [!Note]  
   > 可通过几种方法（WPAD、PAC 文件、netsh 等）将 Windows Server 配置为使用代理服务器。 以下步骤介绍如何使用 netsh 配置代理设置，但[在 Windows 中配置代理服务器设置](/troubleshoot/windows-server/networking/configure-proxy-server-settings)文档中列出的任何方法都是受支持的。


   - 从提升的命令提示符或 PowerShell 运行以下命令来查看现有的代理设置：   

     netsh winhttp show proxy

   - 从提升的命令提示符或 PowerShell 运行以下命令来设置代理设置（将 127.0.01:8888 更改为代理服务器的 IP 地址和端口）：  

     netsh winhttp set proxy 127.0.0.1:8888

3. 通过从提升的命令提示符或 PowerShell 运行以下命令，重新启动存储同步代理服务： 

      net stop filesyncsvc

      注意：存储同步代理 (filesyncsvc) 服务在停止后会自动启动。

## <a name="firewall"></a>防火墙
如前面的部分所述，端口 443 需对外开放。 可能需要进一步对通过此端口流向特定域的流量进行限制，具体取决于所在数据中心、分支或区域的策略。

下表介绍了进行通信所需的域：

| 服务 | 公有云终结点 | Azure 政府版终结点 | 使用情况 |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | `https://management.azure.com` | https://management.usgovcloudapi.net | 包括初始服务器注册调用在内的任何用户调用（例如 PowerShell）都会转到/经过此 URL。 |
| **Azure Active Directory** | https://login.windows.net<br>`https://login.microsoftonline.com` | https://login.microsoftonline.us | Azure 资源管理器调用必须由经过身份验证的用户发出。 若要成功，请使用此 URL 进行用户身份验证。 |
| **Azure Active Directory** | https://graph.microsoft.com/ | https://graph.microsoft.com/ | 在部署 Azure 文件同步的过程中，将在订阅的 Azure Active Directory 中创建服务主体。 此 URL 用于该操作。 此主体用于将最小的一组权限委托给 Azure 文件同步服务。 对 Azure 文件同步进行初始设置的用户必须是经过身份验证且具有订阅所有者特权的用户。 |
| **Azure Active Directory** | https://secure.aadcdn.microsoftonline-p.com | 使用公共终结点 URL。 | 此 URL 可供 Active Directory 身份验证库访问，该库由 Azure 文件同步服务器注册 UI 用来以管理员身份登录。 |
| **Azure 存储** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | 服务器在下载某个文件时，可以直接与存储帐户中的 Azure 文件共享通信，从而提高数据移动效率。 服务器有一个 SAS 密钥，只允许进行针对性的文件共享访问。 |
| **Azure 文件同步** | &ast;.one.microsoft.com<br>&ast;.afs.azure.net | &ast;.afs.azure.us | 在完成初始服务器注册以后，服务器会收到一个区域 URL，适用于该区域中的 Azure 文件同步服务实例。 服务器可以使用此 URL 直接且高效地与负责其同步的实例通信。 |
| **Microsoft PKI** |  https://www.microsoft.com/pki/mscorp/cps<br>http://crl.microsoft.com/pki/mscorp/crl/<br>http://mscrl.microsoft.com/pki/mscorp/crl/<br>http://ocsp.msocsp.com<br>http://ocsp.digicert.com/<br>http://crl3.digicert.com/ | https://www.microsoft.com/pki/mscorp/cps<br>http://crl.microsoft.com/pki/mscorp/crl/<br>http://mscrl.microsoft.com/pki/mscorp/crl/<br>http://ocsp.msocsp.com<br>http://ocsp.digicert.com/<br>http://crl3.digicert.com/ | 安装 Azure 文件同步代理后，PKI URL 用于下载与 Azure 文件同步服务和 Azure 文件共享进行通信所需的中间证书。 OCSP URL 用于检查证书的状态。 |
| **Microsoft Update** | &ast;.update.microsoft.com<br>&ast;.download.windowsupdate.com<br>&ast;.ctldl.windowsupdate.com<br>&ast;.dl.delivery.mp.microsoft.com<br>&ast;.emdl.ws.microsoft.com | &ast;.update.microsoft.com<br>&ast;.download.windowsupdate.com<br>&ast;.ctldl.windowsupdate.com<br>&ast;.dl.delivery.mp.microsoft.com<br>&ast;.emdl.ws.microsoft.com | 安装 Azure 文件同步代理后，Microsoft 更新 URL 用于下载 Azure 文件同步代理更新。 |

> [!Important]
> 在允许流量流向 &ast;.afs.azure.net 时，流量只能流向同步服务。 没有其他 Microsoft 服务在使用此域。
> 如果允许流量通往 &ast;.one.microsoft.com，则可以让流量从服务器通往除同步服务之外的其他服务。 子域下还有更多可用的 Microsoft 服务。

如果 &ast;.afs.azure.net 或 &ast;.one.microsoft.com 范围太广，则可限制服务器的通信，只允许与 Azure 文件同步服务的显式区域性实例通信。 选择哪个或哪些实例取决于向其部署和注册了服务器的存储同步服务的区域。 在下表中，该区域称为“主终结点 URL”。

出于业务连续性和灾难恢复 (BCDR) 原因，你可能已在配置为异地冗余存储 (GRS) 的存储帐户中创建了 Azure 文件共享。 如果是这样，则在发生长时间的区域性中断时，Azure 文件共享会将故障转移到配对区域。 Azure 文件同步使用的区域配对与存储相同。 因此，如果使用 GRS 存储帐户，则需要启用附加的 URL 才能让服务器与 Azure 文件同步的配对区域通信。在下表中，此配对称为“配对区域”。 此外，还需要启用一个流量管理器配置文件 URL。 在发生故障转移时，此 URL 可确保将网络流量无缝重新路由到配对区域；在下表中，此 URL 称为“发现 URL”。

| 云  | 区域 | 主终结点 URL | 配对区域 | 发现 URL |
|--------|--------|----------------------|---------------|---------------|
| 公用 |澳大利亚东部 | https:\//australiaeast01.afs.azure.net<br>https:\//kailani-aue.one.microsoft.com | Australia Southeast | https:\//tm-australiaeast01.afs.azure.net<br>https:\//tm-kailani-aue.one.microsoft.com |
| 公用 |Australia Southeast | https:\//australiasoutheast01.afs.azure.net<br>https:\//kailani-aus.one.microsoft.com | 澳大利亚东部 | https:\//tm-australiasoutheast01.afs.azure.net<br>https:\//tm-kailani-aus.one.microsoft.com |
| 公用 | Brazil South | https:\//brazilsouth01.afs.azure.net | 美国中南部 | https:\//tm-brazilsouth01.afs.azure.net |
| 公用 | 加拿大中部 | https:\//canadacentral01.afs.azure.net<br>https:\//kailani-cac.one.microsoft.com | 加拿大东部 | https:\//tm-canadacentral01.afs.azure.net<br>https:\//tm-kailani-cac.one.microsoft.com |
| 公用 | 加拿大东部 | https:\//canadaeast01.afs.azure.net<br>https:\//kailani-cae.one.microsoft.com | 加拿大中部 | https:\//tm-canadaeast01.afs.azure.net<br>https:\//tm-kailani.cae.one.microsoft.com |
| 公用 | 印度中部 | https:\//centralindia01.afs.azure.net<br>https:\//kailani-cin.one.microsoft.com | 印度南部 | https:\//tm-centralindia01.afs.azure.net<br>https:\//tm-kailani-cin.one.microsoft.com |
| 公用 | 美国中部 | https:\//centralus01.afs.azure.net<br>https:\//kailani-cus.one.microsoft.com | 美国东部 2 | https:\//tm-centralus01.afs.azure.net<br>https:\//tm-kailani-cus.one.microsoft.com |
| 公用 | 东亚 | https:\//eastasia01.afs.azure.net<br>https:\//kailani11.one.microsoft.com | Southeast Asia | https:\//tm-eastasia01.afs.azure.net<br>https:\//tm-kailani11.one.microsoft.com |
| 公用 | 美国东部 | https:\//eastus01.afs.azure.net<br>https:\//kailani1.one.microsoft.com | 美国西部 | https:\//tm-eastus01.afs.azure.net<br>https:\//tm-kailani1.one.microsoft.com |
| 公用 | 美国东部 2 | https:\//eastus201.afs.azure.net<br>https:\//kailani-ess.one.microsoft.com | 美国中部 | https:\//tm-eastus201.afs.azure.net<br>https:\//tm-kailani-ess.one.microsoft.com |
| 公用 | 德国北部 | https:\//germanynorth01.afs.azure.net | 德国中西部 | https:\//tm-germanywestcentral01.afs.azure.net |
| 公用 | 德国中西部 | https:\//germanywestcentral01.afs.azure.net | 德国北部 | https:\//tm-germanynorth01.afs.azure.net |
| 公用 | Japan East | https:\//japaneast01.afs.azure.net | 日本西部 | https:\//tm-japaneast01.afs.azure.net |
| 公用 | 日本西部 | https:\//japanwest01.afs.azure.net | Japan East | https:\//tm-japanwest01.afs.azure.net |
| 公用 | 韩国中部 | https:\//koreacentral01.afs.azure.net/ | 韩国南部 | https:\//tm-koreacentral01.afs.azure.net/ |
| 公用 | 韩国南部 | https:\//koreasouth01.afs.azure.net/ | 韩国中部 | https:\//tm-koreasouth01.afs.azure.net/ |
| 公用 | 美国中北部 | https:\//northcentralus01.afs.azure.net | 美国中南部 | https:\//tm-northcentralus01.afs.azure.net |
| 公用 | 北欧 | https:\//northeurope01.afs.azure.net<br>https:\//kailani7.one.microsoft.com | 西欧 | https:\//tm-northeurope01.afs.azure.net<br>https:\//tm-kailani7.one.microsoft.com |
| 公用 | 美国中南部 | https:\//southcentralus01.afs.azure.net | 美国中北部 | https:\//tm-southcentralus01.afs.azure.net |
| 公用 | 印度南部 | https:\//southindia01.afs.azure.net<br>https:\//kailani-sin.one.microsoft.com | 印度中部 | https:\//tm-southindia01.afs.azure.net<br>https:\//tm-kailani-sin.one.microsoft.com |
| 公用 | Southeast Asia | https:\//southeastasia01.afs.azure.net<br>https:\//kailani10.one.microsoft.com | 东亚 | https:\//tm-southeastasia01.afs.azure.net<br>https:\//tm-kailani10.one.microsoft.com |
| 公用 | 瑞士北部 | https:\//switzerlandnorth01.afs.azure.net<br>https:\//tm-switzerlandnorth01.afs.azure.net | 瑞士西部 | https:\//switzerlandwest01.afs.azure.net<br>https:\//tm-switzerlandwest01.afs.azure.net |
| 公用 | 瑞士西部 | https:\//switzerlandwest01.afs.azure.net<br>https:\//tm-switzerlandwest01.afs.azure.net | 瑞士北部 | https:\//switzerlandnorth01.afs.azure.net<br>https:\//tm-switzerlandnorth01.afs.azure.net |
| 公用 | 英国南部 | https:\//uksouth01.afs.azure.net<br>https:\//kailani-uks.one.microsoft.com | 英国西部 | https:\//tm-uksouth01.afs.azure.net<br>https:\//tm-kailani-uks.one.microsoft.com |
| 公用 | 英国西部 | https:\//ukwest01.afs.azure.net<br>https:\//kailani-ukw.one.microsoft.com | 英国南部 | https:\//tm-ukwest01.afs.azure.net<br>https:\//tm-kailani-ukw.one.microsoft.com |
| 公用 | 美国中西部 | https:\//westcentralus01.afs.azure.net | 美国西部 2 | https:\//tm-westcentralus01.afs.azure.net |
| 公用 | 西欧 | https:\//westeurope01.afs.azure.net<br>https:\//kailani6.one.microsoft.com | 北欧 | https:\//tm-westeurope01.afs.azure.net<br>https:\//tm-kailani6.one.microsoft.com |
| 公用 | 美国西部 | https:\//westus01.afs.azure.net<br>https:\//kailani.one.microsoft.com | 美国东部 | https:\//tm-westus01.afs.azure.net<br>https:\//tm-kailani.one.microsoft.com |
| 公用 | 美国西部 2 | https:\//westus201.afs.azure.net | 美国中西部 | https:\//tm-westus201.afs.azure.net |
| 政府机关 | US Gov 亚利桑那州 | https:\//usgovarizona01.afs.azure.us | US Gov 德克萨斯州 | https:\//tm-usgovarizona01.afs.azure.us |
| 政府机关 | US Gov 德克萨斯州 | https:\//usgovtexas01.afs.azure.us | US Gov 亚利桑那州 | https:\//tm-usgovtexas01.afs.azure.us |

- 如果使用为本地冗余存储 (LRS) 或区域冗余存储 (ZRS) 配置的存储帐户，则只需启用“主终结点 URL”下列出的 URL。

- 如果使用为 GRS 配置的存储帐户，请启用三个 URL。

**示例：** 在 `"West US"` 中部署存储同步服务并在其中注册自己的服务器。 在本例中，允许与服务器通信的 URL 为：

> - https:\//westus01.afs.azure.net（主终结点：美国西部）
> - https:\//eastus01.afs.azure.net（配对的故障转移区域：美国东部）
> - https:\//tm-westus01.afs.azure.net（主要区域的发现 URL）

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>Azure 文件同步 IP 地址的允许列表
Azure 文件同步支持使用[服务标记](../../virtual-network/service-tags-overview.md)，这些标记表示给定 Azure 服务的一组 IP 地址前缀。 可以使用服务标记来创建防火墙规则，允许与 Azure 文件同步服务通信。 Azure 文件同步的服务标记为 `StorageSyncService`。

如果在 Azure 中使用 Azure 文件同步，则可以直接在网络安全组中使用服务标记的名称来允许流量。 若要了解有关如何执行此操作的详细信息，请参阅[网络安全组](../../virtual-network/network-security-groups-overview.md)。

如果是在本地使用 Azure 文件同步，则可以使用服务标记 API 为防火墙的允许列表获取特定的 IP 地址范围。 可以通过两种方法获取此信息：

- 支持服务标记的所有 Azure 服务的最新 IP 地址范围列表每周都会以 JSON 文档的形式在 Microsoft 下载中心上发布。 每个 Azure 云都有自己的 JSON 文档，其中包含与该云相关的 IP 地址范围：
    - [Azure 公用](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure 美国政府版](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure 中国](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure 德国](https://www.microsoft.com/download/details.aspx?id=57064)
- 服务标记发现 API（预览版）允许以编程方式检索最新服务标记列表。 在预览版中，服务标记发现 API 返回的信息可能早于从 Microsoft 下载中心发布的 JSON 文档返回的信息。 可以根据自动化首选项使用 API 接口：
    - [REST API](/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure CLI](/cli/azure/network#az_network_list_service_tags)

由于服务标记发现 API 的更新频率与 JSON 文档发布到 Microsoft 下载中心的频率不同，因此我们建议使用 JSON 文档来更新本地防火墙的允许列表。 可以按以下步骤来完成：

```PowerShell
# The specific region to get the IP address ranges for. Replace westus2 with the desired region code 
# from Get-AzLocation.
$region = "westus2"

# The service tag for Azure File Sync. Do not change unless you're adapting this
# script for another service.
$serviceTag = "StorageSyncService"

# Download date is the string matching the JSON document on the Download Center. 
$possibleDownloadDates = 0..7 | `
    ForEach-Object { [System.DateTime]::Now.AddDays($_ * -1).ToString("yyyyMMdd") }

# Verify the provided region
$validRegions = Get-AzLocation | `
    Where-Object { $_.Providers -contains "Microsoft.StorageSync" } | `
    Select-Object -ExpandProperty Location

if ($validRegions -notcontains $region) {
    Write-Error `
            -Message "The specified region $region is not available. Either Azure File Sync is not deployed there or the region does not exist." `
            -ErrorAction Stop
}

# Get the Azure cloud. This should automatically based on the context of 
# your Az PowerShell login, however if you manually need to populate, you can find
# the correct values using Get-AzEnvironment.
$azureCloud = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty Name

# Build the download URI
$downloadUris = @()
switch($azureCloud) {
    "AzureCloud" { 
        $downloadUris = $possibleDownloadDates | ForEach-Object {  
            "https://download.microsoft.com/download/7/1/D/71D86715-5596-4529-9B13-DA13A5DE5B63/ServiceTags_Public_$_.json"
        }
    }

    "AzureUSGovernment" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/6/4/D/64DB03BF-895B-4173-A8B1-BA4AD5D4DF22/ServiceTags_AzureGovernment_$_.json"
        }
    }

    "AzureChinaCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/9/D/0/9D03B7E2-4B80-4BF3-9B91-DA8C7D3EE9F9/ServiceTags_China_$_.json"
        }
    }

    "AzureGermanCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/0/7/6/076274AB-4B0B-4246-A422-4BAF1E03F974/ServiceTags_AzureGermany_$_.json"
        }
    }

    default {
        Write-Error -Message "Unrecognized Azure Cloud: $_" -ErrorAction Stop
    }
}

# Find most recent file
$found = $false 
foreach($downloadUri in $downloadUris) {
    try { $response = Invoke-WebRequest -Uri $downloadUri -UseBasicParsing } catch { }
    if ($response.StatusCode -eq 200) {
        $found = $true
        break
    }
}

if ($found) {
    # Get the raw JSON 
    $content = [System.Text.Encoding]::UTF8.GetString($response.Content)

    # Parse the JSON
    $serviceTags = ConvertFrom-Json -InputObject $content -Depth 100

    # Get the specific $ipAddressRanges
    $ipAddressRanges = $serviceTags | `
        Select-Object -ExpandProperty values | `
        Where-Object { $_.id -eq "$serviceTag.$region" } | `
        Select-Object -ExpandProperty properties | `
        Select-Object -ExpandProperty addressPrefixes
} else {
    # If the file cannot be found, that means there hasn't been an update in
    # more than a week. Please verify the download URIs are still accurate
    # by checking https://docs.microsoft.com/azure/virtual-network/service-tags-overview
    Write-Verbose -Message "JSON service tag file not found."
    return
}
```

然后，可以使用 `$ipAddressRanges` 中的 IP 地址范围来更新防火墙。 请查看防火墙/网络设备的网站，了解如何更新防火墙。

## <a name="test-network-connectivity-to-service-endpoints"></a>测试服务终结点的网络连接性
在向 Azure 文件同步服务注册服务器后，Test-StorageSyncNetworkConnectivity cmdlet 和 ServerRegistration.exe 可用于测试与特定于此服务器的所有终结点 (URL) 的通信。 此 cmdlet 有助于在未完成的通信阻止服务器完全使用 Azure 文件同步时进行故障排除，并且可用于微调代理和防火墙配置。

若要运行网络连接性测试，请安装 Azure 文件同步代理 9.1 或更高版本，然后运行以下 PowerShell 命令：
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>摘要和风险限制
本文档前面部分的列表包含 Azure 文件同步目前用来通信的 URL。 防火墙必须能够允许发往这些域的出站流量。 Microsoft 会不断更新此列表。

设置进行域限制的防火墙规则可以作为一项改进安全性的措施。 如果使用这些防火墙配置，则需注意，URL 会不断添加，甚至会随时更改。 请不时地回来查看本文。

## <a name="next-steps"></a>后续步骤
- [规划 Azure 文件同步部署](file-sync-planning.md)
- [部署 Azure 文件同步](file-sync-deployment-guide.md)
- [监视 Azure 文件同步](file-sync-monitoring.md)
