---
title: 跟踪 Microsoft Azure 使用量承诺 (MACC)
description: 了解如何跟踪 Microsoft 客户协议 (MACC) 的 Microsoft Azure 使用量承诺 (MACC)。
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 07/06/2021
ms.author: banders
ms.openlocfilehash: 41a4f22e669300e24ddce0248e8ab1744b773202
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2021
ms.locfileid: "113301917"
---
# <a name="track-your-microsoft-azure-consumption-commitment-macc"></a>跟踪 Microsoft Azure 使用量承诺 (MACC)

Microsoft Azure 使用量承诺 (MACC) 是你的组织针对一段时间内的 Microsoft Azure 支出做出的合同承诺。 如果你的组织的某个 Microsoft 客户协议 (MCA) 计费帐户具有 MACC，则你可以通过 Azure 门户或 REST API 查看承诺的重要内容，包括开始和结束日期、剩余承诺金额以及合格的支出。 企业协议 (EA) 计费帐户的 MACC 或 CTC 尚不可通过 Azure 门户或 REST API 进行查看。

## <a name="track-your-macc-commitment"></a>跟踪 MACC 承诺

### <a name="azure-portal"></a>[Azure 门户](#tab/portal)

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 搜索“成本管理 + 计费”。  
    :::image type="content" source="./media/track-consumption-commitment/billing-search-cost-management-billing.png" alt-text="屏幕截图显示了在门户中搜索“成本管理 + 计费”。" lightbox="./media/track-consumption-commitment/billing-search-cost-management-billing.png" :::
