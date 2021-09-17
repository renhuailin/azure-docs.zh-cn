---
title: 教程：为 Azure CDN 创建 WAF 策略 - Azure 门户
description: 本教程介绍如何使用 Azure 门户在 Azure CDN 上创建 Web 应用程序防火墙 (WAF) 策略。
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 09/16/2020
ms.author: victorh
ms.openlocfilehash: a8710bac1d3161581f1002aa2d7531350c197be1
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696711"
---
# <a name="tutorial-create-a-waf-policy-on-azure-cdn-using-the-azure-portal"></a>教程：使用 Azure 门户在 Azure CDN 上创建 WAF 策略

本教程介绍如何创建基本的 Azure Web 应用程序防火墙 (WAF) 策略，并将其应用于 Azure 内容分发网络 (CDN) 上的终结点。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建 WAF 策略
> * 将其与 CDN 终结点关联。 只能将 WAF 策略与 Microsoft 的 Azure CDN 标准版  SKU 上托管的终结点相关联。
> * 配置 WAF 规则

## <a name="prerequisites"></a>先决条件

按照[快速入门：创建 Azure CDN 配置文件和终结点](../../cdn/cdn-create-new-endpoint.md)。 

## <a name="create-a-web-application-firewall-policy"></a>创建 Web 应用程序防火墙策略

首先使用门户创建包含托管的默认规则集 (DRS) 的基本 WAF 策略。

1. 在屏幕的左上方选择“创建资源”，搜索 **WAF**，然后选择“Web 应用程序防火墙”>“创建”。   
2. 在“创建 WAF 策略”页的“基本”选项卡中输入或选择以下信息，对剩余的设置保留默认值，然后选择“查看 + 创建”：   

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | 策略适用于            |选择“Azure CDN(预览版)”。|
    | 订阅            |选择 CDN 配置文件订阅名称。|
    | 资源组          |选择 CDN 配置文件资源组名称。|
    | 策略名称             |输入 WAF 策略的唯一名称。|

   :::image type="content" source="../media/waf-cdn-create-portal/basic.png" alt-text="“创建 WAF 策略”页的屏幕截图，其中包含“查看 + 创建”按钮以及为各种设置输入的值。" border="false":::

3. 在“创建 WAF 策略”页的“关联”选项卡中选择“添加 CDN 终结点”，输入以下设置，然后选择“添加”：    

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | CDN 配置文件              | 选择你的 CDN 配置文件名称。|
    | 端点           | 选择你的终结点的名称，然后选择“添加”。 |
    
    > [!NOTE]
    > 如果该终结点与某个 WAF 策略相关联，则它将灰显。必须先从关联的策略中删除该终结点，然后将它重新关联到新的 WAF 策略。
1. 依次选择“查看 + 创建”、“创建”。  

## <a name="configure-web-application-firewall-policy-optional"></a>配置 Web 应用程序防火墙策略（可选）

### <a name="change-mode"></a>更改模式

默认情况下，创建 WAF 策略时，该策略处于“检测”模式。  在“检测”  模式下，WAF 不会阻止任何请求。 相反，与 WAF 规则匹配的请求将记录在 WAF 日志中。

若要查看 WAF 的运作方式，可将模式设置从“检测”更改为“阻止”。   在“阻止”模式下，与默认规则集 (DRS) 中定义的规则匹配的请求将被阻止，并记录在 WAF 日志中。 

 :::image type="content" source="../media/waf-cdn-create-portal/policy.png" alt-text="“策略设置”部分的屏幕截图。“模式”切换开关设置为“阻止”。" border="false":::

### <a name="custom-rules"></a>自定义规则

若要创建自定义规则，请在“自定义规则”部分下选择“添加自定义规则”。   这将打开自定义规则配置页。 有两种类型的自定义规则：**匹配规则** 和 **速率限制** 规则。

下面的屏幕截图显示了一个自定义匹配规则，如果查询字符串包含值 **blockme**，则该规则将阻止请求。

:::image type="content" source="../media/waf-cdn-create-portal/custommatch.png" alt-text="“自定义规则配置”页的屏幕截图，其中显示了一个规则的设置，该规则可检查 QueryString 变量是否包含值 blockme。" border="false":::

速率限制规则需要另外两个字段：**速率限制持续时间** 和 **速率限制阈值(请求数)** ，如以下示例中所示：

:::image type="content" source="../media/waf-cdn-create-portal/customrate.png" alt-text="“速率限制规则配置”页的屏幕截图。其中显示了“速率限制持续时间”列表框和“速率限制阈值(请求数)”框。" border="false":::

### <a name="default-rule-set-drs"></a>默认规则集 (DRS)

默认情况下会启用 Azure 托管的默认规则集。 若要禁用规则组中的单个规则，请展开该规则组中的规则，选中规则编号前面的复选框，然后在上面的选项卡中选择“禁用”。  若要更改规则集中单个规则的操作类型，请选中规则编号前面的复选框，然后选择上面的“更改操作”选项卡。 

 :::image type="content" source="../media/waf-cdn-create-portal/managed2.png" alt-text="“托管规则”页的屏幕截图，其中显示了规则集、规则组、规则，以及“启用”、“禁用”和“更改操作”按钮。已选中一个规则。" border="false":::

## <a name="clean-up-resources"></a>清理资源

不再需要时，请删除资源组及所有相关资源。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 Azure Web 应用程序防火墙](../overview.md)
