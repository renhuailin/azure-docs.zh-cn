---
title: 服务限制
titleSuffix: Azure Machine Learning
description: 用于容量计划的服务限制以及对 Azure 机器学习的请求和响应的最大限制。
services: machine-learning
author: andscho
ms.author: andscho
ms.reviewer: mldocs
ms.topic: reference
ms.service: machine-learning
ms.subservice: core
ms.date: 12/21/2020
ms.openlocfilehash: b675e72df4f128d0ce096b3ac398fab63c20557e
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97930797"
---
# <a name="service-limits-in-azure-machine-learning"></a>Azure 机器学习中的服务限制

本部分列出了 Azure 机器学习中的基本配额和限制阈值。 若要了解如何增加资源配额，请参阅 ["管理和增加资源配额"](how-to-manage-quotas.md)

## <a name="workspaces"></a>工作区
| 限制 | 值 |
| --- | --- |
| 工作区名称 | 2-32 个字符 |

## <a name="runs"></a>运行次数
| 限制 | 值 |
| --- | --- |
| 每个工作区运行 | 1 千万 |
| RunId/ParentRunId | 256 个字符 |
| DataContainerId | 261个字符 |
| DisplayName |256 个字符|
| 说明 |5000个字符|
| 属性数目 |50 |
| 属性键的长度 |100 个字符 |
| 属性值的长度 |1000个字符 |
| 标记数 |50 |
| 标记键的长度 |100 |
| 标记值的长度 |1000个字符 |
| CancelUri / CompleteUri / DiagnosticsUri |1000个字符 |
| 错误消息长度 |3000个字符 |
| 警告消息长度 |300个字符 |
| 输入数据集的数量 |200 |
| 输出数据集的数目 |20 |


## <a name="metrics"></a>指标
| 限制 | 值 |
| --- | --- |
| 每次运行的指标名称 |50|
| 每个指标的指标行名称 |1 千万|
| 每个指标行的列数 |15|
| 指标列名称长度 |255 个字符 |
| 指标列值长度 |255 个字符 |
| 每批上传的指标行数 | 250 |

> [!NOTE]
> 如果要在每次运行时达到指标名称的限制，因为要将变量格式化为指标名称，请考虑改用行指标，其中一列是变量值，第二列是指标值。

## <a name="artifacts"></a>Artifacts

| 限制 | 值 |
| --- | --- |
| 每个运行的项目数 |1 千万|
| 项目路径的最大长度 |5000个字符 |

## <a name="limit-increases"></a>限制增加
可以通过与 [支持人员联系](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)来增加单个工作区的某些限制。 

## <a name="next-steps"></a>后续步骤

- [配置 Azure 机器学习环境](how-to-configure-environment.md)
- 了解如何在 ["管理和增加资源配额"](how-to-manage-quotas.md)中增加资源配额。

