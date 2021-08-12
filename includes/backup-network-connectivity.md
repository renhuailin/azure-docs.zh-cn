---
title: include 文件
description: include 文件
services: backup
author: v-amallick
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 06/04/2021
ms.author: v-amallick
ms.custom: include file
ms.openlocfilehash: 36a45be18e5614371e3e29dc2907f5c25ba772b9
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111952984"
---
MARS 代理需要访问 Azure Active Directory、Azure 存储 和 Azure 备份服务终结点。 若要获取公共 IP 范围，请参阅 [JSON 文件](https://www.microsoft.com/en-us/download/confirmation.aspx?id=56519&preserveview=true)。 允许访问与 Azure 备份 (AzureBackup)、Azure 存储 (Storage) 和 Azure Active Directory (AzureActiveDirectory) 相对应的 IP。 此外，根据不同的 Windows 版本，操作系统的网络连接性检查将需要访问 `www.msftconnecttest.com` 或 `www.msftncsi.com`。

如果你的计算机的 Internet 访问存在一定限制，请确保防火墙、代理和网络设置允许访问以下 FQDN 和公共 IP 地址。

### <a name="url-and-ip-access"></a>URL 和 IP 访问

**FQDN**

- `www.msftncsi.com`
- `www.msftconnecttest.com`
- *.microsoft.com
- *.windowsazure.com
- *.microsoftonline.com
- *.windows.net

**IP 地址**

- 20.190.128.0/18
- 40.126.0.0/18

如果你是美国政府客户，请确保你有权访问以下 URL：

- `www.msftncsi.com`
- *.microsoft.com
- *.windowsazure.us
- *.microsoftonline.us
- *.windows.net
- *.usgovcloudapi.net

对上面列出的所有 URL 和 IP 地址的访问都使用端口 443 上的 HTTPS 协议。

使用 MARS 代理从 Azure VM 备份文件和文件夹时，还需要将 Azure 虚拟网络配置为允许访问。 如果使用网络安全组 (NSG)，请使用 AzureBackup 服务标记以允许对 Azure 备份进行出站访问。 除了 Azure 备份标记外，还需要通过为 Azure AD (AzureActiveDirectory) 和 Azure 存储 (Storage) 创建类似的 [NSG 规则](../articles/virtual-network/network-security-groups-overview.md#service-tags)，允许在连接后进行身份验证和数据传输。

若要为 Azure 备份标记创建规则，请执行以下步骤：

1. 在“所有服务”中转到“网络安全组”，然后选择“网络安全组”。
1. 在“设置”下选择“出站安全规则”。
1. 选择 **添加** 。
1. 按[安全规则设置](../articles/virtual-network/manage-network-security-group.md#security-rule-settings)中所述，提供创建新规则所需的所有详细信息。<br>确保选项设置如下：
   - 将“目标”设置为“服务标记”。
   - 将“目标服务标记”设置为“AzureBackup”。
1. 选择“添加”，保存新创建的出站安全规则。

同样，可以为 Azure 存储和 Azure AD 创建 NSG 出站安全规则。 若要详细了解服务标记，请参阅[虚拟网络服务标记](../articles/virtual-network/service-tags-overview.md)。

### <a name="azure-expressroute-support"></a>Azure ExpressRoute 支持

可以使用公共对等互连（适用于旧线路）通过 Azure ExpressRoute 备份数据。 不支持通过专用对等互连进行 Microsoft 对等互连备份。

若要使用公共对等互连，请确保以下域和地址在端口 443 上具有 HTTPS 访问权限：

- `www.msftncsi.com`
- `www.msftconnecttest.com`
- *.microsoft.com
- *.windowsazure.com
- *.microsoftonline.com
- *.windows.net

**IP 地址**
- 20.190.128.0/18
- 40.126.0.0/18

若要使用 Microsoft 对等互连，请选择以下服务、区域和相关社区值：
- Azure Active Directory (12076:5060)
- Azure 区域（取决于你的恢复服务保管库的位置）
- Azure 存储（取决于你的恢复服务保管库的位置）

详细了解 [ExpressRoute 路由要求](../articles/expressroute/expressroute-routing.md#bgp)。

>[!NOTE]
>对于新线路，公共对等互连已弃用。


### <a name="private-endpoint-support"></a>专用终结点支持

现在可以使用专用终结点将数据从服务器安全地备份到恢复服务保管库。 由于 Azure AD 不能通过专用终结点进行访问，因此需要单独允许 Azure AD 所需的 IP 和 FQDN 进行出站访问。

使用 MARS 代理备份本地资源时，请确保已将本地网络（包含要备份的资源）与包含保管库的专用终结点的 Azure VNet 对等互连。 然后可以继续安装 MARS 代理并配置备份。 但必须确保仅通过对等互连网络进行所有备份通信。

如果在某个 MARS 代理注册到保管库后删除了该保管库的专用终结点，则需要向该保管库重新注册容器。 不需要停止对它们的保护。
了解有关 [Azure 备份的专用终结点](../articles/backup/private-endpoints.md)的详细信息。

### <a name="throttling-support"></a>限制支持

**功能** | **详细信息**
--- | ---
带宽控制 | 。 在 MARS 代理中，使用“更改属性”来调整带宽。
网络限制 | 不适用于运行 Windows Server 2008 R2、Windows Server 2008 SP2 或 Windows 7 的备份计算机。
