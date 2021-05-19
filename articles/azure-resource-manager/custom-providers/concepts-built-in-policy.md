---
title: 使用策略部署关联
description: 了解如何使用 Azure Policy 服务为自定义提供程序部署关联。
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: 00cd1d39c0110aac9ea96f73127e83197976c95a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "82190123"
---
# <a name="deploy-associations-for-a-custom-provider-using-azure-policy"></a>使用 Azure Policy 为自定义提供程序部署关联

Azure 策略可用于部署关联，将资源关联到自定义提供程序。 本文介绍了一种用于部署关联的内置策略，并介绍了如何使用该策略。

## <a name="built-in-policy-to-deploy-associations"></a>用于部署关联的内置策略

“为自定义提供程序部署关联”是一种内置策略，此策略可用于部署关联，将资源关联到自定义提供程序。 此策略接受三个参数：

- 自定义提供程序 ID - 此 ID 是资源需要与之关联的自定义提供程序的资源 ID。
- 要关联的资源类型 - 这些资源类型是要关联到自定义提供程序的资源类型的列表。 可以使用同一策略将多个资源类型关联到一个自定义提供程序。
- 关联名称前缀 - 此字符串是要添加到正在创建的关联资源的名称中的前缀。 默认值为“DeployedByPolicy”。

DeployIfNotExists 评估 它在资源提供程序处理资源创建或更新请求并且评估返回成功状态代码后运行。 目前可以使用任何模板部署方法部署此模板。
有关关联的详细信息，请参阅 [Azure 自定义提供程序资源载入](./concepts-resource-onboarding.md)

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>如何使用部署关联内置策略 

### <a name="prerequisites"></a>先决条件
如果自定义提供程序需要策略范围的权限来执行某一操作，则在没有授予权限的情况下，关联资源的策略部署将无法正常工作。

### <a name="policy-assignment"></a>策略分配
若要使用内置策略，请创建一个策略分配，并分配“为自定义提供程序部署关联”策略。 然后，该策略将标识不合规的资源并为这些资源部署关联。

![分配内置策略](media/concepts-built-in-policy/assign-builtin-policy-customprovider.png)

## <a name="getting-help"></a>获取帮助

如果你遇到了 Azure 自定义资源提供程序部署方面的问题，请尝试在 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers) 上提问。 该论坛上可能已有类似问题的解答，因此，在发贴之前请先查看以往的提问。 添加标记 ```azure-custom-providers``` 可以快速得到回复！

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用内置策略来部署关联。 请查看以下文章了解详细信息：

- [概念：Azure 自定义提供程序资源加入](./concepts-resource-onboarding.md)
- [教程：使用自定义提供程序进行资源加入](./tutorial-resource-onboarding.md)
- [教程：在 Azure 中创建自定义操作和资源](./tutorial-get-started-with-custom-providers.md)
- [快速入门：创建自定义资源提供程序和部署自定义资源](./create-custom-provider.md)
- [操作说明：将自定义操作添加到 Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [操作说明：将自定义资源添加到 Azure REST API](./custom-providers-resources-endpoint-how-to.md)
