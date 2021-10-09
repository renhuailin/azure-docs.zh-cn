---
title: 标识实体
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 09/09/2021
ms.author: aahi
ms.openlocfilehash: 621d74d37ef8593ee109aa9e5e099bcc2b381383
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672550"
---
### <a name="financial-account-identification"></a>财务帐户标识

此实体类别包括标识的财务信息和正式形式。

#### <a name="category-aba-routing-number"></a>类别：ABA 银行代号

此类别包含以下实体：

:::row:::
    :::column span="":::
        **实体**

        ABA 银行代号

    :::column-end:::
    :::column span="2":::
        **详细信息**

        美国银行家协会 (ABA) 转账路由号码。 也随 `domain=phi` 一起返回。

        若要获得此实体类别，请将 `ABARoutingNumber` 添加到 `piiCategories` 参数中。 如果检测到 `ABARoutingNumber`，也会在 API 响应中返回。
      
    :::column-end:::
    :::column span="2":::
      **支持的文档语言**

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="category-swift-code"></a>类别：SWIFT 代码

此类别包含以下实体：

:::row:::
    :::column span="":::
        **实体**

        SWIFT 代码

    :::column-end:::
    :::column span="2":::
        **详细信息**

        付款说明信息中的 SWIFT 代码。 也随 `domain=phi` 一起返回。

        若要获得此实体类别，请将 `SWIFTCode` 添加到 `piiCategories` 参数中。 如果检测到 `SWIFTCode`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="2":::
      **支持的文档语言**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`
      
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

        信用卡卡号。 也随 `domain=phi` 一起返回。

        若要获得此实体类别，请将 `CreditCardNumber` 添加到 `piiCategories` 参数中。 如果检测到 `CreditCardNumber`，会在 API 响应中返回。

    :::column-end:::
    :::column span="2":::
      **支持的文档语言**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`, `zh-hans`, `ja`, `ko`
      
   :::column-end:::
:::row-end:::

#### <a name="category-international-banking-account-number-iban"></a>类别：国际银行帐号 (IBAN) 

此类别包含以下实体：

