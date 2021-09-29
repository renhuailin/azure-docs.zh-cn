---
title: Azure API 管理中的产品模板 | Microsoft 文档
description: 了解如何在 Azure API 管理开发人员门户中自定义产品页面的内容。
services: api-management
documentationcenter: ''
author: dlepow
manager: erikre
editor: ''
ms.assetid: 49f9254c-4c5f-4ed4-9c8d-798f44e805ee
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: danlep
ms.openlocfilehash: 08b14104339be5a05bc7db25088536f2d0be51de
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128639083"
---
# <a name="product-templates-in-azure-api-management"></a>Azure API 管理中的产品模板

通过 Azure API 管理，用户能够使用一组用于配置内容的模板自定义开发人员门户页面的内容。 使用 [DotLiquid](http://dotliquidmarkup.org/) 语法和所选编辑器（例如 [DotLiquid for Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)），以及提供的一组本地化[字符串资源](api-management-template-resources.md#strings)、[字形资源](api-management-template-resources.md#glyphs)和[页面控件](api-management-page-controls.md)，即可根据这些模板的使用需要非常灵活地配置页面内容。  
  
 本部分中的模板可用于自定义开发人员门户中的产品页面的内容。  
  
-   [产品列表](#ProductList)  
  
-   [Product](#Product)  
  
> [!NOTE]
>  示例默认模板包含在后续文档中，但是会因持续改进而有所更改。 通过导航到所需单个模板，可在开发人员门户中查看实时的默认模板。 如需详细了解如何使用模板，请参阅[如何使用模板自定义 API 管理开发人员门户](./api-management-developer-portal-templates.md)。  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="product-list"></a><a name="ProductList"></a> 产品列表  
 **产品列表** 模板可用于自定义开发人员门户中产品列表页的正文。  
  
 ![产品列表](./media/api-management-product-templates/APIM_ProductsListTemplatePage.png "APIM_ProductsListTemplatePage")  
  
### <a name="default-template"></a>默认模板  
  
```xml  
<search-control></search-control>  
<div class="row">  
    <div class="col-md-9">  
        <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
    </div>  
</div>  
<div class="row">  
    <div class="col-md-12">  
    {% if products.size > 0 %}  
    <ul class="list-unstyled">  
    {% for product in products %}  
        <li>  
            <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>  
            {{product.description}}  
        </li>     
    {% endfor %}  
    </ul>  
    <paging-control></paging-control>  
    {% else %}  
    {% localized "CommonResources|NoItemsToDisplay" %}  
    {% endif %}  
    </div>  
</div>  
```  
  
### <a name="controls"></a>控制  
 `Product list` 模板可使用以下[页面控件](api-management-page-controls.md)。  
  
-   [paging-control](api-management-page-controls.md#paging-control)  
  
-   [search-control](api-management-page-controls.md#search-control)  
  
### <a name="data-model"></a>数据模型  
  
|属性|类型|说明|  
|--------------|----------|-----------------|  
|Paging|[分页](api-management-template-data-model-reference.md#Paging)实体。|产品集合的分页信息。|  
|Filtering|[筛选](api-management-template-data-model-reference.md#Filtering)实体。|产品列表页的筛选信息。|  
|Products|[产品](api-management-template-data-model-reference.md#Product)实体的集合。|对当前用户可见的产品。|  
  
### <a name="sample-template-data"></a>示例模板数据  
  
```json  
{  
    "Paging": {  
        "Page": 1,  
        "PageSize": 10,  
        "TotalItemCount": 2,  
        "ShowAll": false,  
        "PageCount": 1  
    },  
    "Filtering": {  
        "Pattern": null,  
        "Placeholder": "Search products"  
    },  
    "Products": [  
        {  
            "Id": "56f9445ffaf7560049060001",  
            "Title": "Starter",  
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
            "Terms": "",  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        },  
        {  
            "Id": "56f9445ffaf7560049060002",  
            "Title": "Unlimited",  
            "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",  
            "Terms": null,  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        }  
    ]  
}  
```  
  
##  <a name="product"></a><a name="Product"></a> Product  
 **产品** 模板可用于自定义开发人员门户中产品页的正文。  
  
 ![开发人员门户产品页](./media/api-management-product-templates/APIM_ProductPage.png "APIM_ProductPage")  
  
### <a name="default-template"></a>默认模板  
  
```xml  
<h2>{{Product.Title}}</h2>  
<p>{{Product.Description}}</p>  
  
{% assign replaceString0 = '{0}' %}  
  
{% if Limits and Limits.size > 0 %}  
<h3>{% localized "ProductDetailsStrings|WebProductsUsageLimitsHeader"%}</h3>  
<ul>  
  {% for limit in Limits %}  
  <li>{{limit.DisplayName}}</li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if apis.size > 0 %}  
<p>  
  <b>  
    {% if apis.size == 1 %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockSingleApisCount" %}{% endcapture %}  
    {% else %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockMultipleApisCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture apisCount %}{{apis.size}}{% endcapture %}  
    {{ apisCountText | replace : replaceString0, apisCount }}  
  </b>  
</p>  
  
<ul>  
  {% for api in Apis %}  
  <li>  
    <a href="/docs/services/{{api.Id}}">{{api.Name}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if subscriptions.size > 0 %}  
<p>  
  <b>  
    {% if subscriptions.size == 1 %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockSingleSubscriptionsCount" %}{% endcapture %}  
    {% else %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockMultipleSubscriptionsCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture subscriptionsCount %}{{subscriptions.size}}{% endcapture %}  
    {{ subscriptionsCountText | replace : replaceString0, subscriptionsCount }}  
  </b>  
</p>  
  
<ul>  
  {% for subscription in subscriptions %}  
  <li>  
    <a href="/developer#{{subscription.Id}}">{{subscription.DisplayName}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
{% if CannotAddBecauseSubscriptionNumberLimitReached %}  
<b>{% localized "ProductDetailsStrings|TextblockSubscriptionLimitReached" %}</b>  
{% elsif CannotAddBecauseMultipleSubscriptionsNotAllowed == false %}  
<subscribe-button></subscribe-button>  
{% endif %}  
```  
  
### <a name="controls"></a>控制  
 `Product list` 模板可使用以下[页面控件](api-management-page-controls.md)。  
  
-   [subscribe-button](api-management-page-controls.md#subscribe-button)  
  
### <a name="data-model"></a>数据模型  
  
|属性|类型|说明|  
|--------------|----------|-----------------|  
|Products|[Product](api-management-template-data-model-reference.md#Product)|指定的产品。|  
|IsDeveloperSubscribed|boolean|当前用户是否订阅了此产品。|  
|SubscriptionState|数字|订阅的状态。 可能的状态包括：<br /><br /> -   `0 - suspended` – 订阅被阻止，订阅服务器无法调用产品的任何 API。<br />-   `1 - active` – 订阅处于活动状态。<br />-   `2 - expired` – 订阅已达到其到期日期，因此已停用。<br />-   `3 - submitted` – 开发人员已提交订阅请求，但管理员尚未批准或拒绝该请求。<br />-   `4 - rejected` – 管理员已拒绝订阅请求。<br />-   `5 - cancelled` – 开发人员或管理员已取消订阅。|  
|限制|array|此属性已弃用，不应使用。|  
|DelegatedSubscriptionEnabled|boolean|是否为此订阅启用了[委派](./api-management-howto-setup-delegation.md)。|  
|DelegatedSubscriptionUrl|字符串|委派的订阅 URL（如果启用了委派）。|  
|IsAgreed|boolean|如果产品有条款，当前用户是否已同意这些条款。|  
|Subscriptions|[订阅摘要](api-management-template-data-model-reference.md#SubscriptionSummary)实体的集合。|对产品的订阅。|  
|Apis|[API](api-management-template-data-model-reference.md#API) 实体的集合。|此产品中的 API。|  
|CannotAddBecauseSubscriptionNumberLimitReached|boolean|当前用户是否符合订阅此产品的条件（考虑到订阅限制）。|  
|CannotAddBecauseMultipleSubscriptionsNotAllowed|boolean|当前用户是否符合订阅此产品的条件（考虑到是否允许多个订阅）。|  
  
### <a name="sample-template-data"></a>示例模板数据  
  
```json  
{  
    "Product": {  
        "Id": "56f9445ffaf7560049060001",  
        "Title": "Starter",  
        "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
        "Terms": "",  
        "ProductState": 1,  
        "AllowMultipleSubscriptions": false,  
        "MultipleSubscriptionsCount": 1  
    },  
    "IsDeveloperSubscribed": true,  
    "SubscriptionState": 1,  
    "Limits": [],  
    "DelegatedSubscriptionEnabled": false,  
    "DelegatedSubscriptionUrl": null,  
    "IsAgreed": false,  
    "Subscriptions": [  
        {  
            "Id": "56f9445ffaf7560049070001",  
            "DisplayName": "Starter  (default)"  
        }  
    ],  
    "Apis": [  
        {  
            "id": "56f9445ffaf7560049040001",  
            "name": "Echo API",  
            "description": null,  
            "serviceUrl": "http://echoapi.cloudapp.net/api",  
            "path": "echo",  
            "protocols": [  
                2  
            ],  
            "authenticationSettings": null,  
            "subscriptionKeyParameterNames": null  
        }  
    ],  
    "CannotAddBecauseSubscriptionNumberLimitReached": false,  
    "CannotAddBecauseMultipleSubscriptionsNotAllowed": true  
}  
```

## <a name="next-steps"></a>后续步骤
如需详细了解如何使用模板，请参阅[如何使用模板自定义 API 管理开发人员门户](api-management-developer-portal-templates.md)。
