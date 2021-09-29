---
title: 用于个人信息的命名实体
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 09/09/2021
ms.author: aahi
ms.openlocfilehash: ce85895307af9e437fb9ceaaff3f1a385e77e17d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672551"
---
> [!NOTE]
> 若要检测受保护的运行状况信息 (PHI)，请使用 `domain=phi` 参数和模型版本 `2020-04-01` 或更高版本。
>
> 例如：`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/recognition/pii?domain=phi&model-version=2021-01-15`
 
将请求发送到 `/v3.1/entities/recognition/pii` 终结点时，会返回以下实体类别。


| 类别   |  说明                          |
|------------|-------------|
| [Person](#category-person)      |  人员姓名。  |
| [PersonType](#category-persontype) | 某人的工作类型或角色。 |
| [电话号码](#category-phonenumber) |电话号码（仅限美国和欧洲电话号码）。 |
| 组织 |  公司、组、政府机构和其他组织。  |
| [Address](#category-address) | 完整的邮寄地址。  |
| [Email](#category-email) | 电子邮件地址。   |
| [URL](#category-url) | 指向网站的 URL。  |
| [IPAddress](#category-ipaddress) | 网络 IP 地址。  |
| [DateTime](#category-datetime) | 某天的日期和时间。 | 
| [数量](#category-quantity) | 数字和数量。  |
| [Azure 信息](#azure-information) | 可识别的 Azure 信息，如身份验证信息。  |
| [标识](#identification) | 金融和国家/地区特定标识。  |

### <a name="category-person"></a>类别：人员

此类别包含以下实体：

:::row:::
    :::column span="":::
        **实体**

        人员

    :::column-end:::
    :::column span="2":::
        **详细信息**

        人员姓名。 也随 `domain=phi` 一起返回。

        若要获得此实体类别，请将 `Person` 添加到 `piiCategories` 参数中。 如果检测到 `Person`，会在 API 响应中返回。
      
    :::column-end:::
    
    :::column span="":::
      **支持的文档语言**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
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

        某人的工作类型或角色。

        若要获得此实体类别，请将 `PersonType` 添加到 `piiCategories` 参数中。 如果检测到 `PersonType`，会在 API 响应中返回。
      
    :::column-end:::

    :::column span="":::
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

        电话号码（仅限美国和欧洲电话号码）。 也随 `domain=phi` 一起返回。

        若要获得此实体类别，请将 `PhoneNumber` 添加到 `piiCategories` 参数中。 如果检测到 `PhoneNumber`，会在 API 响应中返回。
      
    :::column-end:::

    :::column span="":::
      **支持的文档语言**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
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

        公司、政治团体、乐队、体育俱乐部、政府机构和公共组织。 民族和宗教不包括在此实体类型中。 也随 `domain=phi` 一起返回。

        若要获得此实体类别，请将 `Organization` 添加到 `piiCategories` 参数中。 如果检测到 `Organization`，会在 API 响应中返回。
      
    :::column-end:::

    :::column span="":::
      **支持的文档语言**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
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

        完整邮寄地址。 也随 `domain=phi` 一起返回。

        若要获得此实体类别，请将 `Address` 添加到 `piiCategories` 参数中。 如果检测到 `Address`，会在 API 响应中返回。
      
    :::column-end:::

    :::column span="":::
      **支持的文档语言**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
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

        电子邮件地址。 也随 `domain=phi` 一起返回。
      
        若要获得此实体类别，请将 `Email` 添加到 `piiCategories` 参数中。 如果检测到 `Email`，会在 API 响应中返回。

    :::column-end:::
    :::column span="":::
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

        指向网站的 URL。 也随 `domain=phi` 一起返回。

        若要获得此实体类别，请将 `URL` 添加到 `piiCategories` 参数中。 如果检测到 `URL`，会在 API 响应中返回。
      
    :::column-end:::

    :::column span="":::
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

        网络 IP 地址。 也随 `domain=phi` 一起返回。

        若要获得此实体类别，请将 `IPAddress` 添加到 `piiCategories` 参数中。 如果检测到 `IPAddress`，会在 API 响应中返回。
      
    :::column-end:::

    :::column span="":::
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

        若要获得此实体类别，请将 `DateTime` 添加到 `piiCategories` 参数中。 如果检测到 `DateTime`，会在 API 响应中返回。
      
    :::column-end:::
:::column span="":::
      **支持的文档语言**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
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

        若要获得此实体类别，请将 `Quantity` 添加到 `piiCategories` 参数中。 如果检测到 `Quantity`，会在 API 响应中返回。
      
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

        Age

    :::column-end:::
    :::column span="2":::
        **详细信息**

        年龄。 

        若要获得此实体类别，请将 `Age` 添加到 `piiCategories` 参数中。 如果检测到 `Age`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="2":::
        **支持的文档语言**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="azure-information"></a>Azure 信息

这些实体类别包含可识别的 Azure 信息，包括身份验证信息和连接字符串。 不随 `domain=phi` 参数一起返回。

:::row:::
    :::column span="":::
        **实体**

        Azure DocumentDB 身份验证密钥 

    :::column-end:::
    :::column span="2":::
        **详细信息**

        Azure Cosmos DB 服务器的授权密钥。   

        若要获得此实体类别，请将 `AzureDocumentDBAuthKey` 添加到 `piiCategories` 参数中。 如果检测到 `AzureDocumentDBAuthKey`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::
      **支持的文档语言**

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure IAAS 数据库连接字符串和 Azure SQL 连接字符串。
        

    :::column-end:::
    :::column span="2":::

        Azure 基础结构即服务 (IaaS) 数据库的连接字符串和 SQL 连接字符串。

        若要获得此实体类别，请将 `AzureIAASDatabaseConnectionAndSQLString` 添加到 `piiCategories` 参数中。 如果检测到 `AzureIAASDatabaseConnectionAndSQLString`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure IoT 连接字符串  

    :::column-end:::
    :::column span="2":::

        Azure IoT 的连接字符串。 
      
        若要获得此实体类别，请将 `AzureIoTConnectionString` 添加到 `piiCategories` 参数中。 如果检测到 `AzureIoTConnectionString`，会在 API 响应中返回。

    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure 发布设置密码  

    :::column-end:::
    :::column span="2":::

        Azure 发布设置的密码。

        若要获得此实体类别，请将 `AzurePublishSettingPassword` 添加到 `piiCategories` 参数中。 如果检测到 `AzurePublishSettingPassword`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Redis 缓存连接字符串 

    :::column-end:::
    :::column span="2":::

        Redis 缓存的连接字符串。

        若要获得此实体类别，请将 `AzureRedisCacheString` 添加到 `piiCategories` 参数中。 如果检测到 `AzureRedisCacheString`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure SAS 

    :::column-end:::
    :::column span="2":::

        Azure 软件即服务 (SaaS) 的连接字符串。

        若要获得此实体类别，请将 `AzureSAS` 添加到 `piiCategories` 参数中。 如果检测到 `AzureSAS`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure 服务总线连接字符串

    :::column-end:::
    :::column span="2":::

        Azure 服务总线的连接字符串。

        若要获得此实体类别，请将 `AzureServiceBusString` 添加到 `piiCategories` 参数中。 如果检测到 `AzureServiceBusString`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure 存储帐户密钥 

    :::column-end:::
    :::column span="2":::

        Azure 存储帐户的帐户密钥。 

        若要获得此实体类别，请将 `AzureStorageAccountKey` 添加到 `piiCategories` 参数中。 如果检测到 `AzureStorageAccountKey`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure 存储帐户密钥（通用）

    :::column-end:::
    :::column span="2":::

        Azure 存储帐户的通用帐户密钥。

        若要获得此实体类别，请将 `AzureStorageAccountGeneric` 添加到 `piiCategories` 参数中。 如果检测到 `AzureStorageAccountGeneric`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        SQL Server 连接字符串 

    :::column-end:::
    :::column span="2":::

        运行 SQL Server 的计算机的连接字符串。

        若要获得此实体类别，请将 `SQLServerConnectionString` 添加到 `piiCategories` 参数中。 如果检测到 `SQLServerConnectionString`，会在 API 响应中返回。
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::

### <a name="identification"></a>标识

[!INCLUDE [supported identification entities](./identification-entities.md)]