:::row:::
    :::column span="":::
        **实体**

        国际银行帐号

    :::column-end:::
    :::column span="2":::
        **详细信息**

        付款说明信息中的 IBAN 代码。 也随 `domain=phi` 一起返回。

        若要获得此实体类别，请将 `InternationalBankingAccountNumber` 添加到 `piiCategories` 参数中。 如果检测到 `InternationalBankingAccountNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="2":::
      **支持的文档语言**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`
      
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
    :::column span="2":::
        **详细信息** 也随 `domain=phi` 一起返回。
        
        若要获得此实体类别，请将 `ARNationalIdentityNumber` 添加到 `piiCategories` 参数中。 如果检测到 `ARNationalIdentityNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `en`, `es`
      
   :::column-end:::
:::row-end:::


#### <a name="austria"></a>奥地利

:::row:::
    :::column span="":::
        **实体**

        奥地利身份证

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `ATIdentityCard` 添加到 `piiCategories` 参数中。 如果检测到 `ATIdentityCard`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        奥地利税务标识号

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `ATTaxIdentificationNumber` 添加到 `piiCategories` 参数中。 如果检测到 `ATTaxIdentificationNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        奥地利增值税 (VAT) 号码

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `ATValueAddedTaxNumber` 添加到 `piiCategories` 参数中。 如果检测到 `ATValueAddedTaxNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::



#### <a name="australia"></a>澳大利亚

:::row:::
    :::column span="":::
        **实体**

        澳大利亚银行帐号

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `AUDriversLicenseNumber` 添加到 `piiCategories` 参数中。 如果检测到 `AUDriversLicenseNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        澳大利亚企业编号

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `AUBusinessNumber` 添加到 `piiCategories` 参数中。 如果检测到 `AUBusinessNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        澳大利亚公司编号

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `AUCompanyNumber` 添加到 `piiCategories` 参数中。 如果检测到 `AUCompanyNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        澳大利亚驾照编号  

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `AUDriversLicense` 添加到 `piiCategories` 参数中。 如果检测到 `AUDriversLicense`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        澳大利亚医疗帐号

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `AUMedicalAccountNumber` 添加到 `piiCategories` 参数中。 如果检测到 `AUMedicalAccountNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        澳大利亚护照号码

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `ATPassportNumber` 添加到 `piiCategories` 参数中。 如果检测到 `ATPassportNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        澳大利亚税文件号

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `ATTaxIdentificationNumber` 添加到 `piiCategories` 参数中。 如果检测到 `ATTaxIdentificationNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="belgium"></a>比利时

:::row:::
    :::column span="":::
        **实体**

        比利时国民身份证号码

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `BENationalNumber` 添加到 `piiCategories` 参数中。 如果检测到 `BENationalNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        比利时增值税 (VAT) 号码

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `BEValueAddedTaxNumber` 添加到 `piiCategories` 参数中。 如果检测到 `BEValueAddedTaxNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::


#### <a name="brazil"></a>巴西 

:::row:::
    :::column span="":::
        **实体**

        巴西法人号码 (CNPJ)

        

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `BRLegalEntityNumber` 添加到 `piiCategories` 参数中。 如果检测到 `BRLegalEntityNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        巴西 CPF 号码

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `BRCPFNumber` 添加到 `piiCategories` 参数中。 如果检测到 `BRCPFNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        巴西国民身份证 (RG)

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `BRNationalIDRG` 添加到 `piiCategories` 参数中。 如果检测到 `BRNationalIDRG`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="canada"></a>加拿大

:::row:::
    :::column span="":::
        **实体**

        加拿大银行帐号

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `CABankAccountNumber` 添加到 `piiCategories` 参数中。 如果检测到 `CABankAccountNumber`，会在 API 响应中返回。
    
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        加拿大驾照编号

    :::column-end:::

    :::column span="2":::

        若要获得此实体类别，请将 `CADriversLicenseNumber` 添加到 `piiCategories` 参数中。 如果检测到 `CADriversLicenseNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        加拿大医疗服务号码

        
    :::column-end:::

    :::column span="2":::

        若要获得此实体类别，请将 `CAHealthServiceNumber` 添加到 `piiCategories` 参数中。 如果检测到 `CAHealthServiceNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        加拿大护照号码

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `CAPassportNumber` 添加到 `piiCategories` 参数中。 如果检测到 `CAPassportNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        加拿大个人健康标识号 (PHIN)

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `CAPersonalHealthIdentification` 添加到 `piiCategories` 参数中。 如果检测到 `CAPersonalHealthIdentification`，会在 API 响应中返回。

        也随 `domain=phi` 一起返回。
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        加拿大社会保险号码

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `CASocialInsuranceNumber` 添加到 `piiCategories` 参数中。 如果检测到 `CASocialInsuranceNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::

#### <a name="chile"></a>智利 

:::row:::
    :::column span="":::
        **实体**

        智利身份证号码

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `CLIdentityCardNumber` 添加到 `piiCategories` 参数中。 如果检测到 `CLIdentityCardNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `es`
      
   :::column-end:::
:::row-end:::

#### <a name="china"></a>中国

:::row:::
    :::column span="":::
        **实体**

        中国居民身份证 (PRC) 号码

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `CNResidentIdentityCardNumber` 添加到 `piiCategories` 参数中。 如果检测到 `CNResidentIdentityCardNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="european-union-eu"></a>欧盟 (EU)

:::row:::
    :::column span="":::
        **实体**

        欧盟借记卡号码

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `EUDebitCardNumber` 添加到 `piiCategories` 参数中。 如果检测到 `EUDebitCardNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        欧盟驾照号码

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `EUDriversLicenseNumber` 添加到 `piiCategories` 参数中。 如果检测到 `EUDriversLicenseNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        欧盟 GPU 坐标

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `EUGPSCoordinates` 添加到 `piiCategories` 参数中。 如果检测到 `EUGPSCoordinates`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        欧盟国家标识号

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `EUNationalIdentificationNumber` 添加到 `piiCategories` 参数中。 如果检测到 `EUNationalIdentificationNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        欧盟护照号码

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `EUPassportNumber` 添加到 `piiCategories` 参数中。 如果检测到 `EUPassportNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        欧盟社会安全号码 (SSN) 或等效 ID

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `EUSocialSecurityNumber` 添加到 `piiCategories` 参数中。 如果检测到 `EUSocialSecurityNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        欧盟纳税标识号 (TIN)

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `EUTaxIdentificationNumber` 添加到 `piiCategories` 参数中。 如果检测到 `EUTaxIdentificationNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::

#### <a name="france"></a>法国

:::row:::
    :::column span="":::
        **实体**

        法国驾照编号

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `FRDriversLicenseNumber` 添加到 `piiCategories` 参数中。 如果检测到 `FRDriversLicenseNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        法国医疗保险号码

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `FRHealthInsuranceNumber` 添加到 `piiCategories` 参数中。 如果检测到 `FRHealthInsuranceNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        法国国民身份证 (CNI)

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `FRNationalID` 添加到 `piiCategories` 参数中。 如果检测到 `FRNationalID`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        法国护照号码

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `FRPassportNumber` 添加到 `piiCategories` 参数中。 如果检测到 `FRPassportNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        法国社会安全号码 (INSEE)

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `FRSocialSecurityNumber` 添加到 `piiCategories` 参数中。 如果检测到 `FRSocialSecurityNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        法国税务标识号 (Numéro SPI)

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `FRTaxIdentificationNumber` 添加到 `piiCategories` 参数中。 如果检测到 `FRTaxIdentificationNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        法国增值税 (VAT) 号码

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `FRValueAddedTaxNumber` 添加到 `piiCategories` 参数中。 如果检测到 `FRValueAddedTaxNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::

#### <a name="germany"></a>德国

:::row:::
    :::column span="":::
        **实体**

        德国驾照编号

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `DEDriversLicenseNumber` 添加到 `piiCategories` 参数中。 如果检测到 `DEDriversLicenseNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        德国身份证号码

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `DEIdentityCardNumber` 添加到 `piiCategories` 参数中。 如果检测到 `DEIdentityCardNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        德国护照号码

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `DEPassportNumber` 添加到 `piiCategories` 参数中。 如果检测到 `DEPassportNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        德国税务标识号

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `DETaxIdentificationNumber` 添加到 `piiCategories` 参数中。 如果检测到 `DETaxIdentificationNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        德国增值税号码

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `DEValueAddedNumber` 添加到 `piiCategories` 参数中。 如果检测到 `DEValueAddedNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::

