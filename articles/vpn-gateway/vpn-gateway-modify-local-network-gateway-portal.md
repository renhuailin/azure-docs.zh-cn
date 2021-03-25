---
title: VPN 网关：修改网关 IP 地址设置：Azure 门户
description: 本文介绍了如何使用 Azure 门户更改本地网络网关的 IP 地址前缀。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/16/2020
ms.author: cherylmc
ms.openlocfilehash: 4ff4f1238764d7bdab6e74d29254a6388ea76d78
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "92143149"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>使用 Azure 门户修改本地网络网关设置

有时本地网络网关 AddressPrefix 或 GatewayIPAddress 的设置会变更。 本文演示如何修改本地网络网关设置。 还可以选择以下列表中的其他选项，使用另一种方法来修改这些设置：

> [!div class="op_single_selector"]
> * [Azure 门户](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>

## <a name="local-network-gateway-configuration"></a><a name="configure-lng"></a>本地网关配置

下面的屏幕截图显示了使用公共 IP 地址终结点的本地网关资源的“配置”页：

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/settings.png" alt-text="IP 地址设置" lightbox="./media/vpn-gateway-modify-local-network-gateway-portal/settings-expand.png":::

这是使用 FQDN 终结点的配置页：

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/name.png" alt-text="FQDN 设置":::

## <a name="to-modify-the-gateway-ip-address-or-fqdn"></a><a name="ip"></a>修改网关 IP 地址或 FQDN

> [!NOTE]
> 不能更改 FQDN 终结点和 IP 地址终结点之间的本地网关。 必须删除与此本地网关关联的所有连接，使用新终结点（IP 地址或 FQDN）创建新网关，然后重新创建连接。
>

如果你要连接到的 VPN 设备已更改其公共 IP 地址，请使用以下步骤修改本地网关：

1. 在“本地网关”资源的“设置”部分中，选择“配置”。
2. 在“IP 地址”框中，修改 IP 地址。
3. 选择“保存”，保存这些设置。

如果你要连接到的 VPN 设备已更改其 FQDN（完全限定的域名），请使用以下步骤修改本地网关：

1. 在“本地网关”资源的“设置”部分中，选择“配置”。
2. 在“FQDN”框中，修改域名。
3. 选择“保存”，保存这些设置。

## <a name="to-modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>修改 IP 地址前缀

添加其他地址前缀：

1. 在“本地网关”资源的“设置”部分中，选择“配置”。
2. 在“添加更多地址范围”框中添加 IP 地址空间。
3. 选择“保存”以保存设置。

删除地址前缀：

1. 在“本地网关”资源的“设置”部分中，选择“配置”。
2. 在包含要删除的前缀的行上，选择“...”。
3. 选择“删除” 。
4. 选择“保存”以保存设置。

## <a name="to-modify-bgp-settings"></a><a name="bgp"></a>修改 BGP 设置的步骤

若要添加或更新 BGP 设置，请执行以下操作：

1. 在“本地网关”资源的“设置”部分中，选择“配置”。
2. 选择“配置 BGP 设置”以显示或更新此本地网关的 BGP 配置
3. 在相应的字段中添加或更新自治系统编号或 BGP 对等节点 IP 地址
4. 选择“保存”以保存设置。

若要删除 BGP 设置，请执行以下操作：

1. 在“本地网关”资源的“设置”部分中，选择“配置”。
2. 取消选择“配置 BGP 设置”，以删除现有 BGP ASN 和 BGP 对等节点 IP 地址
3. 选择“保存”以保存设置。

## <a name="next-steps"></a>后续步骤

可验证网关连接。 请参阅[验证网关连接](vpn-gateway-verify-connection-resource-manager.md)。
