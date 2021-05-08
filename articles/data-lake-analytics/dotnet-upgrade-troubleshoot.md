---
title: 如何排查由于 .NET Framework 4.7.2 升级导致的 Azure Data Lake Analytics U-SQL 作业失败问题
description: 排查由于升级到 .NET Framework 4.7.2 而导致的 U-SQL 作业失败问题。
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.date: 10/11/2019
ms.openlocfilehash: ab03ea8a88187289f5dce55f8a396a9d51346a3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92217671"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>Azure Data Lake Analytics 将升级到 .NET Framework v4.7.2

Azure Data Lake Analytics 默认运行时将从 .NET Framework v4.5.2 升级到 .NET Framework v4.7.2。 如果你的 U-SQL 代码使用自定义程序集，并且这些自定义程序集使用 .NET 库，则此变更会带来一个小的中断性变更风险。

从 .NET Framework 4.5.2 到版本 4.7.2 的这一升级意味着在 U-SQL 运行时（默认运行时）中部署的 .NET Framework 现在将始终是 4.7.2。 不存在 .NET Framework 版本的并行选项。

完成这个到 .NET Framework 4.7.2 的升级后，系统的托管代码将以版本 4.7.2 运行，用户提供的库（例如 U-SQL 自定义程序集）将在后向兼容模式下运行，该模式适用于生成程序集时针对的版本。

- 如果你的程序集 DLL 是针对版本 4.5.2 生成的，则所部署的框架会将其视为 4.5.2 库，提供 4.5.2 语义（有几个例外）。
- 如果你以 .NET Framework 4.7.2 作为目标，则现在可以利用那些使用 4.7.2 版功能的 U-SQL 自定义程序集。

由于这个升级（升级到 .NET Framework 4.7.2），可能会将中断性变更引入那些使用 .NET 自定义程序集的 U-SQL 作业。 建议使用以下过程检查是否存在后向兼容性问题。

## <a name="how-to-check-for-backwards-compatibility-issues"></a>如何检查后向兼容性问题

通过对你的 U-SQL 自定义程序集中的 .NET 代码运行 .NET 兼容性检查，检查是否可能存在后向兼容性中断性问题。

> [!Note]
> 该工具不会检测实际的中断性变更。 它仅查明所调用的可能会导致问题的 .NET API（对于某些输入而言）。 如果你收到有关问题的通知，你的代码可能仍然会正常运行，但是你应该进行更细致的检查。

