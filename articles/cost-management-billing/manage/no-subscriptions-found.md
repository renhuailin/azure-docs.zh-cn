---
title: “找不到任何订阅”错误 – Azure 门户登录
description: 针对 Azure 门户登录期间发生“找不到任何订阅”错误的问题提供了解决方案。
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 05/13/2021
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 72a8170b6dffb79d8828d60389728e0bf4d42a41
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110458734"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal"></a>Azure 门户发生“找不到任何订阅”登录错误

尝试登录 [Azure 门户](https://portal.azure.com/)时，可能会出现“找不到任何订阅”错误消息。 本文提供针对此问题的解决方案。

## <a name="symptom"></a>症状

尝试登录 [Azure 门户](https://portal.azure.com/)时，出现以下错误消息：“找不到任何订阅”。

## <a name="cause"></a>原因

如果选择了错误的目录，或者帐户没有足够的权限，会出现此问题。

## <a name="solution"></a>解决方案

### <a name="scenario-error-message-is-received-in-the-azure-portal"></a>场景：在 [Azure 门户](https://portal.azure.com)中收到错误消息

解决此问题：

* 通过单击右上角的帐户确保已选择正确的 Azure 目录。

  ![选择 Azure 门户右上角的目录](./media/no-subscriptions-found/directory-switch.png)
* 如果已选择正确的 Azure 目录，但仍收到错误消息，[请将所有者角色分配给帐户](../../role-based-access-control/role-assignments-portal.md)。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。