#### <a name="hong-kong"></a>香港特别行政区

:::row:::
    :::column span="":::
        **实体**

        香港特别行政区身份证 (HKID) 号码

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `HKIdentityCardNumber` 添加到 `piiCategories` 参数中。 如果检测到 `HKIdentityCardNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="hungary"></a>匈牙利

:::row:::
    :::column span="":::
        **实体**

        匈牙利个人标识号

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `HUPersonalIdentificationNumber` 添加到 `piiCategories` 参数中。 如果检测到 `HUPersonalIdentificationNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        匈牙利税务标识号

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `HUTaxIdentificationNumber` 添加到 `piiCategories` 参数中。 如果检测到 `HUTaxIdentificationNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        匈牙利增值税号码

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `HUValueAddedNumber` 添加到 `piiCategories` 参数中。 如果检测到 `HUValueAddedNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="india"></a>印度

:::row:::
    :::column span="":::
        **实体**

        印度永久帐号 (PAN)

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `INPermanentAccount` 添加到 `piiCategories` 参数中。 如果检测到 `INPermanentAccount`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        印度唯一身份 (Aadhaar) 号码

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `INUniqueIdentificationNumber` 添加到 `piiCategories` 参数中。 如果检测到 `INUniqueIdentificationNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="indonesia"></a>印度尼西亚

:::row:::
    :::column span="":::
        **实体**

        印度尼西亚身份证 (KTP) 号码

    :::column-end:::
    :::column span="2":::

        **详细信息**

        若要获得此实体类别，请将 `IDIdentityCardNumber` 添加到 `piiCategories` 参数中。 如果检测到 `IDIdentityCardNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="ireland"></a>爱尔兰

