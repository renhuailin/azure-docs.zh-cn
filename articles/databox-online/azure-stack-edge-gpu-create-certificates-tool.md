---
title: 通过 Azure Stack Hub 就绪情况检查器工具为 Azure Stack Edge Pro GPU 创建证书
description: 介绍如何使用 Azure Stack Hub 准备情况检查器工具创建证书请求，然后在 Azure Stack Edge Pro GPU 设备上获取和安装证书。
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/01/2021
ms.author: alkohli
ms.openlocfilehash: 08201fafa3f69803e15a02c1535159101a1ecfea
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128660987"
---
# <a name="create-certificates-for-your-azure-stack-edge-pro-gpu-using-azure-stack-hub-readiness-checker-tool"></a>使用 Azure Stack Hub 就绪情况检查器工具为 Azure Stack Edge Pro GPU 创建证书 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍如何使用 Azure Stack Hub 准备情况检查器工具为 Azure Stack Edge Pro 创建证书。 

## <a name="using-azure-stack-hub-readiness-checker-tool"></a>使用 Azure Stack Hub 准备情况检查器工具

使用 Azure Stack Hub 准备情况检查器工具创建进行 Azure Stack Edge Pro 设备部署所需的证书签名请求 (CSR)。 你可以在为 Azure Stack Edge Pro 设备下单后，在等待设备到达期间创建这些请求。

> [!NOTE]
> 此工具仅用于测试或开发目的，不适用于生产设备。 

可以使用 Azure Stack Hub 就绪性检查器工具 (AzsReadinessChecker) 请求以下证书：

- Azure 资源管理器证书
- 本地 UI 证书
- 节点证书
- Blob 证书
- VPN 证书


## <a name="prerequisites"></a>先决条件

若要创建用于 Azure Stack Edge Pro 设备部署的 CSR，请确保： 

- 你有运行 Windows 10 或者 Windows Server 2016 或更高版本的客户端。 
- 你已将 Microsoft Azure Stack Hub 准备情况检查器工具从 [PowerShell 库](https://aka.ms/AzsReadinessChecker)下载到此系统上。
- 你有以下证书信息：
  - 设备名称
  - 节点序列号
  - 外部完全限定的域名 (FQDN)

## <a name="generate-certificate-signing-requests"></a>生成证书签名请求

通过以下步骤准备 Azure Stack Edge Pro 设备证书：

1. 以管理员身份运行 PowerShell（5.1 或更高版本）。
2. 安装 Azure Stack Hub 准备情况检查器工具。 在 PowerShell 提示符处，键入： 

    ```azurepowershell
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

    若要获取已安装的版本，请键入：  

    ```azurepowershell
    Get-InstalledModule -Name Microsoft.AzureStack.ReadinessChecker  | ft Name, Version 
    ```

3. 为所有证书创建一个目录（如果还没有）。 类型： 
    
    ```azurepowershell
    New-Item "C:\certrequest" -ItemType Directory
    ``` 
    
4. 若要创建证书请求，请提供以下信息。 如果要生成 VPN 证书，则其中的某些输入不适用。
    
    |输入 |说明  |
    |---------|---------|
    |`OutputRequestPath`|要在其中创建证书请求的本地客户端上的文件路径。        |
    |`DeviceName`|设备在其本地 Web UI 的“设备”页中的名称。 <br> VPN 证书不需要此字段。         |
    |`NodeSerialNumber`|设备节点在设备本地 Web UI 的“网络”页中的序列号。 <br> VPN 证书不需要此字段。       |
    |`ExternalFQDN`|设备本地 Web UI 的“设备”页中的 DNSDomain 值。         |
    |`RequestType`|请求类型可能是 `MultipleCSR`（不同的终结点使用不同的证书），也可能是 `SingleCSR`（所有终结点都使用同一个证书）。 <br> VPN 证书不需要此字段。     |

    对于除 VPN 证书之外的所有证书，请键入： 
    
    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeDEVICE'
        DeviceName = 'myTEA1'
        NodeSerialNumber = 'VM1500-00025'
        externalFQDN = 'azurestackedge.contoso.com'
        requestType = 'MultipleCSR'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    如果要创建 VPN 证书，请键入： 

    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeVPN'
        externalFQDN = 'azurestackedge.contoso.com'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    
5. 你会在目录（在上面的 OutputRequestPath 参数中指定）中找到证书请求文件。 使用 `MultipleCSR` 参数时，你会看到以下四个扩展名为 `.req` 的文件：

    
    |文件名  |证书请求的类型  |
    |---------|---------|
    |以 `DeviceName` 开头     |本地 Web UI 证书请求      |
    |以 `NodeSerialNumber` 开头     |节点证书请求         |
    |自 `login` 起     |Azure 资源管理器终结点证书请求       |
    |自 `wildcard` 起     |Blob 存储证书请求。 它包含通配符，因为它涵盖了你可以在设备上创建的所有存储帐户。          |
    |自 `AzureStackEdgeVPNCertificate` 起     |VPN 客户端证书请求。         |

    你还会看到一个 INF 文件夹。 它包含一个 management.\<edge-devicename\> 信息文件，该文件以明文形式的对证书进行了详细说明。  


6. 将这些文件（内部文件或公共文件）提交给证书颁发机构。 确保 CA 根据生成的请求来生成符合 Azure Stack Edge Pro 证书要求（针对[节点证书](azure-stack-edge-gpu-certificates-overview.md#node-certificates)、[终结点证书](azure-stack-edge-gpu-certificates-overview.md#endpoint-certificates)和[本地 UI 证书](azure-stack-edge-gpu-certificates-overview.md#local-ui-certificates)）的证书。

## <a name="prepare-certificates-for-deployment"></a>为部署准备证书

从证书颁发机构 (CA) 获取的证书文件必须导入和导出，其属性与 Azure Stack Edge Pro 设备的证书要求匹配。 请在生成了证书签名请求的系统上完成以下步骤。

- 若要导入这些证书，请按照[在访问 Azure Stack Edge Pro 设备的客户端上导入证书](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)中的步骤操作。

- 若要导出这些证书，请按照[从访问 Azure Stack Edge Pro 设备的客户端导出证书](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)中的步骤操作。


## <a name="validate-certificates"></a>验证证书

首先，需生成正确的文件夹结构，并将证书放入相应的文件夹中。 然后，你才能使用工具来验证证书。

1. 以管理员身份运行 PowerShell。

2. 若要生成适当的文件夹结构，请在提示符下键入：

    `New-AzsCertificateFolder -CertificateType AzureStackEdgeDevice -OutputPath "$ENV:USERPROFILE\Documents\AzureStackCSR"`

3. 将 PFX 密码转换为安全字符串。 类型：       

    `$pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString` 

4. 接下来，验证证书。 类型：

    `Invoke-AzsCertificateValidation -CertificateType AzureStackEdgeDevice -DeviceName mytea1 -NodeSerialNumber VM1500-00025 -externalFQDN azurestackedge.contoso.com -CertificatePath $ENV:USERPROFILE\Documents\AzureStackCSR\AzureStackEdge -pfxPassword $pfxPassword`

## <a name="next-steps"></a>后续步骤

[在设备上上传证书](azure-stack-edge-gpu-manage-certificates.md)。
