---
title: Azure Functions 入门
description: 实现使用 Azure Functions 的前几个步骤。
author: craigshoemaker
ms.topic: overview
ms.date: 11/19/2020
ms.author: cshoe
zone_pivot_groups: programming-languages-set-functions-lang-workers
ms.openlocfilehash: 559703401fa87980a5937e81eeab27ef44769c20
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113651472"
---
# <a name="getting-started-with-azure-functions"></a>Azure Functions 入门

## <a name="introduction"></a>简介

[Azure Functions](./functions-overview.md) 允许你将系统的逻辑实现为易于使用的代码块。 这些代码块称为“函数”。

从以下资源开始。

::: zone pivot="programming-language-csharp"

| 操作 | 资源 |
| --- | --- |
| **创建第一个函数** | 使用以下工具之一：<br><br><li>[Visual Studio](./functions-create-your-first-function-visual-studio.md)<li>[Visual Studio Code](./create-first-function-vs-code-csharp.md)<li>[命令行](./create-first-function-cli-csharp.md) |
| **查看正在运行的函数** | <li>[Azure 示例浏览器](/samples/browse/?expanded=azure&languages=csharp&products=azure-functions)<li>[Azure 社区库](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=C%23) |
| **了解交互式教程**| <li>[为业务场景选择最佳的 Azure 无服务器技术](/learn/modules/serverless-fundamentals/)<li>[结构良好的框架 - 性能效率](/learn/modules/azure-well-architected-performance-efficiency/)<li>[使用触发器执行 Azure 函数](/learn/modules/execute-azure-function-with-triggers/) <br><br>请参阅 Microsoft Learn，查看[一系列完整的交互式教程](/learn/browse/?expanded=azure&products=azure-functions)。|
| **查看最佳实践** |<li>[性能和可靠性](./functions-best-practices.md)<li>[管理连接](./manage-connections.md)<li>[错误处理和函数重试](./functions-bindings-error-pages.md?tabs=csharp)<li>[安全性](./security-concepts.md)|
| **深入了解详细信息** | <li>了解函数如何[自动增加或减少](./functions-scale.md)实例以满足需求<li>了解可用的各种[部署方法](./functions-deployment-technologies.md)<li>使用内置的[监视工具](./functions-monitoring.md)来帮助分析函数<li>阅读 [C# 语言参考](./functions-dotnet-class-library.md)|

::: zone-end

::: zone pivot="programming-language-java"
| 操作 | 资源 |
| --- | --- |
| **创建第一个函数** | 使用以下工具之一：<br><br><li>[Visual Studio Code](./create-first-function-vs-code-java.md)<li>[使用终端/命令提示符的 Java/Maven 函数](./create-first-function-cli-java.md)<li>[Gradle](./functions-create-first-java-gradle.md)<li>[Eclipse](./functions-create-maven-eclipse.md)<li>[IntelliJ IDEA](./functions-create-maven-intellij.md) |
| **查看正在运行的函数** | <li>[Azure 示例浏览器](/samples/browse/?expanded=azure&languages=java&products=azure-functions)<li>[Azure 社区库](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Java) |
| **了解交互式教程**| <li>[为业务场景选择最佳的 Azure 无服务器技术](/learn/modules/serverless-fundamentals/)<li>[结构良好的框架 - 性能效率](/learn/modules/azure-well-architected-performance-efficiency/)<li>[使用 Azure Functions 的 Maven 插件开发应用](/learn/modules/develop-azure-functions-app-with-maven-plugin/) <br><br>请参阅 Microsoft Learn，查看[一系列完整的交互式教程](/learn/browse/?expanded=azure&products=azure-functions)。|
| **查看最佳实践** |<li>[性能和可靠性](./functions-best-practices.md)<li>[管理连接](./manage-connections.md)<li>[错误处理和函数重试](./functions-bindings-error-pages.md?tabs=java)<li>[安全性](./security-concepts.md)|
| **深入了解详细信息** | <li>了解函数如何[自动增加或减少](./functions-scale.md)实例以满足需求<li>了解可用的各种[部署方法](./functions-deployment-technologies.md)<li>使用内置的[监视工具](./functions-monitoring.md)来帮助分析函数<li>阅读 [Java 语言参考](./functions-reference-java.md)|
::: zone-end

::: zone pivot="programming-language-javascript"
| 操作 | 资源 |
| --- | --- |
| **创建第一个函数** | 使用以下工具之一：<br><br><li>[Visual Studio Code](./create-first-function-vs-code-node.md)<li>[Node.js 终端/命令提示符](./create-first-function-cli-node.md) |
| **查看正在运行的函数** | <li>[Azure 示例浏览器](/samples/browse/?expanded=azure&languages=javascript%2ctypescript&products=azure-functions)<li>[Azure 社区库](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=JavaScript%2CTypeScript) |
| **了解交互式教程** | <li>[为业务场景选择最佳的 Azure 无服务器技术](/learn/modules/serverless-fundamentals/)<li>[结构良好的框架 - 性能效率](/learn/modules/azure-well-architected-performance-efficiency/)<li>[使用 Azure Functions 构建无服务器 API](/learn/modules/build-api-azure-functions/)<li>[使用 Azure Functions 创建无服务器逻辑](/learn/modules/create-serverless-logic-with-azure-functions/)<li>[使用 Azure Functions 将 Node.js 和 Express API 重构到无服务器 API](/learn/modules/shift-nodejs-express-apis-serverless/) <br><br>请参阅 Microsoft Learn，查看[一系列完整的交互式教程](/learn/browse/?expanded=azure&products=azure-functions)。|
| **查看最佳实践** |<li>[性能和可靠性](./functions-best-practices.md)<li>[管理连接](./manage-connections.md)<li>[错误处理和函数重试](./functions-bindings-error-pages.md?tabs=javascript)<li>[安全性](./security-concepts.md)|
| **深入了解详细信息** | <li>了解函数如何[自动增加或减少](./functions-scale.md)实例以满足需求<li>了解可用的各种[部署方法](./functions-deployment-technologies.md)<li>使用内置的[监视工具](./functions-monitoring.md)来帮助分析函数<li>阅读 [JavaScript](./functions-reference-node.md) 或 [TypeScript](./functions-reference-node.md#typescript) 语言参考|
::: zone-end

::: zone pivot="programming-language-powershell"
| 操作 | 资源 |
| --- | --- |
| **创建第一个函数** | <li>使用 [Visual Studio Code](./create-first-function-vs-code-powershell.md) |
| **查看正在运行的函数** | <li>[Azure 示例浏览器](/samples/browse/?expanded=azure&languages=powershell&products=azure-functions)<li>[Azure 社区库](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=PowerShell) |
| **了解交互式教程** | <li>[为业务场景选择最佳的 Azure 无服务器技术](/learn/modules/serverless-fundamentals/)<li>[结构良好的框架 - 性能效率](/learn/modules/azure-well-architected-performance-efficiency/)<li>[使用 Azure Functions 构建无服务器 API](/learn/modules/build-api-azure-functions/)<li>[使用 Azure Functions 创建无服务器逻辑](/learn/modules/create-serverless-logic-with-azure-functions/)<li>[使用触发器执行 Azure 函数](/learn/modules/execute-azure-function-with-triggers/) <br><br>请参阅 Microsoft Learn，查看[一系列完整的交互式教程](/learn/browse/?expanded=azure&products=azure-functions)。|
| **查看最佳实践** |<li>[性能和可靠性](./functions-best-practices.md)<li>[管理连接](./manage-connections.md)<li>[错误处理和函数重试](./functions-bindings-error-pages.md?tabs=powershell)<li>[安全性](./security-concepts.md)|
| **深入了解详细信息** | <li>了解函数如何[自动增加或减少](./functions-scale.md)实例以满足需求<li>了解可用的各种[部署方法](./functions-deployment-technologies.md)<li>使用内置的[监视工具](./functions-monitoring.md)来帮助分析函数<li>阅读 [PowerShell 语言参考](./functions-reference-powershell.md)）|
::: zone-end

::: zone pivot="programming-language-python"
| 操作 | 资源 |
| --- | --- |
| **创建第一个函数** | 使用以下工具之一：<br><br><li>[Visual Studio Code](./create-first-function-vs-code-python.md)<li>[终端/命令提示符](./create-first-function-cli-python.md) |
| **查看正在运行的函数** | <li>[Azure 示例浏览器](/samples/browse/?expanded=azure&languages=python&products=azure-functions)<li>[Azure 社区库](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Python) |
| **了解交互式教程** | <li>[为业务场景选择最佳的 Azure 无服务器技术](/learn/modules/serverless-fundamentals/)<li>[结构良好的框架 - 性能效率](/learn/modules/azure-well-architected-performance-efficiency/)<li>[使用 Azure Functions 构建无服务器 API](/learn/modules/build-api-azure-functions/)<li>[使用 Azure Functions 创建无服务器逻辑](/learn/modules/create-serverless-logic-with-azure-functions/) <br><br>请参阅 Microsoft Learn，查看[一系列完整的交互式教程](/learn/browse/?expanded=azure&products=azure-functions)。|
| **查看最佳实践** |<li>[性能和可靠性](./functions-best-practices.md)<li>[管理连接](./manage-connections.md)<li>[错误处理和函数重试](./functions-bindings-error-pages.md?tabs=python)<li>[安全性](./security-concepts.md)<li>[提高吞吐量性能](./python-scale-performance-reference.md)|
| **深入了解详细信息** | <li>了解函数如何[自动增加或减少](./functions-scale.md)实例以满足需求<li>了解可用的各种[部署方法](./functions-deployment-technologies.md)<li>使用内置的[监视工具](./functions-monitoring.md)来帮助分析函数<li>阅读 [Python 语言参考](./functions-reference-python.md)|
::: zone-end

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 Azure Functions 应用程序的剖析](./functions-reference.md)
