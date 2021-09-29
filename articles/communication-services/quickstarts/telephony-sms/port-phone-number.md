---
title: 快速入门 - 将电话号码移植到 Azure 通信服务中
description: 了解如何将电话号码移植到通信服务资源中
author: probableprime
manager: mikben
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: pstn
ms.custom: references_regions
ms.openlocfilehash: 611f6b50d1d7ab6cd2578ca7d4e56a97f25b6f51
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128677010"
---
# <a name="quickstart-port-a-phone-number"></a>快速入门：移植电话号码

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

通过将电话号码移植到 Azure 通信服务资源，开始使用 Azure 通信服务。 美国境内的免费电话和地区号均可移植。 有关电话号码类型的详细信息，请访问[电话号码概念文档](../../concepts/telephony-sms/plan-solution.md)。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [活动的通信服务资源。](../create-communication-resource.md)

## <a name="gather-your-azure-resource-details"></a>收集 Azure 资源详细信息

在发起移植请求之前，请导航到 Azure 门户并选择通信服务资源。 在已选择“`Overview`”窗格的情况下，单击右上角的“`JSON View`”链接：

:::image type="content" source="./media/number-port/json-view.png" alt-text="屏幕截图，显示选择 JSON 视图。":::

记录资源的“Azure ID”和“不可变资源 ID”：

:::image type="content" source="./media/number-port/json-properties.png" alt-text="屏幕截图，显示选择 JSON 属性。":::

## <a name="initiate-the-port-request"></a>发起移植请求

美国境内的免费电话和地区号均可移植。 使用以下表单之一提交移植请求：

- 对于免费电话：[免费电话移植请求](https://aka.ms/acs-port-form-tollfree)
- 对于美国的地区号：[地区号移植请求](https://aka.ms/acs-port-form-geographic)

完成后，请将已填写的移植请求表单发送到 acsporting@microsoft.com。 请确保电子邮件主题行以“ACS Port-In Request”开头。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何：

> [!div class="checklist"]
> * 获取通信服务资源元数据
> * 提交移植电话号码的请求

> [!div class="nextstepaction"]
> [发送短信](../telephony-sms/send.md)
> [呼叫入门](../voice-video-calling/getting-started-with-calling.md)
