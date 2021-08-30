---
title: Azure Sentinel 监视列表模板的架构 | Microsoft Docs
description: 了解 Azure Sentinel 中每个内置的监视列表模板中使用的架构。
author: batamig
ms.author: bagold
ms.service: azure-sentinel
ms.topic: reference
ms.custom: mvc
ms.date: 08/04/2021
ms.subservice: azure-sentinel
ms.openlocfilehash: e263c742ae31a665eb428ad2cbeca72f73562b8a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778107"
---
# <a name="azure-sentinel-built-in-watchlist-template-schemas-public-preview"></a>Azure Sentinel 内置的监视列表模板架构（公共预览版）

本文详细介绍了 Azure Sentinel 提供的每个内置监视列表模板中使用的架构。 有关详细信息，请参阅[使用模板（公共预览版）创建新的监视列表](watchlists.md#create-a-new-watchlist-using-a-template-public-preview)。

> [!IMPORTANT]
> Azure Sentinel 监视列表模板目前处于预览版。 [Azure 预览版补充条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
>


## <a name="high-value-assets"></a>高价值资产

“高价值资产”监视列表列出组织中具有重要价值的设备、资源和其他资产，此监视列表包括以下字段：

| 字段名称 | 格式                              | 示例                                                                                                                                | 必需/可选 |
| ---------- | ----------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------------------ |
| **资产类型** | 字符串                              | `Device`, `Azure resource`, `AWS resource`, `URL`, `SPO`, `File share`, `Other`                                                                      | 必需          |
| **资产 ID**   | 字符串，视资产类型而定 | `/subscriptions/d1d8779d-38d7-4f06-91db-9cbc8de0176f/resourceGroups/SOC-Purview/providers/Microsoft.Storage/storageAccounts/purviewadls` | 必需          |
| **资产名称** | 字符串                              | `Microsoft.Storage/storageAccounts/purviewadls`                                                                                          | 可选           |
| **资产 FQDN** | FQDN                                | `Finance-SRv.local.microsoft.com`                                                                                                        | 必需          |
| **IP 地址** | IP                                  | `1.1.1.1`                                                                                                                                | 可选           |
| **标记**       | 列出                                | `["SAW user","Blue Ocean team"] `                                                                                                        | 可选           |
| | | | |

## <a name="vip-users"></a>VIP 用户

“VIP 用户”监视列表列出组织中影响力较高的员工的用户帐户，此监视列表包括以下值：

| 字段名称          | 格式 | 示例                                             | 必需/可选 |
| ------------------- | ------ | --------------------------------------------------- | ------------------ |
| **用户标识符**     | UID    | `52322ec8-6ebf-11eb-9439-0242ac130002`                | 可选           |
| **用户 AAD 对象 ID**  | SID    | `03fa4b4e-dc26-426f-87b7-98e0c9e2955e`                | 可选           |
| **用户本地 SID**    | SID    | `S-1-12-1-4141952679-1282074057-627758481-2916039507` | 可选           |
| **用户主体名称** | UPN    | `JeffL@seccxp.ninja`                                  | 必需          |
| **标记**                | 列出   | `["SAW user","Blue Ocean team"]`                      | 可选           |
| | | | |

## <a name="network-mapping"></a>网络映射

“网络映射”监视列表列出 IP 子网及其各自的组织上下文，此监视列表包括以下字段：

| 字段名称 | 格式       | 示例                      | 必需/可选 |
| ---------- | ------------ | ---------------------------- | ------------------ |
| **IP 子网**  | 子网范围 |` 198.51.100.0/24 - 198….../22` | 必需          |
| **范围名** | 字符串       | `DMZ`                          | 可选           |
| **标记**       | 列出         | `["Example","Example"]`        | 可选           |
| | | | |

## <a name="terminated-employees"></a>离职的员工

“离职的员工”监视列表列出已离职或即将离职的员工的用户帐户，此监视列表包括以下字段：

| 字段名称          | 格式                                                                          | 示例                              | 必需/可选 |
| ------------------- | ------------------------------------------------------------------------------- | ------------------------------------ | ------------------ |
| **用户标识符**     | UID                                                                             | `52322ec8-6ebf-11eb-9439-0242ac130002` | 可选           |
| **用户 AAD 对象 ID**  | SID                                                                             | `03fa4b4e-dc26-426f-87b7-98e0c9e2955e` | 可选           |
| **用户本地 SID**    | SID                                                                             | `S-1-12-1-4141952679-1282074057-123`   | 可选           |
| **用户主体名称** | UPN                                                                             | `JeffL@seccxp.ninja`                  | 必需          |
| **UserState**           | 字符串 <br><br>建议使用 `Notified` 或 `Terminated` | `Terminated`                           | 必需          |
| **通知日期**  | 时间戳 - 具体日期                                                                 | `01.12.20`                             | 可选           |
| **终止日期**    | 时间戳 - 具体日期                                                                 | `01.01.21`                            | 必需          |
| **标记**                | 列出                                                                            | `["SAW user","Amba Wolfs team"]`       | 可选           |
| | | | |


## <a name="identity-correlation"></a>标识相关

“标识相关”监视列表列出属于同一人员的相关用户帐户，此监视列表包括以下字段：

| 字段名称                       | 格式  | 示例                                             | 必需/可选 |
| -------------------------------- | ------- | --------------------------------------------------- | ------------------ |
| **用户标识符**                  | UID     | `52322ec8-6ebf-11eb-9439-0242ac130002`                | 可选           |
| **用户 AAD 对象 ID**               | SID     | `03fa4b4e-dc26-426f-87b7-98e0c9e2955e`                | 可选           |
| **用户本地 SID**                 | SID     | `S-1-12-1-4141952679-1282074057-627758481-2916039507` | 可选           |
| **用户主体名称**              | UPN     | `JeffL@seccxp.ninja`                                  | 必需          |
| **员工 ID**                      | 字符串  | `8234123`                                             | 可选           |
| **Email**                            | 电子邮件   | `JeffL@seccxp.ninja`                                  | 可选           |
| **关联的特权帐户 ID** | UID/SID | `S-1-12-1-4141952679-1282074057-627758481-2916039507` | 可选           |
| **关联的特权帐户**    | UPN     | `Admin@seccxp.ninja`                                  | 可选           |
| **标记**                             | 列出    | `["SAW user","Amba Wolfs team"]`                      | 可选           |
| | | | |

## <a name="service-accounts"></a>服务帐户

“服务帐户”监视列表列出服务帐户及其所有者，此监视列表包括以下字段：

| 字段名称                | 格式 | 示例                                             | 必需/可选 |
| ------------------------- | ------ | --------------------------------------------------- | ------------------ |
| **服务标识符**        | UID    | `1111-112123-12312312-123123123`                      | 可选           |
| **服务 AAD 对象 ID**     | SID    | `11123-123123-123123-123123`                          | 可选           |
| **服务本地 SID**       | SID    | `S-1-12-1-3123123-123213123-12312312-2916039507`      | 可选           |
| **服务主体名称**    | UPN    | `myserviceprin@contoso.com`                           | 必需          |
| **所有者用户标识符**     | UID    | `52322ec8-6ebf-11eb-9439-0242ac130002`                | 可选           |
| **所有者用户 AAD 对象 ID**  | SID    | `03fa4b4e-dc26-426f-87b7-98e0c9e2955e`                | 可选           |
| **所有者用户本地 SID**    | SID    | `S-1-12-1-4141952679-1282074057-627758481-2916039507` | 可选           |
| **所有者用户主体名称** | UPN    | `JeffL@seccxp.ninja`                                  | 必需          |
| **标记**                      | 列出   | `["Automation Account","GitHub Account"]`             | 可选           |
| | | | |

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[使用 Azure Sentinel 监视列表](watchlists.md)。