:::row:::
    :::column span="":::
        **实体**

        爱尔兰个人公共服务 (PPS) 号码

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `IEPersonalPublicServiceNumber` 添加到 `piiCategories` 参数中。 如果检测到 `IEPersonalPublicServiceNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
 
        爱尔兰个人公共服务 (PPS) 号码 v2

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `IEPersonalPublicServiceNumberV2` 添加到 `piiCategories` 参数中。 如果检测到 `IEPersonalPublicServiceNumberV2`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="israel"></a>以色列

:::row:::
    :::column span="":::
        **实体**

        以色列国民身份证

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `ILNationalID` 添加到 `piiCategories` 参数中。 如果检测到 `ILNationalID`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        以色列银行帐号

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `ILBankAccountNumber` 添加到 `piiCategories` 参数中。 如果检测到 `ILBankAccountNumber`，会在 API 响应中返回。
    
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="italy"></a>意大利

:::row:::
    :::column span="":::
        **实体**

        意大利驾照编号

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `ITDriversLicenseNumber` 添加到 `piiCategories` 参数中。 如果检测到 `ITDriversLicenseNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        意大利会计代码

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `ITFiscalCode` 添加到 `piiCategories` 参数中。 如果检测到 `ITFiscalCode`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        意大利增值税号码

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `ITValueAddedTaxNumber` 添加到 `piiCategories` 参数中。 如果检测到 `ITValueAddedTaxNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::


#### <a name="japan"></a>日本

:::row:::
    :::column span="":::
        **实体**

        日本银行帐号

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `JPBankAccountNumber` 添加到 `piiCategories` 参数中。 如果检测到 `JPBankAccountNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        日本驾照编号

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `JPDriversLicenseNumber` 添加到 `piiCategories` 参数中。 如果检测到 `JPDriversLicenseNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        日本“我的号码”（个人）

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `JPMyNumberPersonal` 添加到 `piiCategories` 参数中。 如果检测到 `JPMyNumberPersonal`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        日本“我的号码”（公司）

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `JPMyNumberCorporate` 添加到 `piiCategories` 参数中。 如果检测到 `JPMyNumberCorporate`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        日本居民注册号码

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `ITValueAddedTaxNumber` 添加到 `piiCategories` 参数中。 如果检测到 `ITValueAddedTaxNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::

     `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        日本居住证号码

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `JPResidenceCardNumber` 添加到 `piiCategories` 参数中。 如果检测到 `JPResidenceCardNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        日本社会保险号码 (SIN)

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `JPSocialInsuranceNumber` 添加到 `piiCategories` 参数中。 如果检测到 `JPSocialInsuranceNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        日本护照号码

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `JPPassportNumber` 添加到 `piiCategories` 参数中。 如果检测到 `JPPassportNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::

#### <a name="luxembourg"></a>卢森堡

:::row:::
    :::column span="":::
        **实体**

        卢森堡国家标识号（自然人）

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `LUNationalIdentificationNumberNatural` 添加到 `piiCategories` 参数中。 如果检测到 `LUNationalIdentificationNumberNatural`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        卢森堡国家标识号（非自然人）

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `LUNationalIdentificationNumberNonNatural` 添加到 `piiCategories` 参数中。 如果检测到 `LUNationalIdentificationNumberNonNatural`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::

#### <a name="malta"></a>马耳他

:::row:::
    :::column span="":::
        **实体**

        马耳他身份证号码

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `MTIdentityCardNumber` 添加到 `piiCategories` 参数中。 如果检测到 `MTIdentityCardNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        马耳他税务标识号

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `MTTaxIDNumber` 添加到 `piiCategories` 参数中。 如果检测到 `MTTaxIDNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="new-zealand"></a>新西兰

:::row:::
    :::column span="":::
        **实体**

        新西兰银行帐号

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `NZBankAccountNumber` 添加到 `piiCategories` 参数中。 如果检测到 `NZBankAccountNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        新西兰驾照编号

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `NZDriversLicenseNumber` 添加到 `piiCategories` 参数中。 如果检测到 `NZDriversLicenseNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        新西兰内陆收入编号

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `NZInlandRevenueNumber` 添加到 `piiCategories` 参数中。 如果检测到 `NZInlandRevenueNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        新西兰卫生部编号

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `NZMinistryOfHealthNumber` 添加到 `piiCategories` 参数中。 如果检测到 `NZMinistryOfHealthNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       新西兰社会福利号码

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `NZSocialWelfareNumber` 添加到 `piiCategories` 参数中。 如果检测到 `NZSocialWelfareNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="philippines"></a>菲律宾

:::row:::
    :::column span="":::
        **实体**

        菲律宾统一多用途标识号码

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `PHUnifiedMultiPurposeIDNumber` 添加到 `piiCategories` 参数中。 如果检测到 `PHUnifiedMultiPurposeIDNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="portugal"></a>葡萄牙 

:::row:::
    :::column span="":::
        **实体**

        葡萄牙公民卡编号

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `PTCitizenCardNumber` 添加到 `piiCategories` 参数中。 如果检测到 `PTCitizenCardNumber`，会在 API 响应中返回。
          
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `pt-pt`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       葡萄牙税务标识号

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `PTTaxIdentificationNumber` 添加到 `piiCategories` 参数中。 如果检测到 `PTTaxIdentificationNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::

      `pt-pt`
      
   :::column-end:::
:::row-end:::

#### <a name="singapore"></a>新加坡

:::row:::
    :::column span="":::
        **实体**

        新加坡国家注册身份证 (NRIC) 号码

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `PTTaxIdentificationNumber` 添加到 `piiCategories` 参数中。 如果检测到 `PTTaxIdentificationNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `en`, `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="south-africa"></a>南非

:::row:::
    :::column span="":::
        **实体**

        南非身份证号码

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `ZAIdentificationNumber` 添加到 `piiCategories` 参数中。 如果检测到 `ZAIdentificationNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="south-korea"></a>韩国

