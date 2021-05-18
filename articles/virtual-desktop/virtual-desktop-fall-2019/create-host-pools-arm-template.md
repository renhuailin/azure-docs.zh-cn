---
title: Windows 虚拟桌面（经典）主机池 Azure 资源管理器 - Azure
description: 如何使用 Azure 资源管理器模板在 Windows 虚拟桌面（经典）中创建主机池。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 601512d9a236c1e6f5b09e1a310610597df59c0f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445170"
---
# <a name="create-a-host-pool-in-windows-virtual-desktop-classic-with-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板在 Windows 虚拟桌面（经典）中创建主机池

>[!IMPORTANT]
>本教程的内容适用于 Windows 虚拟桌面（经典），后者不支持 Azure 资源管理器 Windows 虚拟桌面对象。

主机池是 Windows 虚拟桌面租户环境中一个或多个相同虚拟机的集合。 每个主机池可以包含一个应用组，用户可以像在物理桌面上一样与该应用组交互。

请按照此部分的说明使用 Microsoft 提供的 Azure 资源管理器模板来为 Windows 虚拟桌面租户创建主机池。 本文介绍如何在 Windows 虚拟桌面中创建主机池，如何在 Azure 订阅中创建包含 VM 的资源组，如何将这些 VM 加入 AD 域，以及如何将 VM 注册到 Windows 虚拟桌面。

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>运行 Azure 资源管理器模板所要做好的准备

在运行 Azure 资源管理器模板之前，请确保了解以下事项：

- 要使用的映像的源所在的位置。 该映像是来自 Azure 库，还是自定义项？
- 你的域加入凭据。
- 你的 Windows 虚拟桌面凭据。

使用 Azure 资源管理器模板创建 Windows 虚拟桌面主机池时，可以从 Azure 库、托管映像或非托管映像创建虚拟机。 若要详细了解如何创建 VM 映像，请参阅[准备要上传到 Azure 的 Windows VHD 或 VHDX](../../virtual-machines/windows/prepare-for-upload-vhd-image.md)，以及[为 Azure 中的通用化 VM 创建托管映像](../../virtual-machines/windows/capture-image-resource.md)。

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>运行 Azure 资源管理器模板来预配新主机池

若要开始，请转到[此 GitHub URL](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool)。

### <a name="deploy-the-template-to-azure"></a>将模板部署到 Azure

如果是在企业订阅中部署，请向下滚动并选择“部署到 Azure”，然后向前跳转，基于映像源来填充参数。

如果是在云解决方案提供商订阅中部署，请按以下步骤部署到 Azure：

1. 向下滚动并右键单击“部署到 Azure”，然后选择“复制链接位置”。
2. 打开一个文本编辑器（例如记事本）并在其中粘贴该链接。
3. 在紧接“https://portal.azure.com/”之后、井号标签 (#) 之前的位置，输入 @ 符号，后面接租户域名。 这是应该使用的格式的示例：`https://portal.azure.com/@Contoso.onmicrosoft.com#create/`。
4. 以对“云解决方案提供商”订阅拥有“管理员/参与者”权限的用户身份登录到 Azure 门户。
5. 将已复制到文本编辑器的链接粘贴到地址栏中。

有关应为方案输入哪些参数的指导，请参阅 Windows 虚拟桌面[自述文件](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md)。 此自述文件中始终会更新最新的更改。

## <a name="assign-users-to-the-desktop-application-group"></a>将用户分配到桌面应用程序组

在 GitHub Azure 资源管理器模板完成后，请先分配用户访问权限，然后再开始测试虚拟机上的完整会话桌面。

首先[下载并导入 Windows 虚拟桌面 PowerShell 模块](/powershell/windows-virtual-desktop/overview/)（如果尚未这样做），以便在 PowerShell 会话中使用。

若要将用户分配到桌面应用程序组，请打开 PowerShell 窗口并运行此 cmdlet 以登录到 Windows 虚拟桌面环境：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

然后，使用此 cmdlet 将用户添加到桌面应用程序组：

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

用户的 UPN 应与 Azure Active Directory 中该用户的标识（例如 user1@contoso.com）匹配。 若要添加多个用户，必须针对每个用户运行此 cmdlet。

完成这些步骤后，添加到桌面应用程序组的用户可以使用支持的远程桌面客户端登录到 Windows 虚拟桌面，并且可以看到会话桌面的资源。

>[!IMPORTANT]
>为了帮助保护 Azure 中的 Windows 虚拟桌面环境，我们建议你不要在 VM 上打开入站端口 3389。 Windows 虚拟机不需要打开入站端口 3389，用户就可以访问主机池的 VM。 如果必须打开端口 3389 以进行故障排除，我们建议你使用[实时 VM 访问](../../security-center/security-center-just-in-time.md)。