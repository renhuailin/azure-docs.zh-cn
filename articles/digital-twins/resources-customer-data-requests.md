---
title: Azure 数字孪生的客户数据请求功能
titleSuffix: Azure Digital Twins
description: 本文介绍了在 Azure 数字孪生中导出和删除个人数据的过程。
author: baanders
ms.author: baanders
ms.date: 9/14/2021
ms.topic: conceptual
ms.service: digital-twins
services: digital-twins
ms.openlocfilehash: ef68e552d57df0fc202b9d75ae76065e3119ccff
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128642788"
---
# <a name="azure-digital-twins-customer-data-request-features"></a>Azure 数字孪生客户数据请求功能

Azure 数字孪生是一个开发人员平台，用于创建业务环境的安全的数字表示形式。 表示形式由用户选择的数据源中的实时状态数据进行驱动。

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Azure 数字孪生中称为“数字孪生体”的数字表示形式表示真实环境中的实体，与标识符相关联。 Microsoft 不保留任何信息，并且无法访问将会允许标识符与用户关联的数据。 

Azure 数字孪生中的许多数字孪生体不会直接表示个人实体，所表示的典型对象可能会是办公会议室或工厂车间。 然而，用户可能会认为有些实体是个人身份，并且可能会按照他们的意愿来维护自己的资产或将数字孪生体与个人联系起来的库存跟踪方法。 Azure 数字孪生会将所有与数字孪生体关联的数据视为个人数据进行管理和存储。

若要查看、导出和删除可能会在数据主体请求中引用的个人数据，Azure 数字孪生管理员可使用 [Azure 门户](https://portal.azure.com/)（针对用户和角色）或 [Azure 数字孪生 REST API](/rest/api/azure-digitaltwins/)（针对数字孪生体）。 Azure 门户和 REST API 为用户提供了不同的方法来处理此类数据主体请求。

## <a name="identifying-customer-data"></a>标识客户数据

Azure 数字孪生会将个人数据视为与该数据的管理员和用户关联的数据。 

Azure 数字孪生会存储具有环境访问权限的用户的 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) 对象 ID。 Azure 门户中的 Azure 数字孪生会显示用户电子邮件地址，但这些电子邮件地址不会存储在 Azure 数字孪生中。 可以使用 Azure Active Directory 对象 ID 在 Azure Active Directory 中动态查找这些电子邮件地址。

## <a name="deleting-customer-data"></a>删除客户数据

Azure 数字孪生管理员可以使用 Azure 门户来删除与用户相关的数据。 也可以使用 Azure 数字孪生 REST API 在单个数字孪生体上执行删除操作。 若要详细了解可用的 API，请参阅 [Azure 数字孪生 REST API 文档](/rest/api/azure-digitaltwins/)。

## <a name="exporting-customer-data"></a>导出客户数据

Azure 数字孪生存储与数字孪生体相关的数据。 用户可以通过 REST API 来检索和查看该数据，以及通过复制和粘贴来导出该数据。 

可以从 Azure 门户选择、复制和粘贴客户数据，包括用户角色和角色分配。 

## <a name="links-to-additional-documentation"></a>其他文档的链接

有关 Azure 数字孪生服务 API 的完整列表，请参阅 [Azure 数字孪生 REST API 文档](/rest/api/azure-digitaltwins/)。