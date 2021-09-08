---
title: Azure Functions 语言运行时支持策略
description: 了解 Azure Functions 语言运行时支持策略
ms.topic: conceptual
ms.date: 08/17/2021
ms.openlocfilehash: b3b5f7cf108fd18ed450a6837a5dd35ceb83dc60
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123303960"
---
# <a name="language-runtime-support-policy"></a>语言运行时支持策略

本文介绍 Azure Functions 语言运行时支持策略。 

## <a name="retirement-process"></a>停用过程

Azure Functions 运行时是围绕操作系统、Azure Functions 主机和特定于语言的辅助角色等各种组件构建的。 为了保持对函数应用的全方面支持，随着编程语言版本的生命周期日渐终结，Azure Functions 运用分阶段的支持减缩原则。 对于大多数语言版本，停用日期将与社区生命周期终结日期保持一致。 

### <a name="notification-phase"></a>通知阶段

我们会向函数应用用户发送有关语言版本即将停用的通知电子邮件。 通知将在停用日期前至少一年发出。 收到通知后，你应该准备好将函数应用使用的语言版本升级到受支持的版本。

### <a name="retirement-phase"></a>停用阶段

从某个语言版本的生命周期结束日期开始，便无法再创建针对该语言版本的新函数应用。

在该语言生命周期结束日期之后，使用已停用语言版本的函数应用将不符合获得新功能、安全补丁和性能优化的条件。 不过，这些函数应用将继续在平台上运行。 

> [!IMPORTANT]
>我们强烈建议将受影响函数应用的语言版本升级到受支持的版本。   
>如果运行使用不受支持的语言版本的函数应用，则需要先升级，然后才能获得对函数应用的支持。


## <a name="retirement-policy-exceptions"></a>停用策略例外情况

上述停用策略存在几种例外情况。 在我们另行通告之前，下面列出的语言即使即将达到或者已经达到其生命周期终结日期，也仍在平台上受支持。 当这些语言版本达到其生命周期终结日期时，将不再对其进行更新或修补。 因此，我们不建议在这些语言版本中开发和运行函数应用。

|语言版本                        |EOL 日期         |停用日期|
|-----------------------------------------|-----------------|----------------|
|.NET 5|2022 年 2 月|TBA|
|Node 6|2019 年 4 月 30 日|2022 年 2 月 28 日| 
|Node 8|2019 年 12 月 31 日|2022 年 2 月 28 日| 
|Node 10|2021 年 4 月 30 日|2022 年 9 月 30 日| 
|PowerShell Core 6| 2020 年 9 月 4 日|2022 年 9 月 30 日|
|Python 3.6 |2021 年 12 月 23 日|2022 年 9 月 30 日| 
 

## <a name="language-version-support-timeline"></a>语言版本支持时间表

若要详细了解特定语言版本的支持策略时间表，请访问以下外部资源：
* .NET - [dotnet.microsoft.com](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)
* Node - [github.com](https://github.com/nodejs/Release#release-schedule)
* Java - [azul.com](https://www.azul.com/products/azul-support-roadmap/)
* PowerShell - [dotnet.microsoft.com](/powershell/scripting/powershell-support-lifecycle?view=powershell-7.1&preserve-view=true#powershell-releases-end-of-life)
* Python - [devguide.python.org](https://devguide.python.org/#status-of-python-branches)

## <a name="configuring-language-versions"></a>配置语言版本

|语言                         | 配置指南         |
|-----------------------------------------|-----------------|
|C#（类库） |[链接](./functions-dotnet-class-library.md#supported-versions)|
|节点 |[链接](./functions-reference-node.md#setting-the-node-version)|
|PowerShell |[链接](./functions-reference-powershell.md#changing-the-powershell-version)|
|Python |[链接](./functions-reference-python.md#python-version)|
 

## <a name="next-steps"></a>后续步骤

若要详细了解如何升级函数应用语言版本，请参阅以下资源：


+ [当前支持的语言版本](./supported-languages.md#languages-by-runtime-version)
