---
title: Logz.io 故障排除 - Azure 合作伙伴解决方案
description: 了解如何排查 Azure 与 Logz.io 的集成问题。
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 08/17/2021
ms.author: tomfitz
ms.openlocfilehash: 5b1d1eb1a59e40286103d63508c98bcc3ba37b8c
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122428456"
---
# <a name="troubleshoot-logzio-integration-with-azure"></a>排查 Azure 与 Logz.io 的集成问题

本文介绍如何排查 Azure 与 Logz.io 的集成问题。

## <a name="owner-role-needed-to-create-resource"></a>需要所有者角色才能创建资源

要设置 Logz.io，必须在 Azure 订阅中分配有[所有者角色](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)。 开始此集成之前，请[检查你的访问权限](../../role-based-access-control/check-access.md)。

## <a name="single-sign-on-errors"></a>单一登录错误

SSO 设置期间可能会发生错误。 有关如何设置单一登录 (SSO) 的详细信息，请参阅[设置 Logz.io 单一登录](setup-sso.md)。

### <a name="unable-to-save-single-sign-on-settings"></a>无法保存单一登录设置

此错误表示有另一个企业应用程序在使用 Logz.io 安全断言标记语言 (SAML) 标识符。 若要查找使用它的应用程序，请在“基本 SAML 配置”部分选择“编辑”。

若要解决此问题，请禁用使用 SAML 的企业应用程序，或使用其他企业应用程序来设置 Logz.io 的 SAML SSO。 确保应用程序具有所需的设置。

### <a name="application-not-shown-in-single-sign-on"></a>应用程序没有在单一登录中显示

尝试使用应用程序 ID 进行搜索。 如果未显示结果，请检查应用程序的 SAML 设置。 网格只显示具有正确 SAML 设置的现有应用程序。 “标识符”和“回复 URL”必须完全如下图所示。

你可以在空白文本框中为“标识符”和“回复 URL”添加新值。

使用以下模式来添加新值：

- 标识符：`urn:auth0:logzio:<Application ID>`
- **回复 URL**：`https://logzio.auth0.com/login/callback?connection=<Application ID>`

:::image type="content" source="./media/troubleshoot/basic-saml-config.png" alt-text="基本 SAML 配置设置。":::

## <a name="logs-not-being-sent-to-logzio"></a>日志未发送到 Logz.io

只有在 [Azure Monitor 资源日志类别](../../azure-monitor/essentials/resource-logs-categories.md)中列出的资源才会向 Logz.io 发送日志。

验证资源是否在将日志发送到 Logz.io：

1. 转到 [Azure 诊断设置](../../azure-monitor/essentials/diagnostic-settings.md)获取特定资源。
1. 验证是否有 Logz.io 诊断设置。

:::image type="content" source="./media/troubleshoot/diagnostics.png" alt-text="诊断设置。":::

## <a name="limit-reached-in-monitored-resources"></a>监视的资源达到限制

Azure Monitor 诊断在单个资源或订阅上最多支持五个诊断设置。 当达到该限制时，资源将在“监视的资源”中显示“达到限制”。 不能添加 Logz.io 监视。

:::image type="content" source="./media/troubleshoot/limit-monitored-resources.png" alt-text="监视的资源达到限制。":::

## <a name="vm-extension-installation-failed"></a>VM 扩展安装失败

虚拟机 (VM) 只能由单个 Logz.io 帐户（主帐户或子帐户）来监视。 如果你尝试在已由其他帐户监视的 VM 上安装代理，会看到以下错误：

:::image type="content" source="./media/troubleshoot/vm-agent-fail.png" alt-text="虚拟机代理安装失败。":::

## <a name="purchase-errors"></a>购买错误

购买失败，因为未将有效信用卡连接到 Azure 订阅。 或付款方式没有与订阅关联。

解决购买错误：

- 使用其他 Azure 订阅。
- 添加或更新订阅的信用卡或付款方式。 有关详细信息，请参阅[为 Azure 添加或更新信用卡](../../cost-management-billing/manage/change-credit-card.md)。

通过选择“操作详细信息”，可以从资源的“部署”页查看错误输出。

```json
{
  "status": "Failed",
  "error": {
    "code": "BadRequest",
    "message": "{\"message\":\"Purchase has failed because we couldn't find a valid credit card nor
               a payment method associated with your Azure subscription. Please use a different
               Azure subscription or add\\\\update current credit card or payment method for this
               subscription and retry.\",\"code\":\"BadRequest\"}"
  }
}
```

## <a name="next-steps"></a>后续步骤

- 了解如何[管理](manage.md) Logz.io 集成。
- 若要详细了解 SSO，请参阅[设置 Logz.io 单一登录](setup-sso.md)。
