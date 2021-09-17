---
title: 创建和使用自定义域
description: 将自定义域连接到 Azure 中的虚拟机。
author: mimckitt
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/01/2021
ms.author: jamesser
ms.reviewer: cynthn
ms.openlocfilehash: 5bd9adb8354c3919a63e3de2e850513641af12a3
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696546"
---
# <a name="add-custom-domain-to-azure-vm-or-resource"></a>将自定义域添加到 Azure VM 或资源

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集


在 Azure 中，可以通过多种方式将自定义域连接到 VM 或资源。 对于具有公共 IP（虚拟机、负载均衡器、应用程序网关）的所有资源，最直接的方法是在相应的域注册器中创建 A 记录集。 

## <a name="prerequisites"></a>先决条件 
- 需要一个运行 Web 服务器的 VM。 可以使用[快速入门](./linux/quick-create-cli.md)创建 VM 并添加 NGINX。

- VM 必须可由 Web 访问（打开端口 80 或 443）。 对于更安全的部署，首先将 VM 放在负载均衡器或应用程序网关后面。 有关详细信息，请参阅 [快速入门：负载均衡器](../load-balancer/quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard)。

- 具有现有域和对 DNS 设置的访问权限。 有关详细信息，请参阅[购买 Azure 应用服务的自定义域](../app-service/manage-custom-dns-buy-domain.md)。


## <a name="add-custom-domain-to-vm-public-ip-address"></a>将自定义域添加到 VM 公共 IP

在 Azure 门户中创建虚拟机时，此门户会自动为虚拟机创建公共 IP 资源。 你的公共 IP 将显示在 VM 概述页中。 
 
:::image type="content" source="media/custom-domain/essentials.png" alt-text="显示 VM 概述页的概要部分中的公共 IP 地址。":::

如果选择 IP 地址，则可以查看其详细信息。 检查以确保“IP 分配”设置为“静态” 。 如果 VM 或资源重启或关闭，静态 IP 地址将不会更改。

:::image type="content" source="media/custom-domain/ip-config.png" alt-text="显示公共 IP 配置，以便可以查看 IP 地址是否为静态。":::

如果你的 IP 地址不是静态，你将需要创建一个 FQDN。 

1. 在门户中选择 VM。 
1. 在左侧菜单中，选择“属性”
1. 在“公共 IP 地址\DNS 名称标签”下，选择你的 IP 地址。
2. 在“DNS 名称标签”下，输入要使用的前缀。
3. 在页面顶部选择“保存”。
4. 在左侧菜单中选择“概述”，以返回到 VM 概述边栏选项卡。
5. 验证是否正确显示“DNS 名称”。 

打开浏览器并输入你的 IP 地址或 FQDN，验证它是否显示 VM 上运行的 Web 内容。
 
验证静态 IP 或 FQDN 后，请转到域提供程序并导航到 DNS 设置。

添加指向公共 IP 或 FQDN 的 A 记录后。 例如，GoDaddy 域注册机构的过程如下：
1. 登录后选择要使用的自定义域。
2. 在“域”部分选择“管理所有项”，然后选择“DNS | 管理区域”  。
3. 对于“域名”，  请输入自定义域，然后选择“搜索”。 
4. 在“DNS 管理”页中选择“添加”，然后在“类型”列表中选择“A”。
5. 完成 A 条目的字段：
    - 类型：让“A”保留选中状态。
    - 主机：输入 **@**
    - 指向：输入 VM 的公共 IP 或 FQDN。 
    - TTL：让“一小时”保留选中状态。
6. 选择“保存”。

此时会将 A 记录条目添加到 DNS 记录表。
 
创建记录后，通常需要大约一个小时来进行 DNS 传播，但有时可能需要长达 48 小时。 


 
## <a name="next-steps"></a>后续步骤
[应用程序网关的 TLS 终止和端到端 TLS 概述](../application-gateway/ssl-overview.md)。

