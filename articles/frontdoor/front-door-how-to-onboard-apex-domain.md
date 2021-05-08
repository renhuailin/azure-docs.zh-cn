---
title: 将根域或顶点域载入现有 Front Door - Azure 门户
description: 了解如何使用 Azure 门户将根域或顶点域载入现有 Front Door。
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 11/13/2020
ms.author: duau
ms.openlocfilehash: 55eefe7a7490df050aa7ebc2bb41fbadcc8d8279
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "94646332"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>在 Front Door 上载入根或顶点域
Azure Front Door 使用 CNAME 记录来验证载入自定义域的域所有权。 Front Door 不会公开与 Front Door 配置文件关联的前端 IP 地址。 因此，如果你的目的是将顶点域载入到 Front Door，则不能将其映射到 IP 地址。

DNS 协议会阻止在区域顶点分配 CNAME 记录。 例如，如果你的域为 `contoso.com`，则可以为 `somelabel.contoso.com` 创建 CNAME 记录，但无法为 `contoso.com` 本身创建 CNAME。 对于在 Azure Front Door 后面具有负载均衡应用程序的应用程序所有者，此限制会带来问题。 由于使用 Front Door 配置文件需要创建 CNAME 记录，因此无法从区域顶点指向 Front Door 配置文件。

此问题可使用 Azure DNS 中的别名记录得以解决。 与 CNAME 记录不同，别名记录是在区域顶点创建的。 应用程序所有者可使用别名记录将其区域顶点记录指向具有公共终结点的 Front Door 配置文件。 应用程序所有者可以指向用于其 DNS 区域中任何其他域的相同 Front Door 配置文件。 例如，`contoso.com` 和 `www.contoso.com` 可以指向相同的 Front Door 配置文件。 

将顶点域或根域映射到 Front Door 配置文件主要需要 CNAME 平展或 DNS 跟踪。 这是一种机制，其中 DNS 提供程序在达到 IP 地址之前将以递归方式解析 CNAME 条目。 此功能由 Azure DNS 提供支持，用于 Front Door 终结点。 

> [!NOTE]
> 还有其他一些支持 CNAME 平展或 DNS 跟踪的 DNS 提供程序，但是 Azure Front Door 建议其客户使用 Azure DNS 来托管其域。

你可以使用 Azure 门户在 Front Door 上载入顶点域，并通过将其与 TLS 终止证书相关联，对其启用 HTTPS。 顶点域也称为根域或裸域。

## <a name="create-an-alias-record-for-zone-apex"></a>为区域顶点创建别名记录

1. 打开要载入的域的“Azure DNS”配置。

1. 创建或编辑区域顶点的记录。

1. 将记录类型选择为“A”记录，然后为“别名记录集”选择“是”。 “别名类型”应设置为“Azure 资源”。

1. 选择用于托管 Front Door 配置文件的 Azure 订阅。 然后从“Azure 资源”下拉列表中选择 Front Door 资源。

1. 选择“确定”提交更改。

    :::image type="content" source="./media/front-door-apex-domain/front-door-apex-alias-record.png" alt-text="区域顶点的别名记录":::

1. 以上步骤将创建一个指向 Front Door 资源的区域顶点记录，以及一个 CNAME 记录，后者将“afdverify”（例如 - `afdverify.contosonews.com`）映射到该记录上，用于在 Front Door 配置文件上载入该域。

## <a name="onboard-the-custom-domain-on-your-front-door"></a>在 Front Door 上载入自定义域

1. 在“Front Door 设计器”选项卡上，选择“前端主机”部分的“+”图标以添加新的自定义域。

1. 在“自定义主机名”字段中输入根域或顶点域的名称，例如 `contosonews.com`。

1. 验证了从域到 Front Door 的 CNAME 映射后，选择“添加”以添加自定义域。

1. 选择“保存”提交更改。

   :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-domain.png" alt-text="自定义域菜单":::

## <a name="enable-https-on-your-custom-domain"></a>在自定义域上启用 HTTPS

1. 选择已添加的自定义域，并在“自定义域 HTTPS”部分下，将状态更改为“已启用”。

1. 将“证书管理类型”选择为“使用我自己的证书”。

   :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-custom-domain.png" alt-text="自定义域 HTTPS 设置":::    

   > [!WARNING]
   > 顶点域或根域目前不支持 Front Door 托管证书管理类型。 用于在顶点域或根域上为 Front Door 启用 HTTPS 的唯一选项是使用托管在 Azure Key Vault 上的自定义 TLS/SSL 证书。

1. 继续下一步之前，确保已为 Front Door 设置了适当权限访问密钥保管库（如 UI 中所注）。

1. 从当前订阅中选择“密钥保管库帐户”，然后选择相应的“机密”和“机密版本”以映射到正确的证书。

1. 选择“更新”保存所选内容，然后选择“保存”。

1. 选择“刷新”几分钟后，然后再次选择自定义域，查看证书预配进度。 

> [!WARNING]
> 确保已为顶点域创建了合适的路由规则，或已将域添加到现有传递规则。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
