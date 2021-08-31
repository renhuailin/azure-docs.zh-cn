---
title: 在 Azure API for FHIR 中配置跨域资源共享
description: 本文介绍如何在 Azure API for FHIR 中配置跨域资源共享。
author: matjazl
ms.author: zxue
ms.date: 3/11/2019
ms.topic: reference
ms.service: healthcare-apis
ms.subservice: fhir
ms.openlocfilehash: a98687854a82c7dc17d2765a8c71467609435fa5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778247"
---
# <a name="configure-cross-origin-resource-sharing-in-azure-api-for-fhir"></a>在 Azure API for FHIR 中配置跨域资源共享

适用于快速医疗保健互操作性资源 (FHIR) 的 Azure API 支持[跨域资源共享 (CORS)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing)。 通过 CORS，你可以配置设置，使来自一个域（源）的应用程序可以访问来自另一个域的资源（称为跨域请求）。

CORS 通常用于必须对不同的域调用 RESTful API 的单页应用。

## <a name="configure-cors-settings"></a>配置 CORS 设置

若要在 Azure API for FHIR 中配置 CORS 设置，请指定以下设置：

- 源 (Access-Control-Allow-Origin)。 可以向 Azure API for FHIR 发出跨域请求的域的列表。 必须在单独的行中输入每个域（源）。 可以输入一个星号 (*) 以允许来自任何域的调用，但我们不建议这样做，因为这会带来安全风险。

- 标头 (Access-Control-Allow-Headers)。 源请求将包含的标头的列表。 若要允许所有标头，请输入一个星号 (*)。

- 方法 (Access-Control-Allow-Methods)。 API 调用中允许的方法（PUT、GET、POST 等）。 选择“全选”则允许所有方法。

- 最长时间 (Access-Control-Max-Age)。 用于缓存 Access-Control-Allow-Headers 和 Access-Control-Allow-Methods 的预检请求结果的值（以秒为单位）。

- 允许凭据 (Access-Control-Allow-Credentials)。 CORS 请求通常不包含 Cookie，以防止[跨网站请求伪造 (CSRF)](https://en.wikipedia.org/wiki/Cross-site_request_forgery) 攻击。 如果选择此设置，则可以发出请求以包括 Cookie 等凭据。 如果你已使用星号 (*) 设置了源，则无法配置此设置。

![跨域资源共享 (CORS) 设置](media/cors/cors.png)

>[!NOTE]
>不能为不同的域源指定不同的设置。 所有设置（“标头”、“方法”、“最长时间”和“允许凭据”）都适用于在“源”设置中指定的所有源。

## <a name="next-steps"></a>后续步骤

在本文中，你了解了如何在 Azure API for FHIR 中配置跨域共享。 接下来部署一个完全托管的 Azure API for FHIR：
 
>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-portal-quickstart.md)