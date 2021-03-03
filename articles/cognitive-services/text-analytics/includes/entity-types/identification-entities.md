---
title: 标识实体
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/17/2021
ms.author: aahi
ms.openlocfilehash: a376b050d79709885e3542d330bb6b1eea48d046
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750853"
---
### <a name="financial-account-identification"></a>财务帐户标识

此实体类别包括标识的财务信息和正式形式。

#### <a name="category-aba-routing-number"></a>类别： ABA 路由号

此类别包含以下实体：

:::row:::
    :::column span="":::
        **实体**

        ABA 路由号码

    :::column-end:::
    :::column span="2":::
        **详细信息**

        美国银行家协会 (ABA) 转账路由号码。
      
    :::column-end:::
:::row-end:::

#### <a name="category-swift-code"></a>类别： SWIFT 代码

此类别包含以下实体：

:::row:::
    :::column span="":::
        **实体**

        SWIFT 代码

    :::column-end:::
    :::column span="2":::
        **详细信息**

        付款说明信息中的 SWIFT 代码。
      
    :::column-end:::
:::row-end:::

#### <a name="category-credit-card"></a>类别：信用卡

此类别包含以下实体：

:::row:::
    :::column span="":::
        **实体**

        信用卡

    :::column-end:::
    :::column span="2":::
        **详细信息**

        信用卡卡号。 
      
    :::column-end:::
:::row-end:::

#### <a name="category-international-banking-account-number-iban"></a>类别：国际银行帐号 (IBAN)  

此类别包含以下实体：

:::row:::
    :::column span="":::
        **实体**

        信用卡

    :::column-end:::
    :::column span="2":::
        **详细信息**

        付款说明信息中的 IBAN 代码。
      
    :::column-end:::
:::row-end:::

### <a name="government-and-countryregion-specific-identification"></a>政府和国家/地区特定的标识

> [!NOTE]
> 以下特定于财务和国家/地区的实体不随 `domain=phi` 参数一起返回：
> * 护照号码
> * 税务 ID

以下实体按国家/地区分组和列出：

#### <a name="argentina"></a>阿根廷

:::row:::
    :::column span="":::
        **实体**

        阿根廷国民身份证 (DNI) 号码

    :::column-end:::
:::row-end:::


#### <a name="austria"></a>奥地利

:::row:::
    :::column span="":::
        **实体**

        奥地利标识卡

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        奥地利纳税标识号

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        奥地利增值增值税 (VAT) 号

    :::column-end:::
:::row-end:::



#### <a name="australia"></a>澳大利亚

:::row:::
    :::column span="":::
        **实体**

        澳大利亚银行帐号

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        澳大利亚业务数字

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        澳大利亚公司编号

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        澳大利亚驾照  

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        澳大利亚医疗帐号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        澳大利亚登记卡号码

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        澳大利亚登记卡号码

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        澳大利亚税金文件编号

    :::column-end:::

:::row-end:::


#### <a name="belgium"></a>比利时

:::row:::
    :::column span="":::
        **实体**

        比利时国号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        比利时值加税 (VAT) 号

    :::column-end:::

:::row-end:::


#### <a name="brazil"></a>巴西 

:::row:::
    :::column span="":::
        **实体**

        巴西法定实体编号 (CNPJ) 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        巴西 CPF 号

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        巴西国民身份证 (RG)

    :::column-end:::
:::row-end:::

#### <a name="canada"></a>加拿大

:::row:::
    :::column span="":::
        **实体**

        加拿大银行帐号

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        加拿大驾照号码

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        加拿大运行状况服务编号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        加拿大 passport 号码

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        加拿大个人健康标识号 (PHIN)

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        加拿大社会保险电话号码

    :::column-end:::
:::row-end:::

#### <a name="chile"></a>智利 

:::row:::
    :::column span="":::
        **实体**

        智利标识卡号

    :::column-end:::
:::row-end:::

#### <a name="china"></a>中国

:::row:::
    :::column span="":::
        **实体**

        中国居民标识卡 (PRC) 号

    :::column-end:::
:::row-end:::


#### <a name="european-union-eu"></a>欧盟 (EU)

