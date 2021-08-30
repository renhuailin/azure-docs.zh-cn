---
title: 语音识别服务静态数据的加密
titleSuffix: Azure Cognitive Services
description: Microsoft 提供了 Microsoft 托管的加密密钥，还可让你使用自己的密钥（称为客户管理的密钥 (CMK)）管理你的认知服务订阅。 本文介绍适用于语音服务的静态数据加密。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/14/2021
ms.author: egeaney
ms.openlocfilehash: cdf8276904fda5098b3192779e0372b4a1bcc9d2
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113766614"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>语音识别服务静态数据的加密

语音识别服务在将数据保存到云时会自动加密数据。 语音识别服务加密可以保护数据，并帮助组织履行在安全性与合规性方面做出的承诺。

## <a name="about-cognitive-services-encryption"></a>关于认知服务加密

数据使用符合 [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) 的 [256 位 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 加密方法进行加密和解密。 加密和解密都是透明的，这意味着将替你管理加密和访问。 你的数据默认情况下就是安全的，你无需修改代码或应用程序，即可利用加密。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

使用自定义语音识别和自定义语音时，语音识别服务可能会将以下数据存储在云中：  

* 语音识别跟踪数据 - 仅当你为自定义终结点启用了跟踪时才存储
* 已上传的训练和测试数据

默认情况下，你的数据存储在 Microsoft 的存储中，你的订阅使用 Microsoft 托管的加密密钥。 你还可以选择准备你自己的存储帐户。 对应用商店的访问是由托管标识管理的，语音识别服务无法直接访问你自己的数据，例如语音识别跟踪数据、自定义训练数据和自定义模型。

有关托管标识的详细信息，请参阅[什么是托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

同时，使用自定义命令时，可以使用自己的加密密钥来管理订阅。 客户管理的密钥 (CMK)（也称为创建自己的密钥，BYOK）在创建、轮换、禁用和撤销访问控制方面可提供更大的灵活性。 此外，你还可以审核用于保护数据的加密密钥。 有关自定义命令和 CMK 的详细信息，请参阅[静态数据的自定义命令加密](custom-commands-encryption-of-data-at-rest.md)。

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>自带存储 (BYOS) 以进行自定义和日志记录

若要请求访问自带存储，请填写并提交 [语音服务 - 自带存储 (BYOS) 请求表单](https://aka.ms/cogsvc-cmk)。 获得批准后，需要创建自己的存储帐户来存储进行自定义和日志记录所需的数据。 添加存储帐户时，语音服务资源将启用系统分配的托管标识。

> [!IMPORTANT]
> 用于创建启用了 BYOS 功能的语音资源的用户帐户应被分配 [Azure 订阅范围内的“所有者”角色](../../cost-management-billing/manage/add-change-subscription-administrator.md#to-assign-a-user-as-an-administrator)。 否则，在资源预配期间将收到授权错误。

启用系统分配的托管标识后，此资源将注册到 Azure Active Directory (AAD)。 注册后，将向托管标识授予存储帐户的访问权限。 有关托管标识的详细信息，请参阅[什么是托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

> [!IMPORTANT]
> 如果禁用系统分配的托管标识，则将删除对存储帐户的访问权限。 这将导致需要访问存储帐户的部分语音服务停止工作。  

语音服务目前不支持客户密码箱。 但是，可以使用 BYOS 来存储客户数据，这样能够实现与[客户密码箱](../../security/fundamentals/customer-lockbox-overview.md)类似的数据控制。 请记住，语音服务数据仍会保留，并会在创建语音资源的区域中进行处理。 这适用于任何静态数据和传输中的数据。 在使用自定义功能（如自定义语音识别和自定义语音）时，所有的客户数据都会在你的 BYOS（如果使用了）和语音服务资源所在的同一区域中传输、存储和处理。

> [!IMPORTANT]
> Microsoft 不会使用客户数据来改进其语音模型。 此外，如果禁用了终结点日志记录，并且未使用任何自定义，则不会存储任何客户数据。 

## <a name="next-steps"></a>后续步骤

* [服务 - 自带存储 (BYOS) 请求表单](https://aka.ms/cogsvc-cmk)
* [什么是托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。
