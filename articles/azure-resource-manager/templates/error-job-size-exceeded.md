---
title: “超出作业大小”错误
description: 说明如何在作业大小或模板过大时排查错误。
ms.topic: troubleshooting
ms.date: 03/23/2021
ms.openlocfilehash: 5d7333e8faf44b5c2c872448960eff34ff61daf2
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111963262"
---
# <a name="resolve-errors-for-job-size-exceeded"></a>解决“超出作业大小”错误

本文介绍如何解决“JobSizeExceededException”和“DeploymentJobSizeExceededException”错误 。

## <a name="symptom"></a>症状

部署模板时收到一条错误，指出部署已超出限制。

## <a name="cause"></a>原因

当部署超过允许的限制之一时，你会收到此错误。 通常，当模板或运行部署的作业太大时，会出现此错误。

部署作业不能超过 1 MB。 作业包括有关请求的元数据。 对于大型模板，与模板组合的元数据可能会超出作业允许的大小。

模板不能超过 4 MB。 为使用 [copy ](copy-resources.md) 创建许多实例的资源定义扩展模板后，4-MB 限制适用于模板的最终状态。 最终状态还包括变量和参数的已解析值。

模板的其他限制如下：

* 256 个参数
* 256 个变量
* 800 个资源（包括副本计数）
* 64 个输出值
* 模板表达式中不超过 24,576 个字符

使用复制循环来部署资源时，请不要使用循环名称作为依赖项：

```json
dependsOn: [ "nicLoop" ]
```

请改用需要依赖的循环中的资源实例。 例如：

```json
dependsOn: [
    "[resourceId('Microsoft.Network/networkInterfaces', concat('nic-', copyIndex()))]"
]
```

## <a name="solution-1---simplify-template"></a>解决方案 1 - 简化模板

第一种选择是简化模板。 如果模板部署了大量不同的资源类型，则可以进行这种选择。 考虑将模板划分为[链接模板](linked-templates.md)。 将资源类型划分为逻辑组，并为每个组添加链接模板。 例如，如果需要部署大量网络资源，则可以将这些资源移到链接模板中。

可以将其他资源设置为依赖于链接模板，并[从链接模板的输出获取值](linked-templates.md#get-values-from-linked-template)。

## <a name="solution-2---reduce-name-size"></a>解决方案 2 - 缩小名称

请尝试缩短用于[参数](./parameters.md)、[变量](./variables.md)和[输出](./outputs.md)的名称的长度。 当通过复制循环重复这些值时，一个大的名称会被相乘很多次。