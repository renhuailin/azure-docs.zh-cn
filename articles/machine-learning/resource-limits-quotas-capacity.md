---
title: 服务限制
titleSuffix: Azure Machine Learning
description: 用于容量计划的服务限制以及请求和响应 Azure 机器学习的最大限制。
services: machine-learning
author: andscho
ms.author: andscho
ms.reviewer: mldocs
ms.topic: reference
ms.service: machine-learning
ms.subservice: core
ms.date: 12/21/2020
ms.openlocfilehash: b675e72df4f128d0ce096b3ac398fab63c20557e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "97930797"
---
# <a name="service-limits-in-azure-machine-learning"></a>Azure 机器学习服务限制

本部分列出了 Azure 机器学习中的基本配额和限制阈值。 若要了解如何增加资源配额，请参阅[管理和增加资源配额](how-to-manage-quotas.md)

## <a name="workspaces"></a>工作区
| 限制 | Value |
| --- | --- |
| 工作区名称 | 2-32 个字符 |

## <a name="runs"></a>运行次数
| 限制 | Value |
| --- | --- |
| 每工作区运行数 | 1 千万 |
| RunId/ParentRunId | 256 个字符 |
| DataContainerId | 261 个字符 |
| DisplayName |256 个字符|
| 说明 |5,000 个字符|
| 属性数 |50 |
| 属性键的长度 |100 个字符 |
| 属性值的长度 |1,000 个字符 |
| 标记数 |50 |
| 标记键长度 |100 |
| 标记值长度 |1,000 个字符 |
| CancelUri / CompleteUri / DiagnosticsUri |1,000 个字符 |
| 错误消息长度 |3,000 个字符 |
| 警告消息长度 |300 个字符 |
| 输入数据集数 |200 |
| 输出数据集数 |20 |


## <a name="metrics"></a>指标
| 限制 | Value |
| --- | --- |
| 每次运行的指标名称 |50|
| 每个指标的指标行名称 |1 千万|
| 每个指标行的列数 |15|
| 指标列名称长度 |255 个字符 |
| 指标列值长度 |255 个字符 |
| 每批上传的指标行数 | 250 |

> [!NOTE]
> 如果由于将变量格式化为指标名称而达到每个运行的指标名称限制，请考虑改用行指标，其中一列是变量值，第二列是指标值。

## <a name="artifacts"></a>Artifacts

| 限制 | Value |
| --- | --- |
| 每次运行的项目数 |1 千万|
| 项目路径的最大长度 |5,000 个字符 |

## <a name="limit-increases"></a>限制增加
可以通过[联系支持人员](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)来提升单个工作区的一些限制。 

## <a name="next-steps"></a>后续步骤

- [配置 Azure 机器学习环境](how-to-configure-environment.md)
- 若要了解如何增加资源配额，请参阅[管理和增加资源配额](how-to-manage-quotas.md)。

