---
title: 在 FHIR 服务中配置跨域资源共享
description: 本文介绍如何在 FHIR 服务中配置跨域资源共享
author: matjazl
ms.author: zxue
ms.date: 08/03/2021
ms.topic: reference
ms.service: healthcare-apis
ms.subservice: fhir
ms.openlocfilehash: e322b1fb848f9156b1d00d2d1ae051ef62536c4a
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778879"
---
# <a name="configure-cross-origin-resource-sharing-in-fhir-service"></a>在 FHIR 服务中配置跨域资源共享

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

Azure 医疗保健 Api (特此称为 FHIR service) 支持 [跨域资源共享 (CORS) ](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing)中的 FHIR 服务。 通过 CORS，你可以配置设置，使来自一个域（源）的应用程序可以访问来自另一个域的资源（称为跨域请求）。

CORS 通常用于必须对不同的域调用 RESTful API 的单页应用。

若要配置 FHIR 服务中的 CORS 设置，请指定以下设置：

- 源 (Access-Control-Allow-Origin)。 允许向 FHIR 服务发出跨源请求的域的列表。 必须在单独的行中输入每个域（源）。 可以输入一个星号 (*) 以允许来自任何域的调用，但我们不建议这样做，因为这会带来安全风险。

- 标头 (Access-Control-Allow-Headers)。 源请求将包含的标头的列表。 若要允许所有标头，请输入一个星号 (*)。

- 方法 (Access-Control-Allow-Methods)。 API 调用中允许的方法（PUT、GET、POST 等）。 选择“全选”则允许所有方法。

- 最长时间 (Access-Control-Max-Age)。 用于缓存 Access-Control-Allow-Headers 和 Access-Control-Allow-Methods 的预检请求结果的值（以秒为单位）。

- 允许凭据 (Access-Control-Allow-Credentials)。 CORS 请求通常不包含 Cookie，以防止[跨网站请求伪造 (CSRF)](https://en.wikipedia.org/wiki/Cross-site_request_forgery) 攻击。 如果选择此设置，则可以发出请求以包括 Cookie 等凭据。 如果你已使用星号 (*) 设置了源，则无法配置此设置。

![跨域资源共享 (CORS) 设置](media/cors/cors.png)

>[!NOTE]
>不能为不同的域源指定不同的设置。 所有设置（“标头”、“方法”、“最长时间”和“允许凭据”）都适用于在“源”设置中指定的所有源。