---
title: 在 Azure API for FHIR 中配置跨域资源共享
description: 本文介绍如何在 Azure API for FHIR 中配置跨域资源共享。
author: matjazl
ms.author: matjazl
ms.date: 3/11/2019
ms.topic: reference
ms.service: healthcare-apis
ms.subservice: fhir
ms.openlocfilehash: dc4c034b0821f1fe5ecb940591fca77d61edc3ce
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017790"
---
# <a name="configure-cross-origin-resource-sharing-in-azure-api-for-fhir"></a>在 Azure API for FHIR 中配置跨域资源共享

适用于快速医疗保健互操作性资源的 Azure API (FHIR) 支持 [ (CORS) 的跨域资源共享 ](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing)。 CORS 允许你配置设置，以便一个域中的应用程序 (源) 可以从其他域（称为跨域请求）访问资源。

CORS 通常用于单页面应用程序，该应用程序必须将 RESTful API 调用到不同的域。

若要在用于 FHIR 的 Azure API 中配置 CORS 设置，请指定以下设置：

- **源 (访问控制-允许)**。 允许向 Azure API for FHIR 提供跨域请求的域列表。 必须在单独的行中输入每个域 (源) 。 您可以输入一个星号 ( * ) 以允许来自任何域的调用，但我们不建议这样做，因为这会带来安全风险。

- **标头 (访问控制-允许-标头)**。 源请求将包含的标头的列表。 若要允许所有标头，请输入星号 ( * ) 。

- **(访问控制-允许) 方法的方法**。 在 API 调用中 (PUT、GET、POST 等) 的允许方法。 对于所有方法，选择 "全 **选** "。

- **最大年龄 (访问控制-最大年龄)**。 用于缓存 "访问控制-允许-标头" 和 "访问控制-允许"-方法的预检请求结果的值（以秒为单位）。

- **允许凭据 (访问控制-允许)**。 CORS 请求通常不包含 cookie 来阻止 [跨站点请求伪造 (CSRF) ](https://en.wikipedia.org/wiki/Cross-site_request_forgery) 攻击。 如果选择此设置，则可以发出请求以包括 cookie 等凭据。 如果已使用星号 ( * ) 设置了源，则无法配置此设置。

![ (CORS) 设置的跨域资源共享](media/cors/cors.png)

>[!NOTE]
>不能为不同的域来源指定不同的设置。 所有设置 (**标头**、 **方法**、 **最大期限** 和 **允许凭据**) 适用于在 "源" 设置中指定的所有来源。

## <a name="next-steps"></a>后续步骤

本文介绍了如何在 Azure API for FHIR 中配置跨域共享。 接下来，为 FHIR 部署完全托管的 Azure API：
 
>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-portal-quickstart.md)