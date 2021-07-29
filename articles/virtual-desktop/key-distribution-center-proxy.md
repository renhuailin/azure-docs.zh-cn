---
title: 设置 Kerberos 密钥发行中心代理 Azure 虚拟桌面 - Azure
description: 如何将 Azure 虚拟桌面主机池设置为使用 Kerberos 密钥发行中心代理。
author: Heidilohr
ms.topic: how-to
ms.date: 05/04/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 55c68902cebbb6832a9c09c5390d9f43d381bf21
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111757546"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy"></a>配置 Kerberos 密钥发行中心代理

金融或政府组织等具有安全意识的客户通常使用智能卡登录。 智能卡要求执行多重身份验证 (MFA)，因此可以提高部署的安全性。 但是，对于 Azure 虚拟桌面会话的 RDP 部分，智能卡需要使用直接连接或“视距传输”通过 Active Directory (AD) 域控制器执行 Kerberos 身份验证。 如果不使用此直接连接，用户无法通过远程连接自动登录到组织的网络。 Azure 虚拟桌面部署中的用户可以使用 KDC 代理服务来代理此身份验证流量并进行远程登录。 通过 KDC 代理，能够对 Azure 虚拟桌面会话的远程桌面协议进行身份验证，从而让用户能够安全登录。 这样就可以更轻松地实现在家办公，并能够更顺畅地运行某些灾难恢复方案。

但是，设置 KDC 代理通常需要在 Windows Server 2016 或更高版本中分配 Windows Server 网关角色。 如何使用远程桌面服务角色登录到 Azure 虚拟桌面？ 为回答这个问题，我们来快速了解一下它的组件。

需要对 Azure 虚拟桌面服务的两个组件执行身份验证：

- Azure 虚拟桌面客户端中为用户提供他们有权访问的可用桌面或应用程序列表的源。 此身份验证过程在 Azure Active Directory 中进行，也就是说，此组件不是本文的焦点。
- 由于用户选择其中一项可用资源生成的 RDP 会话。 对于远程用户，此组件使用 Kerberos 身份验证，并且需要使用 KDC 代理。

本文将介绍如何在 Azure 门户中配置 Azure 虚拟机客户端中的源。 若要了解如何配置 RD 网关角色，请参阅[部署 RD 网关角色](/windows-server/remote/remote-desktop-services/remote-desktop-gateway-role)。

## <a name="requirements"></a>要求

若要为 Azure 虚拟桌面会话主机配置 KDC 代理，需要满足以下要求：

- 具有对 Azure 门户和 Azure 管理员帐户的访问权限。
- 远程客户端计算机必须运行 Windows 10 或 Windows 7，并且安装了 [Windows 桌面客户端](/windows-server/remote/remote-desktop-services/clients/windowsdesktop)。 目前不支持 Web 客户端。
- 必须已在计算机上安装 KDC 代理。 若要了解如何完成此操作，请参阅[为 Azure 虚拟桌面设置 RD 网关角色](/windows-server/remote/remote-desktop-services/remote-desktop-gateway-role)。
- 计算机的操作系统必须是 Windows Server 2016 或更高版本。

确保满足这些要求后，即可开始设置。

## <a name="how-to-configure-the-kdc-proxy"></a>如何配置 KDC 代理

配置 KDC 代理：

1. 以管理员身份登录到 Azure 门户。

2. 请转到“Azure 虚拟桌面”页面。

3. 选择要为其启用 KDC 代理的主机池，然后选择“RDP 属性”。

4. 选择“高级”选项卡，然后输入以下格式的值（不含空格）：

    
    > kdcproxyname:s:\<fqdn\>
    

    > [!div class="mx-imgBorder"]
    > ![此屏幕截图显示选择了“高级”选项卡，并按步骤 4 所述输入了值。](media/advanced-tab-selected.png)

5. 选择“保存”。 

6. 所选主机池现在应开始发出 RDP 连接文件，其中包含在步骤 4 中输入的 kdcproxyname 值。

## <a name="next-steps"></a>后续步骤

若要了解如何管理 KDC 代理的远程桌面服务端并分配 RD 网关角色，请参阅[部署 RD 网关角色](/windows-server/remote/remote-desktop-services/remote-desktop-gateway-role)。

如果想要缩放 KDC 代理服务器，请参阅[向 RD Web 和网关 Web 前端添加高可用性](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha)，了解如何为 KDC 代理设置高可用性。