---
title: Datadog 故障排除 - Azure 合作伙伴解决方案
description: 本文介绍如何在 Azure 上对 Datadog 进行故障排除。
ms.service: partner-services
ms.topic: conceptual
ms.date: 05/28/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 1c23851dc76cb0e012ad8796d361e5c293bc8d99
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110655146"
---
# <a name="fix-common-errors-for-datadog-on-azure"></a>修复 Azure 上的 Datadog 的常见错误

本文档包含有关排查使用 Datadog 的解决方案问题的信息。

## <a name="purchase-errors"></a>购买错误

* 购买失败，因为该 Azure 订阅未连接有效信用卡，或该订阅未关联付款方式。

  使用其他 Azure 订阅。 或者，为订阅添加或更新信用卡或付款方式。 有关详细信息，请参阅[更新信用卡和付款方式](../../cost-management-billing/manage/change-credit-card.md)。

* EA 订阅不允许在市场中购买。

  使用其他订阅。 或者，检查你的 EA 订阅是否启用了市场购买。 有关详细信息，请参阅[启用市场购买](../../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases)。 如果这些选项不能解决问题，请联系 [Datadog 支持](https://www.datadoghq.com/support)。

## <a name="unable-to-create-datadog-resource"></a>无法创建 Datadog 资源

若要设置 Azure Datadog 集成，必须在 Azure 订阅上拥有“所有者”访问权限。 在开始设置之前，请确保拥有适当的访问权限。

## <a name="single-sign-on-errors"></a>单一登录错误

无法保存单一登录设置 - 如果有另一个使用 Datadog SAML 标识符的企业应用程序，则会发生此错误。 若要查找使用它的应用，请在“基本 SAML 配置”部分选择“编辑”。

若要解决此问题，请禁用其他应用，或使用其他应用作为企业应用在 Datadog 中设置 SAML SSO。 如果决定使用其他应用，请确保该应用具有[所需的设置](create.md#configure-single-sign-on)。

应用未在“单一登录设置”页中显示 - 首先，搜索应用程序 ID。 如果未显示结果，请检查应用的 SAML 设置。 网格只显示具有正确 SAML 设置的应用。 

标识符 URL 必须是 `https://us3.datadoghq.com/account/saml/metadata.xml`。

回复 URL 必须是 `https://us3.datadoghq.com/account/saml/assertion`。

下图显示正确值。
  
:::image type="content" source="media/troubleshoot/troubleshooting.png" alt-text="在 AAD 中检查 Datadog 应用程序的 SAML 设置。" border="true":::

被邀请到租户的来宾用户无法访问单一登录 - 某些用户在 Azure 门户有两个电子邮件地址。 通常，一个电子邮件是用户主体名称 (UPN)，另一个电子邮件是备用电子邮件。

邀请来宾用户时，请使用主租户 UPN。 通过使用 UPN，你可以在单一登录过程中使电子邮件地址保持同步。 可以通过在用户的 Azure 门户的右上角查找电子邮件地址来查找 UPN。
  
## <a name="logs-not-being-emitted"></a>未发出日志

只有在 Azure Monitor 资源日志类别中列出的资源才会向 Datadog 发出日志。 若要验证资源是否正在将日志发送到 Datadog，请导航到特定资源的 Azure 诊断设置。 验证是否有 Datadog 诊断设置。

:::image type="content" source="media/troubleshoot/diagnostic-setting.png" alt-text="Azure 资源上的 Datadog 诊断设置" border="true":::

## <a name="metrics-not-being-emitted"></a>未发出指标

在适当的 Azure 订阅中，为 Datadog 资源分配“监视查阅者”角色。 此角色允许 Datadog 资源收集指标并将这些指标发送到 Datadog。

若要验证资源是否具有正确的角色分配，请打开 Azure 门户并选择订阅。 在左窗格中，选择“访问控制(IAM)”。 搜索 Datadog 资源名称。 确认 Datadog 资源具有“监视查阅者”角色分配，如下所示。

:::image type="content" source="media/troubleshoot/datadog-role-assignment.png" alt-text="Azure 订阅中的 Datadog 角色分配" border="true":::

## <a name="datadog-agent-installation-fails"></a>Datadog 代理安装失败

通过 Azure Datadog 集成，可以在虚拟机或应用服务上安装 Datadog 代理。 配置 Datadog 代理时，使用在 API 密钥屏幕中选择为“默认密钥”的 API 密钥。 如果未选择默认密钥，Datadog 代理安装将失败。

如果 Datadog 代理配置了不正确的密钥，请导航到“API 密钥”屏幕并更改“默认密钥”。 必须卸载 Datadog 代理并重新安装它，才能使用新的 API 密钥配置虚拟机。

## <a name="next-steps"></a>后续步骤

了解如何[管理 Datadog 实例](manage.md)。
