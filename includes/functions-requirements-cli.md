---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 0198bcdd34cd92822c779329affeb1a405264bae
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830489"
---
## <a name="configure-your-local-environment"></a>配置本地环境

在开始之前，必须满足以下条件：

::: zone pivot="programming-language-csharp"
[!INCLUDE [functions-cli-dotnet-prerequisites](functions-cli-dotnet-prerequisites.md)]
::: zone-end  
::: zone pivot="programming-language-java,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other" 
+ [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2)。

+ [Azure CLI](/cli/azure/install-azure-cli)，版本 2.4 或更高版本。 
:::zone-end  
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/)，活动 LTS 和维护 LTS 版本（建议使用 8.11.1 和 10.14.1）。
::: zone-end

::: zone pivot="programming-language-python"
+ Azure Functions 支持的 [Python 3.8（64 位）](https://www.python.org/downloads/release/python-382/)、[Python 3.7（64 位）](https://www.python.org/downloads/release/python-375/)、[Python 3.6（64 位）](https://www.python.org/downloads/release/python-368/)。 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Java 开发人员工具包](/azure/developer/java/fundamentals/java-jdk-long-term-support)版本 8 或 11。 

+ [Apache Maven](https://maven.apache.org) 3.0 或更高版本。

::: zone-end
::: zone pivot="programming-language-other"
+ 所用语言的开发工具。 本教程使用 [R 编程语言](https://www.r-project.org/)作为示例。
::: zone-end

还需要一个具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。