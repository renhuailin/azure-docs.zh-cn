---
title: 在云服务（外延支持）中应用远程桌面
description: 为云服务（外延支持）启用远程桌面
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 02e5a30da1efe8c3cd669babddff305296077f20
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123424179"
---
# <a name="apply-the-remote-desktop-extension-to-azure-cloud-services-extended-support"></a>向 Azure 云服务（外延支持）应用远程桌面扩展

Azure 门户使用远程桌面扩展，即使在部署应用程序后，也能启用远程桌面。 通过云服务的“远程桌面”设置，可启用远程桌面、更新本地管理员帐户、选择身份验证中使用的证书，还可为这些证书设置到期日期。 

## <a name="apply-remote-desktop--extension"></a>应用远程桌面扩展
1. 导航到要为其启用远程桌面的云服务，然后在左侧导航窗格中选择“远程桌面”。

    :::image type="content" source="media/remote-desktop-1.png" alt-text="此图显示了在 Azure 门户中选中“远程桌面”选项":::

2. 选择 **添加** 。
3. 选择要为其启用远程桌面的角色。
4. 填写用户名、密码和到期时间必填字段以及证书字段（非必填）。
> [注意] 远程桌面的密码必须介于 8-123 个字符之间，并且必须至少满足下列密码复杂性要求中的其中 3 项：1) 包含大写字符 2) 包含小写字符 3) 包含数字 4) 包含特殊字符 5) 不允许使用控制字符

   :::image type="content" source="media/remote-desktop-2.png" alt-text="此图显示了输入连接到远程桌面所需的信息。":::

5. 完成后，选择“保存”。 可能需要一些时间角色实例才能完成接收连接的准备。

## <a name="connect-to-role-instances-with-remote-desktop-enabled"></a>连接到启用了远程桌面的角色实例
为角色启用远程桌面后，可直接从 Azure 门户启动连接。

1. 单击“角色和实例”，打开实例设置。

    :::image type="content" source="media/remote-desktop-3.png" alt-text="此图显示了在配置边栏选项卡中选择“角色和实例”选项。":::

2. 选择一个配置了远程桌面的角色实例。
3. 单击“连接”，下载远程桌面连接文件。

    :::image type="content" source="media/remote-desktop-4.png" alt-text="此图显示了在 Azure 门户中选择辅助角色实例。":::
    
4. 打开文件来连接到角色实例。

## <a name="update-remote-desktop-extension-using-powershell"></a>使用 PowerShell 更新远程桌面扩展
请按照以下步骤使用 RDP 扩展将云服务更新到最新模块

1.  将 Az.CloudService 模块更新到[最新版本](https://www.powershellgallery.com/packages/Az.CloudService/0.5.0)

```powershell
Update-Module -Name Az.CloudService 
```
 
2.  删除云服务的现有 RDP 扩展 

```powershell
$resourceGroupName='<Resource Group Name>'  
$cloudServiceName='<Cloud Service Name>' 
 
# Get existing cloud service  
$cloudService = Get-AzCloudService -ResourceGroup $resourceGroupName -CloudServiceName $cloudServiceName  
 
# Remove existing RDP Extension from cloud service object  
$cloudService.ExtensionProfile.Extension = $cloudService.ExtensionProfile.Extension | Where-Object { $_.Type-ne "RDP" }  
 ```
 
3.  使用最新模块将新的 RDP 扩展添加到云服务

```powershell
# Create new RDP extension object  
$credential = Get-Credential  
$expiration='<Expiration Date>'  
$rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name "RDPExtension" -Credential $credential -Expiration $expiration -TypeHandlerVersion "1.2.1"  
 
# Add RDP extension to existing cloud service extension object  
$cloudService.ExtensionProfile.Extension = $cloudService.ExtensionProfile.Extension + $rdpExtension  
 
# Update cloud service  
$cloudService | Update-AzCloudService  
```

## <a name="next-steps"></a>后续步骤 
- 查看云服务（外延支持）的[部署先决条件](deploy-prerequisite.md)。
- 请参阅云服务（外延支持）的[常见问题解答](faq.yml)。
- 使用 [Azure 门户](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[模板](deploy-template.md)或 [Visual Studio](deploy-visual-studio.md) 部署云服务（外延支持）。
