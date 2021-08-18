---
title: 云合作伙伴门户 API 参考 - Microsoft 商业市场
description: 市场 API 操作的说明、要使用的先决条件和列表。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 5813b08a14a95a8b7bbb51b3d6593fe374a83ba6
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112282036"
---
# <a name="cloud-partner-portal-api-reference"></a>云合作伙伴门户 API 参考

> [!NOTE]
> 云合作伙伴门户 API 已与合作伙伴中心集成，并将继续在其中工作。 本次转换带来了少量更改。 请查看本文档中列出的 [CPP API 更改](#changes-to-cpp-apis-after-the-migration-to-partner-center)，以确保在转换到合作伙伴中心后，代码能继续工作。 CPP API 应仅用于在转换到合作伙伴中心之前已经集成的现有产品；新产品应使用合作伙伴中心提交 API。

云合作伙伴门户 REST API允许以编程方式检索和操作工作负载、产品/服务和发布者配置文件。 API 使用 Azure 基于角色的访问控制 (Azure RBAC)，在处理时强制实施正确的权限。

本参考提供云合作伙伴门户 REST API 的技术详细信息。 本文档中的有效负载示例仅供参考，随着新功能的添加可能会发生变化。

## <a name="prerequisites-and-considerations"></a>先决条件和注意事项

在使用 API 之前，应该查看：

- [先决条件](./cloud-partner-portal-api-prerequisites.md)一文，了解如何向帐户添加服务主体，并获取 Azure Active Directory (Azure AD) 访问令牌以进行身份验证。
- 有两种[并发控制](./cloud-partner-portal-api-concurrency-control.md)策略可用于调用这些 API。
- 其他 API [注意事项](./cloud-partner-portal-api-considerations.md)，如版本控制和错误处理。

## <a name="changes-to-cpp-apis-after-the-migration-to-partner-center"></a>迁移到合作伙伴中心后，CPP API 发生的更改

| **API** | **更改描述** | **影响** |
| ------- | ---------------------- | ---------- |
| POST 发布、上线、取消 | 对于已迁移的产品/服务，响应标头将采用不同的格式，但将以相同的方式继续工作，这表示有一个相对路径用于检索操作状态。 | 在发送某产品/服务的任何相应 POST 请求时，位置标头将采用以下两种格式之一，具体取决于产品/服务的迁移状态：<ul><li>未迁移的产品/服务<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>已迁移的产品/服务<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| GET 操作 | 对于之前支持在响应中使用“notification-email”字段的产品/服务类型，此字段将被弃用，且将不对已迁移的产品/服务返回此字段。 | 对于已迁移的产品/服务，我们将不再向请求中指定的一列电子邮件地址发送通知。 相反，API 服务将与 合作伙伴中心内的通知电子邮件进程保持一致来发送电子邮件。 具体而言，通知将发送至合作伙伴中心内在你的帐户设置的卖家联系人信息部分中设定的电子邮件地址，告知你操作进度。<br><br>请在合作伙伴中心的[帐户设置](https://go.microsoft.com/fwlink/?linkid=2165291)中查看卖家联系人信息部分中设定的电子邮件地址，以确保提供了正确的电子邮件地址来接收通知。 |

## <a name="common-tasks"></a>常见任务

本参考详细介绍了用于执行以下常见任务的 API。

### <a name="offers"></a>产品

- [检索所有产品/服务](./cloud-partner-portal-api-retrieve-offers.md)
- [检索特定产品/服务](./cloud-partner-portal-api-retrieve-specific-offer.md)
- [检索产品/服务状态](./cloud-partner-portal-api-retrieve-offer-status.md)
- [创建产品/服务](./cloud-partner-portal-api-creating-offer.md)
- [发布产品/服务](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Operations

- [检索操作](./cloud-partner-portal-api-retrieve-operations.md)
- [取消操作](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>发布应用

- [推出](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>其他任务

- [设置虚拟机套餐的定价](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>疑难解答

- [排查身份验证错误](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
