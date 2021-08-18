---
title: 使用 Azure 应用程序网关重写 URL 和查询字符串 - Azure 门户
description: 了解如何使用 Azure 门户来配置 Azure 应用程序网关以重写 URL 和查询字符串
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: how-to
ms.date: 4/05/2021
ms.author: azhussai
ms.openlocfilehash: c8bcaa692fe33229ef7d71f717879f39ffa88279
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742719"
---
# <a name="rewrite-url-with-azure-application-gateway---azure-portal"></a>使用 Azure 应用程序网关重写 URL - Azure 门户

本文介绍如何使用 Azure 门户来配置[应用程序网关 v2 SKU](application-gateway-autoscaling-zone-redundant.md) 实例以重写 URL。

>[!NOTE]
> URL 重写功能仅适用于 Standard_v2 和 WAF_v2 SKU 版应用程序网关。 在启用 WAF 的网关上配置 URL 重写时，将在重写的请求标头和 URL 上进行 WAF 评估。 [了解详细信息](rewrite-http-headers-url.md#using-url-rewrite-or-host-header-rewrite-with-web-application-firewall-waf_v2-sku)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="before-you-begin"></a>准备阶段

若要完成本文中的步骤，需要有应用程序网关 v2 SKU 实例。 v1 SKU 不支持重写 URL。 如果没有 v2 SKU，请在开始之前创建[应用程序网关 v2 SKU](tutorial-autoscale-ps.md) 实例。

## <a name="sign-in-to-azure"></a>登录 Azure

使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="configure-url-rewrite"></a>配置 URL 重写

在以下示例中，每当请求 URL 包含 /article 时，URL 路径和 URL 查询字符串就会重写

`contoso.com/article/123/fabrikam` -> `contoso.com/article.aspx?id=123&title=fabrikam`

1. 选择“所有资源”，然后选择你的应用程序网关。

2. 在左窗格中，选择“重写”。 

3. 选择“重写集”：

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-1.png" alt-text="添加重写集":::

4. 为重写集提供一个名称，并将其与路由规则相关联：

    a. 在“名称”框中输入重写集的名称。
    
    b. 选择在“关联的路由规则”列表中列出的一个或多个规则。 这用于通过传递规则将重写配置关联到源侦听器。 只能选择尚未与其他重写集关联的传递规则。 已经与其他重写集关联的规则处于灰显状态。
    
    c. 选择“下一步”。
    
    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-2.png" alt-text="关联到规则":::

5. 创建重写规则：

    a. 选择“添加重写规则”。
    
    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-3.png" alt-text="突出显示“添加重写规则”的屏幕截图。":::
    
    b. 在“重写规则名称”框中输入重写规则的名称。 在“规则顺序”框中输入一个数字。

6. 在此示例中，仅当路径包含 /article 时，我们才会重写 URL 路径和 URL 查询字符串。 为此，请添加条件用于评估 URL 路径是否包含 /article

    a. 选择“添加条件”，然后选择包含 **If** 指令的框将其展开。
    
    b. 由于我们在此示例中要检查 URL 路径中的模式 /article，请在“要检查的变量的类型”列表中，选择“服务器变量”。
    
    c. 在“服务器变量”列表中，选择“uri_path”
    
    d. 在“区分大小写”下选择“否”。
    
    e. 在“运算符”列表中选择“等于(=)”。
    
    f. 输入正则表达式模式。 在此示例中，我们将使用 `.*article/(.*)/(.*)` 模式
    
      ( ) 用于捕获 substring，以供稍后在编写用于重写 URL 路径的表达式时使用。 有关详细信息，请参阅[此文](rewrite-http-headers-url.md#capturing)。

    g. 选择“确定”。

    Condition

 

7. 添加操作以重写 URL 和 URL 路径

   a. 在“重写类型”列表中，选择“URL”。

   b. 在“操作类型”列表中，选择“设置”。

   c. 在“组件”下，选择“URL 路径和 URL 查询字符串”

   d. 在“URL 路径值”中，输入路径的新值。 在此示例中，我们将使用“/article.aspx” 

   e. 在“URL 查询字符串值”中，输入 URL 查询字符串的新值。 在此示例中，我们将使用“id={var_uri_path_1}&title={var_uri_path_2}”
    
    `{var_uri_path_1}` 和 `{var_uri_path_2}` 用于在计算表达式 `.*article/(.*)/(.*)` 中的条件时获取捕获的子字符串
    
   f. 选择“确定”。

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-5.png" alt-text="操作":::

8. 单击“创建”以创建重写集。

9. 验证新的重写集是否在重写集列表中显示

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-6.png" alt-text="添加重写规则":::

## <a name="verify-url-rewrite-through-access-logs"></a>通过访问日志验证 URL 重写

观察访问日志中的以下字段，以验证是否按照预期进行 URL 重写。

* **originalRequestUriWithArgs**：此字段包含原始请求 URL
* **requestUri**：在应用程序网关上执行重写操作后，此字段将包含 URL

有关访问日志中的所有字段的详细信息，请参阅[此处](application-gateway-diagnostics.md#for-application-gateway-and-waf-v2-sku)。

##  <a name="next-steps"></a>后续步骤

有关如何设置某些常见用例的重写的详细信息，请参阅[常见重写方案](./rewrite-http-headers-url.md)。
