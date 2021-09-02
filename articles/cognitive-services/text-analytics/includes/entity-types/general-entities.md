---
title: 常规命名实体
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2021
ms.author: aahi
ms.openlocfilehash: e12a4226357160e2269034136a2df9c671b66313
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734078"
---
文本分析的 NER 特性返回以下常规（非标识）实体类别。 例如，向 `/entities/recognition/general` 终结点发送请求时。


| 类别 | 说明                          |
|------------|-------------|--------------------------------------|-------------------------------------------------------------|--------------------------------------|
| [Person](#category-person)     | 人员姓名。  |
| [PersonType](#category-persontype) | 某人的工作类型或角色。 |
| [位置](#category-location)    | 自然地标和人造地标、结构、地理特征和地缘政治实体 |
| 组织  | 公司、政治团体、乐队、体育俱乐部、政府机构和公共组织。  |
| [事件](#category-event)  | 历史事件、社会事件和自然发生的事件。 |
| [Product](#category-product) | 各种类别的物理对象。 |
| [Skill](#category-skill) | 能力、技能或专长。  |
| [Address](#category-address) | 完整的邮寄地址。  |
| [电话号码](#category-phonenumber) | 电话号码。 |
| [Email](#category-email) | 电子邮件地址。 |
| [URL](#category-url) | 指向网站的 URL。 |
| [IPAddress](#category-ipaddress) | 网络 IP 地址。 |
| [DateTime](#category-datetime) | 某天的日期和时间。 |
| [数量](#category-quantity) | 数值度量和单位。 |


### <a name="category-person"></a>类别：人员

此类别包含以下实体：

:::row:::
    :::column span="":::
        **实体**

        人员

    :::column-end:::
    :::column span="2":::
        **详细信息**

        人员姓名。
      
    :::column-end:::
    :::column span="2":::
      **支持的文档语言**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, <br> `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt`-`pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

### <a name="category-persontype"></a>类别：PersonType

此类别包含以下实体：


:::row:::
    :::column span="":::
        **实体**

        PersonType

    :::column-end:::
    :::column span="2":::
        **详细信息**

        某人的工作类型或角色
      
    :::column-end:::
    :::column span="2":::
      **支持的文档语言**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-location"></a>类别：位置

此类别包含以下实体：

:::row:::
    :::column span="":::
        **实体**

        位置

    :::column-end:::
    :::column span="2":::
        **详细信息**

        自然地标和人造地标、结构、地理特征和地缘政治实体。
      
    :::column-end:::
    :::column span="2":::
      **支持的文档语言**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>子类别

此类别中的实体可以有以下子类别。

:::row:::
    :::column span="":::
        **实体子类别**

        地缘政治实体 (GPE)

    :::column-end:::
    :::column span="2":::
        **详细信息**

        城市、国家/地区、省/自治区/直辖市。
      
    :::column-end:::
    :::column span="2":::
      **支持的文档语言**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Structural

    :::column-end:::
    :::column span="2":::

        人造结构。 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        地理

    :::column-end:::
    :::column span="2":::

        地理和自然特征，如河流、海洋和沙漠。
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-organization"></a>类别：组织

此类别包含以下实体：

:::row:::
    :::column span="":::
        **实体**

        组织

    :::column-end:::
    :::column span="2":::
        **详细信息**

        公司、政治团体、乐队、体育俱乐部、政府机构和公共组织。 民族和宗教不包括在此实体类型中。
      
    :::column-end:::
    :::column span="2":::
      **支持的文档语言**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>子类别

此类别中的实体可以有以下子类别。

:::row:::
    :::column span="":::
        **实体子类别**

        医疗

    :::column-end:::
    :::column span="2":::
        **详细信息**

        医疗公司和团体。
      
    :::column-end:::
    :::column span="2":::
      **支持的文档语言**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        证券交易

    :::column-end:::
    :::column span="2":::

        证券交易所集团。 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        体育游戏

    :::column-end:::
    :::column span="2":::

        与体育相关的组织。
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-event"></a>类别：事件

此类别包含以下实体：

:::row:::
    :::column span="":::
        **实体**

        事件

    :::column-end:::
    :::column span="2":::
        **详细信息**

        历史事件、社会事件和自然发生的事件。
      
    :::column-end:::
    :::column span="2":::
      **支持的文档语言**

      `en`、`es`、`fr`、`de`、`it`、`zh-hans`、`ja`、`ko`、`pt-pt` 和 `pt-br`  
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>子类别

此类别中的实体可以有以下子类别。

:::row:::
    :::column span="":::
        **实体子类别**

        文化

    :::column-end:::
    :::column span="2":::
        **详细信息**

        文化活动和假期。
      
    :::column-end:::
    :::column span="2":::
      **支持的文档语言**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Natural

    :::column-end:::
    :::column span="2":::

        自然发生的事件。
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        体育游戏

    :::column-end:::
    :::column span="2":::

        体育活动。
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-product"></a>类别：产品

此类别包含以下实体：

:::row:::
    :::column span="":::
        **实体**

        产品

    :::column-end:::
    :::column span="2":::
        **详细信息**

        各种类别的物理对象。
      
    :::column-end:::
    :::column span="2":::
      **支持的文档语言**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::


#### <a name="subcategories"></a>子类别

此类别中的实体可以有以下子类别。

:::row:::
    :::column span="":::
        **实体子类别**

        计算产品
    :::column-end:::
    :::column span="2":::
        **详细信息**

        计算产品。
      
    :::column-end:::
    :::column span="2":::
      **支持的文档语言**

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-skill"></a>类别：技能

此类别包含以下实体：

:::row:::
    :::column span="":::
        **实体**

        技能

    :::column-end:::
    :::column span="2":::
        **详细信息**

        能力、技能或专长。
      
    :::column-end:::
    :::column span="2":::
      **支持的文档语言**

      `en` , `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br` 
      
   :::column-end:::
:::row-end:::

### <a name="category-address"></a>类别：地址

此类别包含以下实体：

:::row:::
    :::column span="":::
        **实体**

        地址

    :::column-end:::
    :::column span="2":::
        **详细信息**

        完整邮寄地址。
      
    :::column-end:::
    :::column span="2":::
      **支持的文档语言**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-phonenumber"></a>类别：PhoneNumber

此类别包含以下实体：

:::row:::
    :::column span="":::
        **实体**

        PhoneNumber

    :::column-end:::
    :::column span="2":::
        **详细信息**

        电话号码（仅限美国和欧洲电话号码）。
      
    :::column-end:::
    :::column span="2":::
      **支持的文档语言**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-email"></a>类别：电子邮件

此类别包含以下实体：

:::row:::
    :::column span="":::
        **实体**

        电子邮件

    :::column-end:::
    :::column span="2":::
        **详细信息**

        电子邮件地址。
      
    :::column-end:::
    :::column span="2":::
      **支持的文档语言**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-url"></a>类别：URL

此类别包含以下实体：

:::row:::
    :::column span="":::
        **实体**

        URL

    :::column-end:::
    :::column span="2":::
        **详细信息**

        指向网站的 URL。 
      
    :::column-end:::
    :::column span="2":::
      **支持的文档语言**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-ipaddress"></a>类别：IPAddress

此类别包含以下实体：

:::row:::
    :::column span="":::
        **实体**

        IPAddress

    :::column-end:::
    :::column span="2":::
        **详细信息**

        网络 IP 地址。 
      
    :::column-end:::
    :::column span="2":::
      **支持的文档语言**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-datetime"></a>类别：DateTime

此类别包含以下实体：

:::row:::
    :::column span="":::
        **实体**

        DateTime

    :::column-end:::
    :::column span="2":::
        **详细信息**

        某天的日期和时间。 
      
    :::column-end:::
    :::column span="2":::
      **支持的文档语言**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

此类别中的实体可以具有以下子类别

#### <a name="subcategories"></a>子类别

此类别中的实体可以有以下子类别。

:::row:::
    :::column span="":::
        **实体子类别**

        日期

    :::column-end:::
    :::column span="2":::
        **详细信息**

        日历日期。
      
    :::column-end:::
    :::column span="2":::
      **支持的文档语言**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        时间

    :::column-end:::
    :::column span="2":::

        当日时间。
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        DateRange

    :::column-end:::
    :::column span="2":::

        日期范围。
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        TimeRange

    :::column-end:::
    :::column span="2":::

        时间范围。
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        持续时间

    :::column-end:::
    :::column span="2":::

        持续时间。
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        设置

    :::column-end:::
    :::column span="2":::

        集，重复的时间。
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-quantity"></a>类别：数量

此类别包含以下实体：

:::row:::
    :::column span="":::
        **实体**

        数量

    :::column-end:::
    :::column span="2":::
        **详细信息**

        数字和数量。
      
    :::column-end:::
    :::column span="2":::
      **支持的文档语言**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>子类别

此类别中的实体可以有以下子类别。

:::row:::
    :::column span="":::
        **实体子类别**

        Number

    :::column-end:::
    :::column span="2":::
        **详细信息**

        数字。
      
    :::column-end:::
    :::column span="2":::
      **支持的文档语言**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        百分比

    :::column-end:::
    :::column span="2":::

        百分比
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        序号

    :::column-end:::
    :::column span="2":::

        序号。
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Age

    :::column-end:::
    :::column span="2":::

        年龄。
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        货币

    :::column-end:::
    :::column span="2":::

        货币
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        维度

    :::column-end:::
    :::column span="2":::

        维度和度量。
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        温度

    :::column-end:::
    :::column span="2":::

        温度。
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
