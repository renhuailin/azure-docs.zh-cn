---
title: 了解设备模型存储库的概念 |Microsoft Docs
description: 作为解决方案开发人员或 IT 专业人员，请了解有关设备型号存储库的基本概念。
author: rido-min
ms.author: rmpablos
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 1a58a2f69b9c6c6742c4b9daf32dd0e13341aac1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742137"
---
# <a name="device-models-repository"></a>设备型号存储库

设备模型存储库 (DMR) 使设备构建者能够管理和共享 IoT 即插即用设备型号。 设备模型是使用 [数字孪生建模语言 (DTDL) ](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)定义的 JSON LD 文档。

DMR 定义了一个模式，用于根据设备克隆模型标识符 (DTMI) 在文件夹结构中存储 DTDL 接口。 可以通过将 DTMI 转换为相对路径，在 DMR 中查找接口。 例如， `dtmi:com:example:Thermostat;1` DTMI 转换为 `/dtmi/com/example/thermostat-1.json` 。

## <a name="public-device-models-repository"></a>公用设备型号存储库

Microsoft 托管了具有以下特征的公共 DMR：

- 特选模型。 Microsoft 使用 GitHub 拉取请求 (PR) 验证工作流来查看和批准所有可用接口。
- 不可变性.  发布后，无法更新接口。
- 超大规模。 Microsoft 提供所需的基础结构来创建安全、可缩放的终结点，你可以在其中发布和使用设备型号。

## <a name="custom-device-models-repository"></a>自定义设备模型存储库

使用相同的 DMR 模式在任何存储介质（例如本地文件系统或自定义 HTTP web 服务器）中创建自定义 DMR。 可以通过更改用于访问 DMR 的基 URL，从自定义 DMR 中检索设备模型，方法与从公共 DMR 检索设备模型的方式相同。

> [!NOTE]
> Microsoft 提供了用于在公共 DMR 中验证设备模型的工具。 可以在自定义存储库中重复使用这些工具。

## <a name="public-models"></a>公共模型

存储在模型存储库中的公共设备模型可供所有人使用并集成到其应用程序中。 使用公用设备模型，设备构建者和解决方案开发人员可以使用开放的 eco 系统共享和重复使用其 IoT 即插即用设备型号。

有关如何在模型存储库中发布模型以使其公开的说明，请参阅 [发布模型](#publish-a-model) 部分。

用户可以浏览、搜索和查看官方 [GitHub 存储库](https://github.com/Azure/iot-plugandplay-models)中的公共接口。

`dtmi`还可以通过公共终结点使用文件夹中的所有接口[https://devicemodels.azure.com](https://devicemodels.azure.com)

### <a name="resolve-models"></a>解析模型

若要以编程方式访问这些接口，需要将 DTMI 转换为可用于查询公共终结点的相对路径。

若要将 DTMI 转换为绝对路径，请将 `DtmiToPath` 函数用于 `IsValidDtmi` ：

```cs
static string DtmiToPath(string dtmi)
{
    if (!IsValidDtmi(dtmi))
    {
        return null;
    }
    // dtmi:com:example:Thermostat;1 -> dtmi/com/example/thermostat-1.json
    return $"/{dtmi.ToLowerInvariant().Replace(":", "/").Replace(";", "-")}.json";
}

static bool IsValidDtmi(string dtmi)
{
    // Regex defined at https://github.com/Azure/digital-twin-model-identifier#validation-regular-expressions
    Regex rx = new Regex(@"^dtmi:[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?(?::[A-Za-z](?:[A-Za-z0-9_]*[A-Za-z0-9])?)*;[1-9][0-9]{0,8}$");
    return rx.IsMatch(dtmi);
}
```

对于存储库的结果路径和基 URL，我们可以获取接口：

```cs
const string _repositoryEndpoint = "https://devicemodels.azure.com";

string dtmiPath = DtmiToPath(dtmi.ToString());
string fullyQualifiedPath = $"{_repositoryEndpoint}{dtmiPath}";
string modelContent = await _httpClient.GetStringAsync(fullyQualifiedPath);
```

## <a name="publish-a-model"></a>发布模型

> [!Important]
> 您必须具有 GitHub 帐户才能向公用 DMR 提交模型。

1. 派生公共 GitHub 存储库： [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models) 。
1. 克隆分叉存储库。 （可选）创建一个新分支，使更改与 `main` 分支隔离。
1. `dtmi`使用文件夹/文件名约定将新接口添加到文件夹。 若要了解详细信息，请参阅将 [模型导入到 `dtmi/` 文件夹](#import-a-model-to-the-dtmi-folder)。
1. 使用工具在本地验证模型 `dmr-client` 。 若要了解详细信息，请参阅 [验证模型](#validate-models)。
1. 在本地提交更改并推送到你的分支。
1. 在分支中，创建一个面向分支的拉取请求 `main` 。 请参阅 [创建问题或](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request) 请求文档。
1. 查看 [拉取请求要求](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md)。

拉取请求会触发一组 GitHub 操作来验证已提交的接口，并确保拉取请求满足所有要求。

Microsoft 将在三个工作日内通过所有检查来响应拉取请求。

### <a name="dmr-client-tools"></a>`dmr-client` 工具包

在 PR 检查期间用于验证模型的工具也可用于在本地添加和验证 DTDL 接口。

> [!NOTE]
> 此工具需要 [.NET SDK](https://dotnet.microsoft.com/download) 3.1 版或更高版本。

### <a name="install-dmr-client"></a>安装 `dmr-client`

```bash
curl -L https://aka.ms/install-dmr-client-linux | bash
```

```powershell
iwr https://aka.ms/install-dmr-client-windows -UseBasicParsing | iex
```

### <a name="import-a-model-to-the-dtmi-folder"></a>将模型导入到 `dtmi/` 文件夹

如果已将模型存储在 json 文件中，则可以使用 `dmr-client import` 命令将它们添加到 `dtmi/` 具有正确文件名的文件夹中：

```bash
# from the local repo root folder
dmr-client import --model-file "MyThermostat.json"
```

> [!TIP]
> 可以使用 `--local-repo` 参数来指定本地存储库根文件夹。

### <a name="validate-models"></a>验证模型

可以通过命令验证模型 `dmr-client validate` ：

```bash
dmr-client validate --model-file ./my/model/file.json
```

> [!NOTE]
> 验证使用最新的 DTDL 分析器版本来确保所有接口都与 DTDL 语言规范兼容。

若要验证外部依赖项，它们必须位于本地存储库中。 若要验证模型，请使用 `--repo` 选项来指定 `local` 或 `remote` 文件夹以解析依赖项：

```bash
# from the repo root folder
dmr-client validate --model-file ./my/model/file.json --repo .
```

### <a name="strict-validation"></a>严格验证

DMR 包括其他 [要求](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md)，请使用 `stict` 标志来验证模型：

```bash
dmr-client validate --model-file ./my/model/file.json --repo . --strict true
```

检查控制台输出中是否有任何错误消息。

### <a name="export-models"></a>导出模型

可以使用 JSON 数组 (本地或远程) 从给定存储库导出到单个文件：

```bash
dmr-client export --dtmi "dtmi:com:example:TemperatureController;1" -o TemperatureController.expanded.json
```

## <a name="next-steps"></a>后续步骤

建议的下一步是查看 [IoT 即插即用的体系结构](concepts-architecture.md)。
