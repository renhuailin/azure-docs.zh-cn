---
title: Azure 数据工厂实体的命名规则
description: 描述数据工厂实体的命名规则。
author: ssabat
ms.author: susabat
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 0e469df360fed09924dbf6759bdd84c15671c7f9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739423"
---
# <a name="azure-data-factory---naming-rules"></a>Azure 数据工厂 - 命名规则

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

下表提供了数据工厂项目的命名规则。

| 名称 | 名称唯一性 | 验证检查 |
|:--- |:--- |:--- |
| 数据工厂 | 在 Microsoft Azure 内唯一。 名称不区分大小写，即 `MyDF` 和 `mydf` 指的是同一个数据工厂。 |<ul><li>一个数据工厂绑定到一个 Azure 订阅。</li><li>对象名称必须以字母或数字开头，并且只能包含字母、数字和短划线 (-) 字符。</li><li>每个短划线 (-) 字符的前后必须紧接字母或数字。 容器名称中不允许使用连续短划线。</li><li>名称长度为 3-63 个字符。</li></ul> |
| 链接服务/数据集/管道/数据流 | 在数据工厂中唯一。 名称不区分大小写。 |<ul><li>对象名称必须以字母开头。</li><li>不允许使用以下字符：“.”、“+”、“?”、“/”、“<”、“>”、“*”、“%”、“&”、“:”、“\\”</li><li>短划线 ("-") 不可用于链接服务、数据流和数据集的名称。</li></ul>  |
| Integration Runtime |在数据工厂中唯一。 名称不区分大小写。 |<ul><li>集成运行时名称只能包含字母、数字和短划线 (-) 字符。</li><li>第一个字符和最后一个字符必须是字母或数字。 每个短划线 (-) 字符的前后必须紧接字母或数字。</li><li>集成运行时名称中不允许使用连续短划线。 </li></ul> |
| 数据流转换 | 在数据流中是唯一的。 名称不区分大小写 | <ul><li>数据流转换名称只能包含字母和数字</li><li>第一个字符必须是字母。 </li></ul> |
| 资源组 |在 Microsoft Azure 内唯一。 名称不区分大小写。 | 有关详细信息，请参阅 [Azure 命名规则和限制](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming)。 |
| 管道参数和变量  |在管道中唯一。 名称不区分大小写。 | <ul><li>由于向后兼容性，对参数名称和变量名称进行的验证检查限制为唯一。</li><li>当使用参数或变量引用实体名称（例如，链接服务）时，将应用实体命名规则。</li><li>一种好办法是遵循数据流转换的命名规则来命名管道参数和变量。</li></ul> |

## <a name="next-steps"></a>后续步骤

了解如何按照[快速入门：创建数据工厂](quickstart-create-data-factory-powershell.md)一文中的分步说明创建数据工厂。 
