---
title: 在 Azure 医疗保健 API 中部署 FHIR 服务
description: 本文指导用户如何在 Azure 门户 中部署 FHIR 服务。
author: stevewohl
ms.service: healthcare-apis
ms.topic: quickstart
ms.date: 09/10/2021
ms.author: ginle
ms.openlocfilehash: 5f65355ab77547c8587cd228378868b56b193c9b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124782518"
---
# <a name="deploy-a-fhir-service-within-azure-healthcare-apis---using-portal"></a>在 Azure 医疗保健 API 中部署 FHIR 服务 - 使用门户

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

本文介绍了如何在 Azure 医疗保健 API 中部署 FHIR 服务 (使用) FHIR 服务Azure 门户。

## <a name="prerequisite"></a>先决条件

在开始使用之前，应该已经部署了 Azure 医疗保健 API。 有关部署 Azure 医疗保健 API 的信息，请参阅部署工作区[中的 Azure 门户。](../healthcare-apis-quickstart.md)

## <a name="create-a-new-fhir-service"></a>创建新的 FHIR 服务

在工作区中，选择"**部署 FHIR 服务"。**

[![部署 FHIR 服务 ](media/fhir-service/deploy-fhir-services.png) ](media/fhir-service/deploy-fhir-services.png#lightbox)

选择 **"+ 添加 FHIR 服务"。**

[![添加 FHIR 服务 ](media/fhir-service/add-fhir-service.png) ](media/fhir-service/add-fhir-service.png#lightbox)

输入 **FHIR 服务的** "帐户名称"。 选择 **STU3** (**R4**) **FHIR** 版本，然后选择"**查看 + 创建"。**

[![创建 FHIR 服务 ](media/fhir-service/create-fhir-service.png) ](media/fhir-service/create-fhir-service.png#lightbox)

选择" **创建"** 之前，请查看 FHIR 服务的 **"** 基本信息"和 **"** 其他"设置的属性。 如果需要返回并进行更改，请选择"上一 **步"。** 确认显示 **"验证成功** "消息。 

[![验证 FHIR 服务 ](media/fhir-service/validation-fhir-service.png) ](media/fhir-service/validation-fhir-service.png#lightbox)

## <a name="additional-settings-optional"></a>其他设置（可选）

还可以选择"其他 **设置"** 选项卡以查看身份验证设置。 Azure API for FHIR 的默认配置是 **使用 Azure RBAC 来分配数据平面角色**。 在此模式下配置时，FHIR 服务的"颁发机构"将设置为Azure Active Directory租户。

[![其他设置 FHIR 服务 ](media/fhir-service/additional-settings-tab.png) ](media/fhir-service/additional-settings-tab.png#lightbox)

请注意，用于输入"允许 **的对象 ID"** 的框灰显。这是因为在这种情况下，我们使用 Azure RBAC 来配置角色分配。

如果要将 FHIR 服务配置为使用外部或辅助 Azure Active Directory 租户，则可以更改该颁发机构并为应该被允许访问服务器的用户和组输入对象 ID。

## <a name="fetch-fhir-api-capability-statement"></a>提取 FHIR API 功能语句

若要验证是否预配了新的 FHIR API 帐户，请浏览到 提取功能语句 `https://<WORKSPACE NAME>-<ACCOUNT-NAME>.fhir.azurehealthcareapis.com/metadata` 。

## <a name="next-steps"></a>后续步骤

>[!div class="nextstepaction"]
>[使用 Postman 访问 FHIR 服务](../use-postman.md)

