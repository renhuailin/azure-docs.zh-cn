---
title: 如何使用多租户托管权限在 Azure 上部署 Windows 10
description: 了解如何使用多租户托管权限来充分利用 Windows 软件保障权益，从而将本地许可证引入到 Azure 中。
author: mimckitt
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 2/2/2021
ms.author: mimckitt
ms.custom: rybaker, chmimckitt, devx-track-azurepowershell
ms.openlocfilehash: 08193cc933bfd7c1d183df119b1602270ecf6f74
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688503"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>如何使用多租户托管权限在 Azure 上部署 Windows 10 
**适用于：** :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集

对于其用户使用 Windows 10 企业版 E3/E5 或使用 Windows 虚拟桌面访问（用户订阅许可证或附加设备用户订阅许可证）的客户，通过使用 Windows 10 多租户托管权限，他们可以在云中使用其 Windows 10 许可证并在 Azure 上运行 Windows 10 虚拟机，无需购买其他许可证。 多租户托管权限仅适用于 Windows 10（版本 1703 或更高版本）。

有关详细信息，请参阅[适用于 Windows 10 的多租户托管](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)。

> [!NOTE]
> - 若要使用 Windows 7、8.1 和 10 映像进行开发或测试，请参阅[适用于开发/测试方案的 Azure 中的 Windows 客户端](client-images.md)
> - 有关 Windows Server 许可权益，请参阅 [Windows Server 映像的 Azure 混合使用权益](hybrid-use-benefit-licensing.md)。

## <a name="subscription-licenses-that-qualify-for-multitenant-hosting-rights"></a>符合多租户托管权限的订阅许可证

使用 [Microsoft 管理中心](/microsoft-365/admin/admin-overview/about-the-admin-center)，可以确认是否已向用户分配支持 Windows 10 的许可证。

> [!IMPORTANT]
> 用户必须具有以下订阅许可证之一才能在 Azure 中使用 Windows 10 映像处理任何生产工作负载。 如果你没有这些订阅许可证，可通过[云服务合作伙伴](https://azure.microsoft.com/overview/choosing-a-cloud-service-provider/)或直接通过 [Microsoft](https://www.microsoft.com/microsoft-365?rtc=1) 购买。

**符合条件的订阅许可证：**

-   Microsoft 365 E3/E5 
-   Microsoft 365 F3 
-   Microsoft 365 A3/A5 
-   Windows 10 企业版 E3/E5
-   Windows 10 教育版 A3/A5 
-   Windows VDA E3/E5


## <a name="deploying-windows-10-image-from-azure-marketplace"></a>通过 Azure 市场部署 Windows 10 映像 
对于 PowerShell、CLI 和 Azure 资源管理器模板部署，可使用 `PublisherName: MicrosoftWindowsDesktop` 和 `Offer: Windows-10` 找到 Windows 10 映像。 多租户托管权限支持 Windows 10 版本创意者更新 (1809) 或更高版本。 

```powershell
Get-AzVmImageSku -Location '$location' -PublisherName 'MicrosoftWindowsDesktop' -Offer 'Windows-10'

Skus                        Offer      PublisherName           Location 
----                        -----      -------------           -------- 
rs4-pro                     Windows-10 MicrosoftWindowsDesktop eastus   
rs4-pron                    Windows-10 MicrosoftWindowsDesktop eastus   
rs5-enterprise              Windows-10 MicrosoftWindowsDesktop eastus   
rs5-enterprisen             Windows-10 MicrosoftWindowsDesktop eastus   
rs5-pron                    Windows-10 MicrosoftWindowsDesktop eastus  
```

有关可用映像的详细信息，请参阅[通过 Azure PowerShell 查找并使用 Azure 市场 VM 映像](./cli-ps-findimage.md)

## <a name="uploading-windows-10-vhd-to-azure"></a>将 Windows 10 VHD 上传到 Azure
如果要上传通用化的 Windows 10 VHD，请注意，Windows 10 不会默认启用内置 Administrator 帐户。 若要启用内置 Administrator 帐户，请在自定义脚本扩展中包含以下命令。

```powershell
Net user <username> /active:yes
```

以下 PowerShell 代码片段用于将所有管理员（包括内置 Administrator）帐户标记为活动帐户。 如果内置 Administrator 用户名未知，此示例非常有用。
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
参考信息： 
* [如何将 VHD 上传到 Azure](upload-generalized-managed.md)
* [如何准备好要上传到 Azure 的 Windows VHD](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>使用多租户托管权限部署 Windows 10
确保[已安装并配置最新的 Azure PowerShell](/powershell/azure/)。 准备好 VHD 之后，即可使用 `Add-AzVhd` cmdlet 将 VHD 上传到 Azure 存储帐户，如下所示：

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**使用 Azure 资源管理器模板部署进行部署** 在资源管理器模板中，可为 `licenseType` 指定一个附加参数。 可以阅读有关[创作 Azure Resource Manager 模板](../../azure-resource-manager/templates/syntax.md)的详细信息。 将 VHD 上传到 Azure 之后，请编辑 Resource Manager 模板以将许可证类型包含为计算提供程序的一部分，然后照常部署模板：
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**通过 PowerShell 部署** 通过 PowerShell 部署 Windows Server VM 时，可使用 `-LicenseType` 的附加参数。 将 VHD 上传到 Azure 之后，可以使用 `New-AzVM` 创建 VM 并指定许可类型，如下所示：
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>验证 VM 是否正在利用许可权益
通过 PowerShell 或 Resource Manager 部署方法部署 VM 之后，请使用 `Get-AzVM` 验证许可证类型，如下所示：
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

输出类似于许可证类型正确的 Windows 10 的以下示例：

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

以下 VM 在部署时未提供 Azure 混合使用权益许可（例如直接从 Azure 库部署 VM），可看出其中的输出差异：

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>有关联接 Azure AD 的其他信息
Azure 使用内置 Administrator 帐户预配所有 Windows，但不能使用此方法联接 AAD。 例如，“设置”>“帐户”>“访问工作或学校帐户”>“+连接”将不起作用。 若要手动加入 Azure AD，必须创建另一个管理员帐户并以其身份登录。 还可以使用预配包配置 Azure AD，使用“后续步骤”部分的链接了解详细信息。

## <a name="next-steps"></a>后续步骤
- 深入了解[为 Windows 10 配置 VDA](/windows/deployment/vda-subscription-activation)
- 深入了解 [Windows 10 的多租户托管](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)