:::row:::
    :::column span="":::
        **实体**

        韩国居民注册号码

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `KRResidentRegistrationNumber` 添加到 `piiCategories` 参数中。 如果检测到 `KRResidentRegistrationNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `ko`
      
   :::column-end:::
:::row-end:::

#### <a name="spain"></a>西班牙

:::row:::
    :::column span="":::
        **实体**

        西班牙 DNI

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `ESDNI` 添加到 `piiCategories` 参数中。 如果检测到 `ESDNI`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        西班牙社会安全号码 (SSN)

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `ESSocialSecurityNumber` 添加到 `piiCategories` 参数中。 如果检测到 `ESSocialSecurityNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        西班牙税务标识号

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `ESTaxIdentificationNumber` 添加到 `piiCategories` 参数中。 如果检测到 `ESTaxIdentificationNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
 
#### <a name="switzerland"></a>瑞士

:::row:::
    :::column span="":::
        **实体**

        瑞士社会安全号码 AHV

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `CHSocialSecurityNumber` 添加到 `piiCategories` 参数中。 如果检测到 `CHSocialSecurityNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `fr`, `de`, `it`
      
   :::column-end:::
:::row-end:::


#### <a name="taiwan"></a>台湾 

:::row:::
    :::column span="":::
        **实体**

        台湾居民身份证

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `TWNationalID` 添加到 `piiCategories` 参数中。 如果检测到 `TWNationalID`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       台湾居民证 (ARC/TARC)

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `TWResidentCertificate` 添加到 `piiCategories` 参数中。 如果检测到 `TWResidentCertificate`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        台湾护照号码

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `TWPassportNumber` 添加到 `piiCategories` 参数中。 如果检测到 `TWPassportNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="united-kingdom"></a>英国

:::row:::
    :::column span="":::
        **实体**

        英国 驾照编号

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `UKDriversLicenseNumber` 添加到 `piiCategories` 参数中。 如果检测到 `UKDriversLicenseNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `en`
      
    :::column-end:::
    
:::row-end:::
:::row:::
    :::column span="":::

       英国 选举名册编号

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `UKNationalInsuranceNumber` 添加到 `piiCategories` 参数中。 如果检测到 `UKNationalInsuranceNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       英国 国家医疗服务 (NHS) 号码

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `UKNationalHealthNumber` 添加到 `piiCategories` 参数中。 如果检测到 `UKNationalHealthNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       英国 国家保险号码 (NINO)

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `UKNationalInsuranceNumber` 添加到 `piiCategories` 参数中。 如果检测到 `UKNationalInsuranceNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       英国 或美国护照号码

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `USUKPassportNumber` 添加到 `piiCategories` 参数中。 如果检测到 `USUKPassportNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       英国 唯一纳税人参考编号

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `UKUniqueTaxpayerNumber` 添加到 `piiCategories` 参数中。 如果检测到 `UKUniqueTaxpayerNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="united-states"></a>美国

:::row:::
    :::column span="":::
        **实体**

        美国社会安全号码 (SSN)

    :::column-end:::
    :::column span="2":::
        **详细信息**

        若要获得此实体类别，请将 `USSocialSecurityNumber` 添加到 `piiCategories` 参数中。 如果检测到 `USSocialSecurityNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       美国驾照编号

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `USDriversLicenseNumber` 添加到 `piiCategories` 参数中。 如果检测到 `USDriversLicenseNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       美国或英国 护照号码

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `USUKPassportNumber` 添加到 `piiCategories` 参数中。 如果检测到 `USUKPassportNumber`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       美国个人纳税人标识号 (ITIN)

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `USIndividualTaxpayerIdentification` 添加到 `piiCategories` 参数中。 如果检测到 `USIndividualTaxpayerIdentification`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       美国禁毒署 (DEA) 编号

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `DrugEnforcementAgencyNumber` 添加到 `piiCategories` 参数中。 如果检测到 `DrugEnforcementAgencyNumber`，会在 API 响应中返回。
      
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       美国银行帐号

    :::column-end:::
    :::column span="2":::

        若要获得此实体类别，请将 `USBankAccountNumber` 添加到 `piiCategories` 参数中。 如果检测到 `USBankAccountNumber`，会在 API 响应中返回。
        
        也随 `domain=phi` 一起返回。
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
