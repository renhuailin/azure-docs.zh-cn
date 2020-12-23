---
title: VPN 网关：用于 P2S VPN 连接的 Azure AD 租户：Azure AD 身份验证
description: 了解如何设置用于 P2S Open VPN 身份验证的 Azure AD 租户。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/27/2020
ms.author: cherylmc
ms.openlocfilehash: 3055c9dd1294af81c6c52603dd60bb5aa6075abd
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2020
ms.locfileid: "92777864"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>为 P2S OpenVPN 协议连接创建 Azure Active Directory 租户

连接到 VNet 时，可以使用基于证书的身份验证或 RADIUS 身份验证。 但是，在使用开放 VPN 协议时，还可以使用 Azure Active Directory 身份验证。 本文帮助设置用于 P2S Open VPN 身份验证的 Azure AD 租户。

[!INCLUDE [Windows 10 and OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1.验证 Azure AD 租户

验证你是否有 Azure AD 租户。 如果没有 Azure AD 租户，可以按照[创建新租户](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)一文中的步骤创建一个：

* 组织名称
* 初始域名

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/newtenant.png" alt-text="新 Azure AD 租户" border="false":::

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2.创建 Azure AD 租户用户

Azure AD 租户需要以下帐户：全局管理员帐户和主用户帐户。 主要用户帐户用作主要嵌入帐户（服务帐户）。 创建 Azure AD 租户用户帐户时，可以根据要创建的用户类型调整目录角色。

使用 " [添加或删除用户-Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md) 中的步骤为你的 Azure AD 租户至少创建两个用户。 若要创建帐户类型，请务必更改“目录角色”：

* 全局管理员
* User

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3.在 VPN 网关上启用 Azure AD 身份验证

1. 找到要用于身份验证的目录的目录 ID。 它在 "Active Directory" 页的 "属性" 部分中列出。

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/directory-id.png" alt-text="显示目录属性的屏幕截图" lightbox="./media/openvpn-create-azure-ad-tenant/directory-id.png":::

1. 复制“目录 ID”。

1. 以拥有“全局管理员”角色的用户身份登录到 Azure 门户。

1. 接下来，做出管理员许可。 在浏览器的地址栏中复制并粘贴与部署位置相关的 URL：

   公共

   ```
   https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
   ````

   Azure Government

   ```
   https://login.microsoftonline.us/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
   ````

   德国 Microsoft 云

   ```
   https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
   ````

    Azure 中国世纪互联

    ```
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

   > [!NOTE]
   > 如果你使用的全局管理员帐户不是 Azure AD 租户的本机用户提供许可，请在 URL 中将 "common" 替换为 Azure AD 的目录 id。 还可能需要在某些其他情况下将 "common" 替换为目录 id。
   >

1. 如果出现提示，请选择“全局管理员”帐户。

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/pick.png" alt-text="选取帐户" border="false":::
1. 出现提示时选择“接受”。

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/accept.jpg" alt-text="屏幕截图显示请求你的组织接受的消息权限，其中包含详细信息和接受选项。" border="false":::
1. 在 Azure AD 下的“企业应用程序”中，将会发现已列出“Azure VPN”。

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/azurevpn.png" alt-text="显示 &quot;所有应用程序&quot; 页的屏幕截图。" lightbox="./media/openvpn-create-azure-ad-tenant/azurevpn.png" :::
1. 如果还没有正常运行的“点到站点”环境，请按照说明创建一个。 请参阅[创建点到站点 VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md)，以创建和配置点到站点 VPN 网关。

    > [!IMPORTANT]
    > OpenVPN 不支持基本 SKU。

1. 通过导航到“点到站点配置”并选取“OpenVPN (SSL)”作为“隧道类型”，在 VPN 网关上启用 Azure AD 身份验证。 选择 " **Azure Active Directory** " 作为 " **身份验证类型** "，然后填写 **Azure Active Directory** 部分下的信息。

   * **租户：** Azure AD 租户的 TenantID ```https://login.microsoftonline.com/{AzureAD TenantID}/```

   * **受众：** "Azure VPN" Azure AD 企业应用的 ApplicationID ```{AppID of the "Azure VPN" AD Enterprise app}```

   * **颁发者** ：安全令牌服务的 URL ```https://sts.windows.net/{AzureAD TenantID}/```


   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/azure-ad-auth-portal.png" alt-text="SAzure VPN" border="false":::

   > [!NOTE]
   > 请确保在 `AadIssuerUri` 值的末尾包含尾随斜杠。 否则，连接可能会失败。
   >

1. 通过单击“下载 VPN 客户端”链接来创建和下载配置文件。

1. 解压缩已下载的 zip 文件。

1. 浏览到解压缩后的“AzureVPN”文件夹。

1. 记下“azurevpnconfig.xml”文件的位置。 azurevpnconfig.xml 包含 VPN 连接的设置，可以直接导入到 Azure VPN 客户端应用程序中。 还可以将此文件分发给需要通过电子邮件或其他方式建立连接的所有用户。 用户需有有效的 Azure AD 凭据才能成功建立连接。

## <a name="next-steps"></a>后续步骤

对于虚拟网络，必须创建并配置 VPN 客户端配置文件。 请参阅[配置 VPN 客户端以建立 P2S VPN 连接](openvpn-azure-ad-client.md)。
