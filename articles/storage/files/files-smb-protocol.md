---
title: Azure 文件存储中的 SMB 文件共享
description: 了解托管在使用服务器消息块 (SMB) 协议的 Azure 文件存储中的文件共享。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/25/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 15a3064ab02a9c83a489219210be32ee99134e47
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723349"
---
# <a name="smb-file-shares-in-azure-files"></a>Azure 文件存储中的 SMB 文件共享
Azure 文件存储提供两种用于装载 Azure 文件共享的行业标准协议：[服务器消息块 (SMB)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) 协议和[网络文件系统 (NFS)](https://en.wikipedia.org/wiki/Network_File_System) 协议（预览版）。 通过 Azure 文件存储，可以选择最适合你的工作负荷的文件系统协议。 尽管可以在同一存储帐户中创建 SMB 和 NFS 文件共享，但 Azure 文件共享不支持同时使用 SMB 和 NFS 协议访问单个 Azure 文件共享。 对于所有文件共享，Azure 文件存储提供企业级文件共享，这些共享可以纵向扩展以满足你的存储需求，并且可同时由数千个客户端访问。

本文将介绍 SMB Azure 文件共享。 有关 NFS Azure 文件共享的信息，请参阅 [Azure 文件存储中的 NFS 文件共享](files-nfs-protocol.md)。

## <a name="common-scenarios"></a>常见方案
SMB 文件共享用于各种应用程序，其中包括终端用户文件共享和支持数据库和应用程序的文件共享。 SMB 文件共享通常用于以下场景：

- 终端用户文件共享，例如团队共享和主目录等。
- 为基于 Windows 的应用程序提供存储支持，例如 SQL Server 数据库或为 Win32 或 .NET 本地文件系统 API 编写的业务线应用程序。 
- 新的应用程序和服务开发，尤其是当该应用程序或服务需要随机 IO 和分层存储时。

## <a name="features"></a>功能
Azure 文件存储支持 SMB 和 Azure 的主要功能，这些功能是 SMB 文件共享的生产部署所需的：

- AD 域加入和自定义访问控制列表 (DACL)。
- 使用 Azure 备份的集成的无服务器备份。
- 使用 Azure 专用终结点的网络隔离。
- 使用 SMB 多通道的高网络吞吐量（仅限高级文件共享）。
- SMB 通道加密，包括 AES-256-GCM、AES-128-GCM 和 AES-128-CCM。
- 通过 VSS 集成共享快照支持先前版本。
- 针对 Azure 文件共享的自动软删除以防止意外删除。
- 使用 Internet 安全的 SMB 3.0+ 的 Internet 可访问文件共享（可选）。

SMB 文件共享可以直接装载在本地，也可以[使用 Azure 文件同步缓存在本地](../file-sync/file-sync-introduction.md)。  

## <a name="security"></a>安全性
使用 Azure 存储服务加密 (SSE) 对存储在 Azure 文件存储中的所有数据进行静态加密。 存储服务加密的工作方式类似于 Windows 上的 BitLocker：在文件系统级别下对数据进行加密。 由于数据在 Azure 文件共享的文件系统下加密，因此，在将数据编码到磁盘时，无需访问客户端上的基础密钥即可读取或写入 Azure 文件共享。 静态加密同时适用于 SMB 和 NFS 协议。

默认情况下，所有 Azure 存储帐户均已启用传输中加密。 即通过 SMB 装载文件共享（或通过 FileREST 协议访问文件共享）时，Azure 文件存储仅允许通过加密或 HTTPS 使用 SMB 3.x 建立的连接。 如果启用了传输中加密，不支持使用 SMB 通道加密的 SMB 3.x 的客户端将无法装载 Azure 文件共享。 

与 Windows 10 21H1 版本一起使用时，Azure 文件存储支持行业领先的 AES-256-GCM 与 SMB 3.1.1。 SMB 3.1.1 还支持 AES-128-GCM，并且 SMB 3.0 支持 AES-128-CCM。 出于性能原因，AES-128-GCM 是 Windows 10 21H1 版本上默认协商的加密。

可以为 Azure 存储帐户禁用传输中加密。 禁用加密后，Azure 文件存储还将允许没有加密的 SMB 2.1 和 SMB 3.x。 禁用传输中加密的主要原因是为了支持必须在更低版本的操作系统（例如，Windows Server 2008 R2 或更低版本的 Linux 发行版）上运行的旧版应用程序。 Azure 文件存储仅允许在与 Azure 文件共享相同的 Azure 区域内建立 SMB 2.1 连接；Azure 文件共享的 Azure 区域之外的 SMB 2.1 客户端（例如，本地或其他 Azure 区域）将无法访问文件共享。

## <a name="smb-protocol-settings"></a>SMB 协议设置
Azure 文件存储为 SMB 协议提供多个设置。

- **SMB 多通道**：启用/禁用 SMB 多通道（仅限高级文件共享）。 要了解如何启用 SMB 多通道，请参阅[在 FileStorage 存储帐户上启用 SMB 多通道](storage-files-enable-smb-multichannel.md)。 SMB 多通道默认处于禁用状态。
- **SMB 版本**：允许哪些 SMB 版本。 支持的协议版本为 SMB 3.1.1、SMB 3.0 和 SMB 2.1。 默认情况下，支持所有的 SMB 版本，但如果启用了“需要安全传输”，则不支持 SMB 2.1，因为 SMB 2.1 不支持传输中加密。
- **身份验证方法**：允许哪些 SMB 身份验证方法。 支持的身份验证方法为 NTLMv2 和 Kerberos。 默认情况下，允许所有的身份验证方法。 如果删除了 NTLMv2，则无法使用存储帐户密钥装载 Azure 文件共享。
- **Kerberos 票证加密**：允许哪些加密算法。 支持的加密算法为 RC4-HMAC 和 AES-256。
- **SMB 通道加密**：允许哪些 SMB 通道加密算法。 支持的加密算法为 AES-256-GCM、AES-128-GCM 和 AES-128-CCM。

可以通过 Azure PowerShell 模块切换 SMB 协议设置。

# <a name="portal"></a>[Portal](#tab/azure-portal)
可以使用 PowerShell 或 CLI 查看和更改 SMB 协议设置。 请选择所需的选项卡以查看有关如何获取和设置 SMB 协议设置的步骤。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
要获取或设置 SMB 协议设置，必须提供对存储帐户的引用，方法是直接提供资源组和存储帐户名称，或者提供从 `Get-AzStorageAccount` cmdlet 获取的存储帐户对象。 以下示例使用第二种方法来获取和设置 SMB 协议设置。

在运行这些 PowerShell 命令之前，请记得将 `<resource-group>` 和 `<storage-account>` 替换为适合你的环境的值。

```PowerShell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName
```

要获取 SMB 协议设置，请使用 `Get-AzStorageFileServiceProperty` cmdlet。 如果从未修改过 SMB 协议设置，此 cmdlet 返回的值将为 NULL。 NULL 返回值应解释为“默认设置生效”。 为了使用户更易于理解，以下 PowerShell 命令将 NULL 值替换为了人类可读的默认值。 

```PowerShell
# Replacement values for null parameters. If you copy this into your own 
# scripts, you will need to ensure that you keep these variables up-to-date with any new 
# options we may add to these parameters in the future.
$smbMultichannelEnabled = $false
$smbProtocolVersions = "SMB2.1", "SMB3.0", "SMB3.1.1"
$smbAuthenticationMethods = "NTLMv2", "Kerberos"
$smbKerberosTicketEncryption = "RC4-HMAC", "AES-256"
$smbChannelEncryption = "AES-128-CCM", "AES-128-GCM", "AES-256-GCM"

Get-AzStorageFileServiceProperty -StorageAccount $storageAccount | `
    Select-Object -Property `
        ResourceGroupName, `
        StorageAccountName, `
        @{ 
            Name = "SmbMultichannelEnabled"; 
            Expression = { 
                if ($null -eq $_.ProtocolSettings.Smb.Multichannel.Enabled) { 
                    $smbMultichannelEnabled 
                } else { 
                    $_.ProtocolSettings.Smb.Multichannel.Enabled 
                } 
            } 
        }, 
        @{ 
            Name = "SmbProtocolVersions";
            Expression = {
                if ($null -eq $_.ProtocolSettings.Smb.Versions) {
                    [String]::Join(", ", $smbProtocolVersions)
                } else {
                    [String]::Join(", ", $_.ProtocolSettings.Smb.Versions)
                }
            }
        },
        @{
            Name = "SmbChannelEncryption";
            Expression = {
                if ($null -eq $_.ProtocolSettings.Smb.ChannelEncryption) {
                    [String]::Join(", ", $smbChannelEncryption)
                } else {
                    [String]::Join(", ", $_.ProtocolSettings.Smb.ChannelEncryption)
                }
            }
        },
        @{
            Name = "SmbAuthenticationMethods";
            Expression = {
                if ($null -eq $_.ProtocolSettings.Smb.AuthenticationMethods) {
                    [String]::Join(", ", $smbAuthenticationMethods)
                } else {
                    [String]::Join(", ", $_.ProtocolSettings.Smb.AuthenticationMethods)
                }
            }
        },
        @{
            Name = "SmbKerberosTicketEncryption";
            Expression = {
                if ($null -eq $_.ProtocolSettings.Smb.KerberosTicketEncryption) {
                    [String]::Join(", ", $smbKerberosTicketEncryption)
                } else {
                    [String]::Join(", ", $_.ProtocolSettings.Smb.KerberosTicketEncryption)
                }
            }
        }
```

根据组织的安全性、性能和兼容性要求，你可能需要修改 SMB 协议设置。 以下 PowerShell 命令将 SMB 文件共享限制为仅使用最安全的选项。

> [!Important]  
> 将 SMB Azure 文件共享限制为仅使用最安全的选项时，可能会导致某些客户端在不满足要求时无法连接。 例如，从 Windows 10 版本 21H1 开始，引入了 AES-256-GCM 作为 SMB 通道加密的一种选项。 这意味着不支持 AES-256-GCM 的旧客户端将无法连接。

```PowerShell
Update-AzStorageFileServiceProperty `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -SmbAuthenticationMethod "Kerberos" `
    -SmbChannelEncryption "AES-256-GCM" `
    -SmbKerberosTicketEncryption "AES-256" `
    -SmbProtocolVersion "SMB3.1.1"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
要获取 SMB 协议设置，请使用 `az storage account file-service-properties show` 命令。 如果从未修改过 SMB 协议设置，该命令返回的值将为 NULL。 NULL 返回值应解释为“默认设置生效”。 为了使用户更易于理解，以下 Bash 命令将 NULL 值替换为人类可读的默认值。 

```bash
resourceGroupName="<resource-group>"
storageAccountName="<storage-account>"

# Values to be replaced
replaceSmbMultichannel="\"smbMultichannelEnabled\": null"
replaceSmbProtocolVersion="\"smbProtocolVersions\": null"
replaceSmbChannelEncryption="\"smbChannelEncryption\": null"
replaceSmbAuthenticationMethods="\"smbAuthenticationMethods\": null"
replaceSmbKerberosTicketEncryption="\"smbKerberosTicketEncryption\": null"

# Replacement values for null parameters. If you copy this into your own 
# scripts, you will need to ensure that you keep these variables up-to-date with any new 
# options we may add to these parameters in the future.
defaultSmbMultichannelEnabled="\"smbMultichannelEnabled\": false"
defaultSmbProtocolVersions="\"smbProtocolVersions\": \"SMB2.1;SMB3.0;SMB3.1.1\""
defaultSmbChannelEncryption="\"smbChannelEncryption\": \"AES-128-CCM;AES-128-GCM;AES-256-GCM\""
defaultSmbAuthenticationMethods="\"smbAuthenticationMethods\": \"NTLMv2;Kerberos\""
defaultSmbKerberosTicketEncryption="\"smbKerberosTicketEncryption\": \"RC4-HMAC;AES-256\""

# Build JMESPath query string
query="{" 
query="${query}smbMultichannelEnabled: protocolSettings.smb.multichannel.enabled,"
query="${query}smbProtocolVersions: protocolSettings.smb.versions,"
query="${query}smbChannelEncryption: protocolSettings.smb.channelEncryption,"
query="${query}smbAuthenticationMethods: protocolSettings.smb.authenticationMethods,"
query="${query}smbKerberosTicketEncryption: protocolSettings.smb.kerberosTicketEncryption"
query="${query}}"

# Get protocol settings from the Azure Files FileService object
protocolSettings=$(az storage account file-service-properties show \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "${query}")

# Replace returned values if null with default values 
protocolSettings="${protocolSettings/$replaceSmbMultichannel/$defaultSmbMultichannelEnabled}"
protocolSettings="${protocolSettings/$replaceSmbProtocolVersion/$defaultSmbProtocolVersion}"
protocolSettings="${protocolSettings/$replaceSmbChannelEncryption/$defaultSmbChannelEncryption}"
protocolSettings="${protocolSettings/$replaceSmbAuthenticationMethods/$defaultSmbAuthenticationMethods}"
protocolSettings="${protocolSettings/$replaceSmbKerberosTicketEncryption/$defaultSmbKerberosTicketEncryption}"

# Print returned settings
echo $protocolSettings
```

根据组织的安全性、性能和兼容性要求，你可能需要修改 SMB 协议设置。 以下 Azure CLI 命令将 SMB 文件共享限制为仅使用最安全的选项。

> [!Important]  
> 将 SMB Azure 文件共享限制为仅使用最安全的选项时，可能会导致某些客户端在不满足要求时无法连接。 例如，从 Windows 10 版本 21H1 开始，引入了 AES-256-GCM 作为 SMB 通道加密的一种选项。 这意味着不支持 AES-256-GCM 的旧客户端将无法连接。

```bash
az storage account file-service-properties update \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --versions "SMB3.1.1" \
    --channel-encryption "AES-256-GCM" \
    --auth-methods "Kerberos" \
    --kerb-ticket-encryption "AES-256"
```
---

## <a name="limitations"></a>限制
Azure 文件存储中的 SMB 文件共享支持 SMB 协议和 NTFS 文件系统支持的功能的子集。 尽管大多数用例和应用程序不需要这些功能，但如果某些应用程序依赖不受支持的功能，这些应用程序则可能无法在 Azure 文件存储中正常工作。 不支持以下功能：

- [SMB 直通](/windows-server/storage/file-server/smb-direct)  
- SMB 目录租用
- [SMB 文件共享的 VSS](/archive/blogs/clausjor/vss-for-smb-file-shares)（这使 VSS 提供程序能够在拍摄快照之前将它们的数据刷新到 SMB 文件共享）
- 备用数据流
- 扩展的属性
- 对象标识符
- [硬链接](/windows/win32/fileio/hard-links-and-junctions)
- [软链接](/windows/win32/fileio/creating-symbolic-links)  
- [重分析点](/windows/win32/fileio/reparse-points)  
- [稀疏文件](/windows/win32/fileio/sparse-files)
- [短文件名（8.3 别名）](/windows/win32/fileio/naming-a-file#short-vs-long-names)  
- [压缩](https://techcommunity.microsoft.com/t5/itops-talk-blog/smb-compression-deflate-your-io/ba-p/1183552)

## <a name="regional-availability"></a>区域可用性
SMB Azure 文件共享可在每个 Azure 区域中使用，包括所有公共区域和主权区域。 高级 SMB 文件共享在[部分区域](https://azure.microsoft.com/global-infrastructure/services/?products=storage)中可用。

## <a name="next-steps"></a>后续步骤
- [规划 Azure 文件部署](storage-files-planning.md)
- [创建 Azure 文件共享](storage-how-to-create-file-share.md)
- 在你的首选操作系统上装载 SMB 文件共享：
    - [在 Windows 上装载 SMB 文件共享](storage-how-to-use-files-windows.md)
    - [在 Linux 上装载 SMB 文件共享](storage-how-to-use-files-linux.md)
    - [在 macOS 上装载 SMB 文件共享](storage-how-to-use-files-mac.md)