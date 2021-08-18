---
title: 排查签署 Microsoft 客户协议后出现的订阅访问问题 - Azure
description: 本文可帮助排查签署 Microsoft 客户协议后出现的订阅访问问题。
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.subservice: microsoft-customer-agreement
ms.topic: troubleshooting
ms.date: 04/07/2021
ms.author: banders
ms.reviewer: baolcsva
ms.openlocfilehash: dff361bb3413bfc57383a284e5c162c61e7d6212
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724458"
---
# <a name="troubleshoot-subscription-access-after-you-sign-a-microsoft-customer-agreement"></a>排查签署 Microsoft 客户协议后出现的订阅访问问题

本文可帮助排查签署 Microsoft 客户协议后出现的订阅访问问题。 请使用以下信息来排查和解决常见问题。

## <a name="troubleshoot-subscription-access"></a>排查订阅访问问题

请确保你的计费帐户类型为“Microsoft 客户协议”。 可以在 Azure 门户的“成本管理 + 计费”中查看计费帐户类型。 有关详细信息，请参阅[检查你是否有权访问 MCA](../understand/mca-understand-your-usage.md#check-access-to-a-microsoft-customer-agreement)。

## <a name="troubleshoot-viewing-your-billing-account"></a>排查在查看计费帐户时遇到的问题

如果在查看计费帐户时遇到问题，并且有多个租户，请尝试在 Azure 门户中切换目录。

1. 在 Azure 门户的右上方，选择你的 Azure 帐户。
1. 选择“切换目录”。  
    :::image type="content" source="./media/troubleshoot-subscription-access/switch-directory.png" alt-text="显示“切换目录”选项的屏幕截图。" lightbox="./media/troubleshoot-subscription-access/switch-directory.png" :::
1. 在“目录 + 订阅”窗口中，选择要切换到的另一个目录。  
    :::image type="content" source="./media/troubleshoot-subscription-access/select-directory.png" alt-text="显示在何处选择其他目录的屏幕截图。" lightbox="./media/troubleshoot-subscription-access/select-directory.png" :::

## <a name="troubleshoot-account-access"></a>排查帐户访问问题

你可能有多个使用相同电子邮件地址的 Microsoft 帐户。 你可能有个人帐户和工作帐户。 Microsoft 客户协议使用你的工作或学校帐户。 有关详细信息，请参阅[确保使用工作或学校帐户登录](https://support.microsoft.com/office/which-account-do-you-want-to-use-2b5bbd7a-7df6-4283-beff-8015e28eb7b9)。

- 使用工作或学校帐户登录。  
    :::image type="content" source="./media/troubleshoot-subscription-access/two-accounts.png" alt-text="显示“工作或学校帐户”选项的屏幕截图。" lightbox="./media/troubleshoot-subscription-access/two-accounts.png" :::

## <a name="next-steps"></a>后续步骤

- 阅读 [Microsoft 客户协议文档](./index.yml)。