1. 通过以下方式对 .NET DLL 运行后向兼容性检查器：
   1. 使用 [.NET Portability Analyzer Visual Studio 扩展](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)中的 Visual Studio 扩展
   1. 下载并使用 [GitHub dotnetapiport](https://github.com/microsoft/dotnet-apiport) 中的独立工具。 有关运行独立工具的说明，请参阅 [GitHub dotnetapiport 中断性变更](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md)
   1. 对于 4.7.2 兼 容性，`read isRetargeting == True` 表明可能存在问题。
2. 如果该工具指示你的代码可能受到某个可能的后向不兼容性问题（下面列出了一些常见的不兼容性示例）的影响，则可通过以下方式进一步进行检查：
   1. 分析你的代码并确定代码是否在将值传递给受影响的 API
   1. 执行运行时检查。 ADLA 中不会并行执行运行时部署。 在升级之前，你可以对一个具有代表性的数据集使用 VisualStudio 的本地运行和本地 .NET Framework 4.7.2 来执行运行时检查。
3. 如果你确实受到后向不兼容性问题的影响，请采取必要的步骤来修复它（例如修复数据或代码逻辑）。

在大多数情况下，你应该不会受后向不兼容性问题的影响。

## <a name="timeline"></a>时间线

你可以在这里检查新运行时的部署：[运行时排除故障](runtime-troubleshoot.md)，并查看以前成功的作业。

### <a name="what-if-i-cant-get-my-code-reviewed-in-time"></a>如果我不能及时检查代码，该怎么办？

你可以针对旧的运行时版本（以 4.5.2 作为目标构建）提交作业，但是由于缺少 .NET Framework 并行功能，它仍然只能在 4.5.2 兼容模式下运行。 由于存在此行为，你可能仍会遇到一些后向兼容性问题。

### <a name="what-are-the-most-common-backwards-compatibility-issues-you-may-encounter"></a>你可能会遇到的最常见后向兼容性问题是什么

可以通过检查器来查明的最常见后向不兼容性问题包括（我们通过对自己的内部 ADLA 作业运行检查器生成了此列表）：哪些库受到影响（注意：你只能间接调用这些库，因此必须执行操作 1 来检查作业是否受影响），以及可能的补救操作。 注意：在我们自己的作业中，几乎在所有情况下，由于大多数中断性变更的范围有限，警告都是误报。

- IAsyncResult.CompletedSynchronously 属性必须正确，才能完成生成的任务
  - 在调用 TaskFactory.FromAsync 时，必须正确实现 IAsyncResult.CompletedSynchronously 属性，才能完成生成的任务。 即，当且仅当同步完成实现时，该属性才应返回 true。 之前，属性未被选中。
  - 受影响的库：mscorlib、System.Threading.Tasks
  - 建议的操作：确保 TaskFactory.FromAsync 正确返回 true

- DataObject.GetData 现在将数据检索为 UTF-8
  - 对于面向 .NET Framework 4 或者在 .NET Framework 4.5.1 或早期版本上运行的应用，DataObject.GetData 将 HTML 格式的数据检索为 ASCII 字符串。 因此，非 ASCII 字符（ASCII 代码大于 0x7F 的字符）由两个随机字符表示。#N##N#对于面向 .NET Framework 4.5 或更高版本并在 .NET Framework 4.5.2 上运行的应用，`DataObject.GetData` 将按 UTF-8 检索 HTML 格式的数据，从而可以正确表示大于 0x7F 的字符。
  - 受影响的库：Glo
  - 建议的操作：确保检索的数据采用你需要的格式

- XmlWriter 引发无效的代理项对
  - 对于面向 .NET Framework 4.5.2 或以前的版本的应用程序，使用异常回退处理编写无效的代理项对并不会总是引发异常。 对于面向 .NET Framework 4.6 的应用，尝试编写无效的代理项对会引发 `ArgumentException`。
  - 受影响的库：System.Xml、System.Xml.ReaderWriter
  - 建议的操作：确保未编写会导致参数异常的无效代理项对

- HtmlTextWriter 未正确呈现 `<br/>` 元素
  - 从 .NET Framework 4.6 开始，调用带有 `<BR />` 的 `HtmlTextWriter.RenderBeginTag()` 和 `HtmlTextWriter.RenderEndTag()` 将正确插入唯一 `<BR />`（而非两个）
  - 受影响的库：System.Web
  - 建议的操作：确保插入的 `<BR />` 数量符合你的预期，这样在生产作业中就不会出现任何随机行为

- 调用具有 null 自变量的 CreateDefaultAuthorizationContext 的方式已更改
  - 调用具有 null authorizationPolicies 自变量的 `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)` 所返回的 AuthorizationContext 实现更改了其在 .NET Framework 4.6 中的实现。
  - 受影响的库：System.IdentityModel
  - 建议的操作：确保在存在空的授权策略时处理新的预期行为
  
- RSACng 现在可正确加载非标准密钥大小的 RSA 密钥
  - 在 .NET Framework 4.6.2 之前的版本中，使用非标准密钥大小的 RSA 证书的客户无法通过 `GetRSAPublicKey()` 和 `GetRSAPrivateKey()` 扩展方法访问这些密钥。 将引发 `CryptographicException` 并显示消息“不支持所请求的密钥大小”。 .NET Framework 4.6.2 中已修复此问题。 同样，`RSA.ImportParameters()` 和 `RSACng.ImportParameters()` 现在可以处理非标准密钥大小，而不会引发 `CryptographicException`。
  - 受影响的库：mscorlib、System.Core
  - 建议的操作：确保 RSA 密钥按预期方式工作

- 路径冒号检查更严格
  - 在 .NET Framework 4.6.2 中，为了支持以前不受支持的路径，进行了大量更改（无论是在长度方面还是在格式方面）。 检查正确的驱动器分隔符（冒号）语法变得更加严格，这样做的副作用是阻止了少量特选路径 API 中的某些 URI 路径，这些曾经是可以容忍的。
  - 受影响的库：mscorlib、System.Runtime.Extensions
  - 建议的操作：

- 调用 ClaimsIdentity 构造函数
  - 从 .NET Framework 4.6.2 开始，具有 `T:System.Security.Principal.IIdentity` 参数的 `T:System.Security.Claims.ClaimsIdentity` 构造函数设置 `P:System.Security.Claims.ClaimsIdentify.Actor` 属性的方式发生了变化。 如果 `T:System.Security.Principal.IIdentity` 参数是 `T:System.Security.Claims.ClaimsIdentity` 对象，且该 `T:System.Security.Claims.ClaimsIdentity` 对象的 `P:System.Security.Claims.ClaimsIdentify.Actor` 属性不为 `null`，则 `P:System.Security.Claims.ClaimsIdentify.Actor` 属性是使用 `M:System.Security.Claims.ClaimsIdentity.Clone` 方法附加的。 在 Framework 4.6.1 及更低版本中，`P:System.Security.Claims.ClaimsIdentify.Actor` 属性作为现有引用进行附加。 由于此变更，从 .NET Framework 4.6.2 开始，新的 `T:System.Security.Claims.ClaimsIdentity` 对象的 `P:System.Security.Claims.ClaimsIdentify.Actor` 属性不等于构造函数的 `T:System.Security.Principal.IIdentity` 参数的 `P:System.Security.Claims.ClaimsIdentify.Actor` 属性。 在 .NET Framework 4.6.1 及更早版本中，它们是相等的。
  - 受影响的库：mscorlib
  - 建议的操作：确保 ClaimsIdentity 在新运行时上按预期方式工作

- 使用 DataContractJsonSerializer 控制字符的序列化现在与 ECMAScript V6 和 V8 兼容
  - 在 .NET Framework 4.6.2 及更低版本中，DataContractJsonSerializer 未按与 ECMAScript V6 和 V8 标准兼容的方式对一些特殊控制字符（如 \b、\f 和 \t）进行序列化。 从 .NET Framework 4.7 开始，这些控制字符的序列化与 ECMAScript V6 和 V8 兼容。
  - 受影响的库：System.Runtime.Serialization.Json
  - 建议的操作：确保在使用 DataContractJsonSerializer 时具有相同的行为
