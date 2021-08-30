---
title: 配置用于 P2S OpenVPN 协议连接的 VPN 客户端：Azure AD 身份验证：macOS
description: 了解如何配置 macOS VPN 客户端，以使用 VPN 网关点到站点连接和 Azure Active Directory 身份验证连接到虚拟网络。
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 08/04/2021
ms.author: cherylmc
ms.openlocfilehash: 25049323d8dc9259c2f6edc439ecb5b624490009
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729651"
---
# <a name="azure-active-directory-authentication-configure-a-vpn-client-for-p2s-openvpn-protocol-connections---macos"></a>Azure Active Directory 身份验证：配置用于 P2S OpenVPN 协议连接的 VPN 客户端 - macOS

本文介绍如何为运行 macOS 10.15 及更高版本的计算机配置 VPN 客户端，以使用点到站点 VPN 和 Azure Active Directory 身份验证连接到虚拟网络。 在使用 Azure AD 进行连接和身份验证之前，必须先配置 Azure AD 租户。 有关详细信息，请参阅[配置 Azure AD 租户](openvpn-azure-ad-tenant.md)。 有关点到站点连接的详细信息，请参阅[关于点到站点连接](point-to-site-about.md)。

> [!NOTE]
> Azure AD 身份验证仅支持 OpenVPN® 协议连接，并且需要 Azure VPN 客户端。
>

对于你想要使用点到站点 VPN 连接将其连接到 VNet 的每台计算机，需要执行以下操作：
 
* 将 Azure VPN 客户端下载到计算机。
* 配置包含 VPN 设置的客户端配置文件。 

如果希望配置多台计算机，则可以在一台计算机上创建客户端配置文件，将其导出后再导入到其他计算机。

## <a name="prerequisites"></a>先决条件

在使用 Azure AD 进行连接和身份验证之前，必须先配置 Azure AD 租户。 有关详细信息，请参阅[配置 Azure AD 租户](openvpn-azure-ad-tenant.md)。

## <a name="to-download-the-azure-vpn-client"></a><a name="download"></a>下载 Azure VPN 客户端

1. 从 Apple Store 商店下载 [Azure VPN 客户端](https://apps.apple.com/us/app/azure-vpn-client/id1553936137)。
1. 在计算机上安装客户端。

## <a name="to-import-a-connection-profile"></a><a name="import"></a>导入连接配置文件

1. 下载并提取配置文件。 有关步骤，请参阅[使用 VPN 客户端配置文件](about-vpn-profile-download.md)。
1. 在“Azure VPN 客户端”页上，选择“导入”。

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-1.png" alt-text="Azure VPN 客户端导入选择的屏幕截图。":::
1. 导航到要导入的配置文件，将其选中，然后单击“打开”。

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-2.png" alt-text="Azure VPN 客户端导入操作的屏幕截图，显示了单击“打开”后的情况。":::
1. 查看连接配置文件信息，然后单击“保存”。

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-3.png" alt-text="将要保存导入的配置文件设置的 Azure VPN 客户端的屏幕截图。":::
1. 在“VPN 连接”窗格中，选择保存的连接配置文件。 然后单击“连接”。 

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-4.png" alt-text="Azure VPN 客户端的屏幕截图，显示你可以单击“连接”。":::
1. 连接后，状态会更改为“已连接”。 若要断开与会话的连接，请单击“断开连接”。

   :::image type="content" source="media/openvpn-azure-ad-client-mac/import-5.png" alt-text="Azure VPN 客户端的“已连接”状态和“断开连接”按钮的屏幕截图。":::

## <a name="to-create-a-connection-manually"></a><a name="manual"></a>手动创建连接

1. 打开 Azure VPN 客户端。 选择“添加”以创建新连接。

   :::image type="content" source="media/openvpn-azure-ad-client-mac/add-1.png" alt-text="选择“添加”后的 Azure VPN 客户端的屏幕截图。":::

1. 在“Azure VPN 客户端”页上，你可以对配置文件设置进行配置。

   :::image type="content" source="media/openvpn-azure-ad-client-mac/add-2.png" alt-text="Azure VPN 客户端配置文件设置的屏幕截图。":::

   配置下列设置：

   * 连接名称：用于引用此连接配置文件的名称。
   * VPN 服务器：此名称是要用于引用服务器的名称。 此处选择的名称无需是服务器的正式名称。
   * 服务器验证
     * 证书信息：证书 CA。
     * 服务器机密：服务器的机密。
   * **客户端身份验证**
     * 身份验证类型：Azure Active Directory
     * 租户：租户的名称。
     * 颁发者：颁发者的名称。
1. 填写这些字段后，请单击“保存”。
1. 在“VPN 连接”窗格中，选择已配置的连接配置文件。 然后单击“连接”。 

   :::image type="content" source="media/openvpn-azure-ad-client-mac/add-3.png" alt-text="要连接的 Azure VPN 客户端的屏幕截图。":::
1. 使用你的凭据登录以进行连接。

   :::image type="content" source="media/openvpn-azure-ad-client-mac/add-4.png" alt-text="Azure VPN 客户端通过登录进行连接的屏幕截图。":::
1. 连接后，你会看到状态为“已连接”。 要断开连接时，请单击“断开连接”以断开该连接。

   :::image type="content" source="media/openvpn-azure-ad-client-mac/add-5.png" alt-text="Azure VPN 客户端的“已连接”状态和“断开连接”按钮的屏幕截图。":::

## <a name="to-remove-a-connection-profile"></a><a name="remove"></a>删除连接配置文件

可以从计算机中删除 VPN 连接配置文件。 

1. 导航到 Azure VPN 客户端。
1. 选择要删除的 VPN 连接，单击下拉列表，然后选择“删除”。

   :::image type="content" source="media/openvpn-azure-ad-client-mac/remove-1.png" alt-text="删除操作的屏幕截图。":::
1. 在“删除 VPN 连接?”框中，单击“删除”。
   :::image type="content" source="media/openvpn-azure-ad-client-mac/remove-2.png" alt-text="删除操作的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[为使用 Azure AD 身份验证的 P2S 开放 VPN 连接创建 Azure Active Directory 租户](openvpn-azure-ad-tenant.md)。
