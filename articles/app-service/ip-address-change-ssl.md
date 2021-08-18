---
title: 为 TLS/SSL IP 地址更改做准备
description: 如果 TLS/SSL IP 地址将要更改，请了解如何在更改后继续运行应用。
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 3712931f73463ec1a799f003b82197752a735136
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112895276"
---
# <a name="how-to-prepare-for-a-tlsssl-ip-address-change"></a>如何为 TLS/SSL IP 地址更改做好准备

如果收到有关 Azure 应用服务应用的 TLS/SSL IP 地址即将更改的通知，请遵照本文中的说明释放现有的 TLS/SSL IP 地址并分配新地址。

## <a name="release-tlsssl-ip-addresses-and-assign-new-ones"></a>释放 TLS/SSL IP 地址并分配新地址

1.  打开 [Azure 门户](https://portal.azure.com)。

2.  在左侧导航菜单中选择“应用服务”。

3.  从列表中选择自己的应用服务应用。

4.  在左侧导航窗格中，单击“设置”标头下的“SSL 设置”。 

1. 在“TLS/SSL 绑定”部分，选择主机名记录。 在打开的编辑器中，从“SSL 类型”下拉菜单中选择“SNI SSL”，然后单击“添加绑定”。   如果出现操作成功的消息，则表示已释放现有的 IP 地址。

6.  在“SSL 绑定”部分，再次选择包含证书的同一主机名记录。 在打开的编辑器中，这次请从“SSL 类型”下拉菜单中选择“基于 IP 的 SSL”，然后单击“添加绑定”。   如果出现操作成功的消息，表示已获取新的 IP 地址。

7.  如果在域注册门户（第三方 DNS 提供程序或 Azure DNS）中配置了 A 记录（直接指向你的 IP 地址的 DNS 记录），请将现有 IP 地址替换为新生成的 IP 地址。 遵照下一部分中的说明可以找到新 IP 地址。

## <a name="find-the-new-ssl-ip-address-in-the-azure-portal"></a>在 Azure 门户中找到新的 SSL IP 地址

1.  等待几分钟时间，然后打开 [Azure 门户](https://portal.azure.com)。

2.  在左侧导航菜单中选择“应用服务”。

3.  从列表中选择自己的应用服务应用。

4.  在“设置”标题下，单击左侧导航栏中的“属性”，找到标有“虚拟 IP 地址”的部分。

5. 复制 IP 地址并重新配置域记录或 IP 机制。

## <a name="next-steps"></a>后续步骤

本文介绍了如何对 Azure 发起的 IP 地址更改做好准备。 有关 Azure 应用服务中的 IP 地址的详细信息，请参阅 [Azure 应用服务中的入站和出站 IP 地址](overview-inbound-outbound-ips.md)。