3. 在计费范围页中，选择要跟踪其承诺的计费帐户。 计费帐户的类型应为 **Microsoft 客户协议**。  
    :::image type="content" source="./media/track-consumption-commitment/list-of-scopes.png" alt-text="显示计费范围的屏幕截图。" lightbox="./media/track-consumption-commitment/list-of-scopes.png" :::
    > [!NOTE]
     > Azure 门户会记住你访问的最后一个计费范围，并在你下一次转到“成本管理 + 计费”页时显示该范围。 如果你早前访问过“成本管理 + 计费”，则看不到计费范围页。 如果是这样，请检查你是否处于[正确的范围](#check-access-to-a-microsoft-customer-agreement)。 否则，请[切换范围](view-all-accounts.md#switch-billing-scope-in-the-azure-portal)，选择 Microsoft 客户协议的计费帐户。
4. 从左侧选择“属性”，然后选择“Microsoft Azure 使用量承诺(MACC)”。  
    :::image type="content" source="./media/track-consumption-commitment/select-macc-tab.png" alt-text="屏幕截图显示了选择 MACC 选项卡。" lightbox="./media/track-consumption-commitment/select-macc-tab.png" :::
5. “Microsoft Azure 使用量承诺(MACC)”选项卡包含以下部分。

#### <a name="remaining-commitment"></a>剩余承诺金额 

“剩余承诺金额”显示自上次发票之后的剩余承诺金额。

:::image type="content" source="./media/track-consumption-commitment/macc-remaining-commitment.png" alt-text="屏幕截图显示了某个 MACC 的剩余承诺金额。" lightbox="./media/track-consumption-commitment/macc-remaining-commitment.png" :::

#### <a name="details"></a>详细信息

“详细信息”部分显示承诺使用量的其他重要方面。

:::image type="content" source="./media/track-consumption-commitment/macc-details.png" alt-text="屏幕截图显示了 MACC 详细信息。" lightbox="./media/track-consumption-commitment/macc-details.png" :::

| 术语 | 定义 |
|---|---|
| ID | 唯一标识你的 MACC 的标识符。 此标识符用来通过 REST API 获取你的 MACC 信息。 |
| 购买日期 | 做出承诺的日期。 |
| 开始日期 | 承诺生效的日期。 |
| 结束日期 | 承诺到期的日期。 |
| 承诺金额 | 你承诺在符合 MACC 条件的产品/服务上支出的金额。 |
| 状态 | 你的承诺的状态。 |

你的 MACC 可能具有下列状态之一：

- 活动：MACC 处于活动状态。 任何合格支出都会计入你的 MACC 承诺。
- 已完成：你已完成你的 MACC 承诺。 
- 已过期：MACC 已过期。 有关详细信息，请与你的 Microsoft 帐户团队联系。 
- 已取消：MACC 已被取消。 新的 Azure 支出不会计入你的 MACC 承诺。

#### <a name="events"></a>事件

“事件”部分显示对你的 MACC 承诺造成减量的事件（已开具发票的支出）。

:::image type="content" source="./media/track-consumption-commitment/macc-events.png" alt-text="屏幕截图显示了 MACC 事件。" lightbox="./media/track-consumption-commitment/macc-events.png" :::

| 术语 | 定义 |
|---|---|
| Date | 事件的发生日期 |
| 说明 | 事件的说明 |
| 计费配置文件 | 发生的事件对应的计费对象信息 |
| MACC 减量 | 事件导致的 MACC 减量 |
| 剩余承诺金额 | 发生此事件后的剩余 MACC 承诺金额 |

### <a name="rest-api"></a>[REST API](#tab/rest)

可以使用 [Azure 计费](/rest/api/billing/)和[消耗](/rest/api/consumption/) API 以编程方式获取你的计费帐户的 Microsoft Azure 使用量承诺 (MACC)。

下面显示的示例使用 REST API。 目前，PowerShell 和 Azure CLI 不受支持。

### <a name="find-billing-accounts-you-have-access-to"></a>查找你有权访问的计费帐户

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2020-05-01
```
API 响应会返回计费帐户的列表。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "type": "Microsoft.Billing/billingAccounts",
      "properties": {
        "displayName": "Contoso",
        "agreementType": "MicrosoftCustomerAgreement",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "hasReadAccess": true,
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/9a12f056-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "9a12f056-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "type": "Microsoft.Billing/billingAccounts",
      "properties": {
        "displayName": "Kayla Lewis",
        "agreementType": "MicrosoftCustomerAgreement",
        "accountStatus": "Active",
        "accountType": "Individual",
        "hasReadAccess": true,
      }
    }
  ]
}
```

使用计费帐户的 `displayName` 属性来标识要跟踪其 MACC 的计费帐户。 复制计费帐户的 `name`。 例如，如果想要跟踪 Contoso 计费帐户的 MACC，则需要复制 `9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx`。 将该值粘贴到某个位置，以便在下一步使用它。

### <a name="get-a-list-of-microsoft-azure-consumption-commitments"></a>获取 Microsoft Azure 使用量承诺的列表

发出以下请求，将 `<billingAccountName>` 替换为在第一步复制的 `name` (`9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx`)。

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/providers/Microsoft.Consumption/lots?api-version=2021-05-01&$filter=source%20eq%20%27ConsumptionCommitment%27
```
API 响应会返回你的计费帐户的 MACC 的列表。

```json
    {
    "value": [
      {
        "id": "/providers/Microsoft.Billing/billingAccounts/9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/lots/G2021032459206000XXXX",
        "name": "G2021032459206000XXXX",
        "type": "Microsoft.Consumption/lots",
        "eTag": null,
        "properties": {
          "purchasedDate": "2021-03-24T16:26:46.0000000Z",
          "status": "Active",
          "originalAmount": {
            "currency": "USD",
            "value": 10000.0
          },
          "closedBalance": {
            "currency": "USD",
            "value": 9899.42
          },
          "source": "ConsumptionCommitment",
          "startDate": "2021-03-01T00:00:00.0000000Z",
          "expirationDate": "2024-02-28T00:00:00.0000000Z"
        }
      },
      {
        "id": "/providers/Microsoft.Billing/billingAccounts/9a157b81-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/lots/G1011082459206000XXXX",
        "name": "G1011082459206000XXXX",
        "type": "Microsoft.Consumption/lots",
        "eTag": null,
        "properties": {
          "purchasedDate": "2021-03-24T16:26:46.0000000Z",
          "status": "Complete",
          "originalAmount": {
            "currency": "USD",
            "value": 10000.0
          },
          "closedBalance": {
            "currency": "USD",
            "value": 0.00
          },
          "source": "ConsumptionCommitment",
          "startDate": "2020-03-01T00:00:00.0000000Z",
          "expirationDate": "2021-02-28T00:00:00.0000000Z"
        }
      }
    ]
  }
```

| 元素名称  | 说明                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `purchasedDate`  | 购买 MACC 的日期。   |
| `status`  | 你的承诺的状态。 |
| `originalAmount` | 原始承诺金额。 |
| `closedBalance`   | 自上次发票之后的剩余承诺金额。    |
| `source`      | 对于 MACC，source 将始终为 ConsumptionCommitment。 |
| `startDate`      |  MACC 的激活日期。  |
| `expirationDate`  | MACC 的到期日期。   |

你的 MACC 可能具有下列状态之一： 

- 活动：MACC 处于活动状态。 任何合格支出都会计入你的 MACC 承诺。
- 已完成：你已完成你的 MACC 承诺。 
- 已过期：MACC 已过期。 有关详细信息，请与你的 Microsoft 帐户团队联系。 
- 已取消：MACC 已被取消。 新的 Azure 支出不会计入你的 MACC 承诺。 

### <a name="get-events-that-affected-macc-commitment"></a>获取影响了 MACC 承诺的事件

发出以下请求，将 `<billingAccountName>` 替换为在第一步复制的 `name` (`5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx`)。 需要传递 startDate 和 endDate 来获取所需期间内的事件。

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/providers/Microsoft.Consumption/events?api-version=2021-05-01&startDate=<startDate>&endDate=<endDate>&$filter=lotsource%20eq%20%27ConsumptionCommitment%27
```

API 响应会返回影响了你的 MACC 承诺的所有事件。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/events/103axxxx-2c25-7xx3-f2a0-ad9a3f1c91xx",
      "name": "103axxxx-2c25-7xx3-f2a0-ad9a3f1c91xx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/SWFF-DVM4-XXX-XXX",
        "billingProfileDisplayName": "Finance",
        "lotId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/lots/G2021032459206000XXXX",
        "lotSource": "ConsumptionCommitment",
        "transactionDate": "2021-05-05T00:09:13.0000000Z",
        "description": "Balance after invoice T00075XXXX",
        "charges": {
          "currency": "USD",
          "value": -100.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 9899.71
        },
        "eventType": "SettledCharges",
        "invoiceNumber": "T00075XXXX"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/events/203axxxx-2c25-7xx3-f2a0-ad9a3f1c91xx",
      "name": "203axxxx-2c25-7xx3-f2a0-ad9a3f1c91xx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/SWFF-DVM4-XXX-XXX",
        "billingProfileDisplayName": "Engineering",
        "lotId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/providers/Microsoft.Consumption/lots/G2021032459206000XXXX",
        "lotSource": "ConsumptionCommitment",
        "transactionDate": "2021-04-05T00:09:13.0000000Z",
        "description": "Balance after invoice T00074XXXX",
        "charges": {
          "currency": "USD",
          "value": -0.29
        },
        "closedBalance": {
          "currency": "USD",
          "value": 9999.71
        },
        "eventType": "SettledCharges",
        "invoiceNumber": "T00074XXXX"
      }
    }
  ]
}