:::row:::
    :::column span="":::
        **实体**

        欧盟借记卡号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        欧盟驱动程序的许可证编号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        欧盟国家识别号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        欧盟 passport 号码

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        欧盟社会保障号 (SSN) 或等效 ID

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        欧盟纳税标识号 (TIN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        欧盟 GPS 坐标

    :::column-end:::
:::row-end:::

#### <a name="france"></a>法国

:::row:::
    :::column span="":::
        **实体**

        法国驾照号码

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        法国健康保险编号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        法国国内 ID 卡 (CNI) 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        法国 passport 号码

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        法国社会安全号码 (INSEE)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        法国纳税标识号 (Numéro SPI) 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        法国增值销售税 (VAT) 号

    :::column-end:::
:::row-end:::

#### <a name="germany"></a>德国

:::row:::
    :::column span="":::
        **实体**

        德国驾照编号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        德国身份证号码

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        德国登记卡号码

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        德国税务标识号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        德国增值税号码

    :::column-end:::
:::row-end:::

#### <a name="hong-kong"></a>香港特别行政区

:::row:::
    :::column span="":::
        **实体**

        香港特别行政区身份证 (HKID) 号码

    :::column-end:::
:::row-end:::

#### <a name="hungary"></a>匈牙利

:::row:::
    :::column span="":::
        **实体**

        匈牙利个人识别码

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        匈牙利税务标识号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        匈牙利增值税号码

    :::column-end:::
:::row-end:::

#### <a name="india"></a>印度

:::row:::
    :::column span="":::
        **实体**

        印度永久帐号 (PAN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        印度唯一身份 (Aadhaar) 号码

    :::column-end:::
:::row-end:::


#### <a name="indonesia"></a>印度尼西亚

:::row:::
    :::column span="":::
        **实体**

        印度尼西亚身份证 (KTP) 号码

    :::column-end:::
:::row-end:::

#### <a name="ireland"></a>爱尔兰

:::row:::
    :::column span="":::
        **实体**

        爱尔兰个人公共服务 (PPS) 号码

    :::column-end:::
:::row-end:::

#### <a name="israel"></a>以色列

:::row:::
    :::column span="":::
        **实体**

        以色列国民身份证

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        以色列银行帐号

    :::column-end:::
:::row-end:::

#### <a name="italy"></a>意大利

:::row:::
    :::column span="":::
        **实体**

        意大利驾照编号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        意大利会计代码

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        意大利增值税号码

    :::column-end:::
:::row-end:::


#### <a name="japan"></a>日本

:::row:::
    :::column span="":::
        **实体**

        日本银行帐号

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        日本驾照编号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        日本 "我的数字" (个人) 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        日本 "我的数字" (公司) 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        日本居民注册号码

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        日本居住卡号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        日本社会保险号码 (SIN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        日本护照号码

    :::column-end:::
:::row-end:::

#### <a name="luxembourg"></a>卢森堡

:::row:::
    :::column span="":::
        **实体**

         (自然人员的卢森堡国识别号) 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        卢森堡国识别码 (非自然人员) 

    :::column-end:::
:::row-end:::

#### <a name="malta"></a>马耳他

:::row:::
    :::column span="":::
        **实体**

        马耳他身份证号码

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        马耳他税务标识号

    :::column-end:::
:::row-end:::


#### <a name="new-zealand"></a>新西兰

:::row:::
    :::column span="":::
        **实体**

        新西兰银行帐号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        新西兰驾照编号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        新西兰内陆收入编号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        新西兰卫生部编号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       新西兰社会福利号码

    :::column-end:::
:::row-end:::


#### <a name="philippines"></a>菲律宾

:::row:::
    :::column span="":::
        **实体**

        菲律宾统一多用途标识号码

    :::column-end:::
:::row-end:::

#### <a name="portugal"></a>葡萄牙 

:::row:::
    :::column span="":::
        **实体**

        葡萄牙公民卡编号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       葡萄牙税务标识号

    :::column-end:::
:::row-end:::

#### <a name="singapore"></a>新加坡

:::row:::
    :::column span="":::
        **实体**

        新加坡国家注册身份证 (NRIC) 号码

    :::column-end:::
:::row-end:::


#### <a name="south-africa"></a>南非

:::row:::
    :::column span="":::
        **实体**

        南非身份证号码

    :::column-end:::
:::row-end:::


#### <a name="south-korea"></a>韩国

:::row:::
    :::column span="":::
        **实体**

        韩国居民注册号码

    :::column-end:::
:::row-end:::

#### <a name="spain"></a>西班牙

:::row:::
    :::column span="":::
        **实体**

        西班牙 DNI

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        西班牙社会安全号码 (SSN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        西班牙税务标识号

    :::column-end:::
:::row-end:::
 
#### <a name="switzerland"></a>瑞士

:::row:::
    :::column span="":::
        **实体**

        瑞士社会安全号码 AHV

    :::column-end:::
:::row-end:::


#### <a name="taiwan"></a>台湾 

:::row:::
    :::column span="":::
        **实体**

        台湾国民身份证

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       台湾居民证 (ARC/TARC)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        台湾护照号码

    :::column-end:::
:::row-end:::

#### <a name="united-kingdom"></a>英国

:::row:::
    :::column span="":::
        **实体**

        英国 驾照编号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       英国 选举名册编号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       英国 国家医疗服务 (NHS) 号码

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       英国 国家保险号码 (NINO)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       英国 或美国护照号码

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

       英国 唯一纳税人参考编号

    :::column-end:::

:::row-end:::


#### <a name="united-states"></a>美国

:::row:::
    :::column span="":::
        **实体**

        美国社会安全号码 (SSN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       美国驾照编号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       美国或英国 护照号码

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       美国个人纳税人标识号 (ITIN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       美国禁毒署 (DEA) 编号

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       美国银行帐号

    :::column-end:::
:::row-end:::