```
| 元素名称  | 说明                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `billingProfileId` | 发生的事件对应的计费对象信息的唯一标识符。 |
| `billingProfileDisplayName` | 发生的事件对应的计费对象信息的显示名称。 |
| `lotId`   | MACC 的唯一标识符。    |
| `lotSource`      | 它将是 MACC 的 ConsumptionCommitment。 |
| `transactionDate`      |  事件的发生日期。  |
| `description`  | 事件的说明。   |
| `charges`  | MACC 减量金额。   |
| `closedBalance`  | 发生此事件后的余额。   |
| `eventType`  | MACC 仅支持 SettledCharges 事件。   |
| `invoiceNumber`  | 其收费对 MACC 造成了减量的发票的唯一 ID。   |

---

## <a name="azure-services-and-marketplace-offers-that-are-eligible-for-macc"></a>符合 MACC 条件的 Azure 服务和市场产品/服务

你可以在 Azure 门户中确定哪些 Azure 服务和市场产品/服务符合 MACC 减量条件。 有关详细信息，请参阅[确定哪些产品/服务符合 Azure 使用量承诺 (MACC/CTC) 的条件](/marketplace/azure-consumption-commitment-benefit#determine-which-offers-are-eligible-for-azure-consumption-commitments-maccctc)。

## <a name="azure-credits-and-macc"></a>Azure 额度和 MACC

如果你的组织收到了 Microsoft 的 Azure 额度，则这些额度涵盖的消耗或购买不会计入 MACC 承诺。

如果你的组织购买了 Azure 预付款，则这些额度涵盖的消耗或购买不会计入 MACC 承诺。  但是，实际的预付款购买本身会对 MACC 承诺造成减量。

例如，Contoso 在 5 月份做出了金额为 50,000 美元的 MACC 承诺。 在 6 月份，他们购买了 10,000 美元的 Azure 预付款。 此购买将对其 MACC 承诺造成减量，剩余承诺金额将变为 40,000 美元。 在 6 月份，Contoso 使用了 10,000 美元符合 Azure 预付款条件的服务。 服务费用将由 Azure 预付款支付；但是，服务费用不会对其 MACC 承诺造成减量。 用完 Azure 预付款后，所有 Azure 服务消耗和其他符合条件的购买都会对其 MACC 承诺造成减量。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查对 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

## <a name="next-steps"></a>后续步骤

- [确定哪些产品/服务符合 Azure 使用量承诺 (MACC/CTC) 的条件](/marketplace/azure-consumption-commitment-benefit#determine-which-offers-are-eligible-for-azure-consumption-commitments-maccctc)
- [跟踪 Azure 额度余额](mca-check-azure-credits-balance.md)