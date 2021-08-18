---
title: 了解设备模型存储库的概念 |Microsoft Docs
description: 作为解决方案开发人员或 IT 专业人员，请了解设备模型存储库的基本概念。
author: rido-min
ms.author: rmpablos
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: 5a9a2126d8732a2923428efb7e58cc6ec45e9fa5
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2021
ms.locfileid: "114406319"
---
# <a name="device-models-repository"></a>设备模型存储库

借助设备模型存储库 (DMR)，设备构建者可以管理和共享 IoT 即插即用设备模型。 设备模型是使用[数字孪生建模语言 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 定义的 JSON LD 文档。

DMR 定义了一个模式，用于根据设备孪生模型标识符 (DTMI) 在文件夹结构中存储 DTDL 接口。 可以通过将 DTMI 转换为相对路径，在 DMR 中查找接口。 例如，将 `dtmi:com:example:Thermostat;1` DTMI 转换为 `/dtmi/com/example/thermostat-1.json`。

## <a name="public-device-models-repository"></a>公共设备模型存储库

Microsoft 托管着具有以下特征的公共 DMR：

- 特选模型。 Microsoft 使用 GitHub 拉取请求 (PR) 验证工作流来查看和批准所有可用接口。
- 不可变性。  接口发布后则无法更新。
- 超大规模。 Microsoft 提供所需的基础结构来创建安全、可缩放的终结点，从中可以发布和使用设备型号。

## <a name="custom-device-models-repository"></a>自定义设备模型存储库

使用相同的 DMR 模式在任何存储介质（例如本地文件系统或自定义 HTTP web 服务器）中创建自定义 DMR。 通过更改用于访问 DMR 的基 URL，可以从自定义 DMR 中检索设备模型，方法与从公共 DMR 检索设备模型的方式相同。

> [!NOTE]
> Microsoft 提供用于验证公共 DMR 中设备模型的工具。 可以在自定义存储库中重复使用这些工具。

## <a name="public-models"></a>公共模型

人人都可在其应用程序中使用和集成模型存储库中存储的公共设备模型。 公用设备模型为设备构建者和解决方案开发人员提供了一种开放的生态系统，以便共享和重复使用其 IoT 即插即用设备模型。

有关如何在模型存储库中发布公共模型的说明，请参阅[发布模型](#publish-a-model)部分。

用户可以浏览、搜索和查看官方 [GitHub 存储库](https://github.com/Azure/iot-plugandplay-models)中的公共接口。

此外，还可以通过公共终结点 [https://devicemodels.azure.com](https://devicemodels.azure.com) 使用 `dtmi` 文件夹中的所有接口

### <a name="resolve-models"></a>解析模型

要以编程方式访问这些接口，可以使用 NuGet 包 [Azure.IoT.ModelsRepository](https://www.nuget.org/packages/Azure.IoT.ModelsRepository) 中提供的 `ModelsRepositoryClient`。 默认情况下，此客户端配置为查询 [devicemodels.azure.com](https://devicemodels.azure.com/) 可用的公共 DMR，也可配置为任何自定义存储库。

客户端接受 `DTMI` 作为输入，并返回包含所有必需接口的字典：

```cs
using Azure.IoT.ModelsRepository;

var client = new ModelsRepositoryClient();
IDictionary<string, string> models = client.GetModels("dtmi:com:example:TemperatureController;1");
models.Keys.ToList().ForEach(k => Console.WriteLine(k));
```

预期输出应显示在依赖项链中找到的三个接口的 `DTMI`：

```txt
dtmi:com:example:TemperatureController;1
dtmi:com:example:Thermostat;1
dtmi:azure:DeviceManagement:DeviceInformation;1
```

可以将 `ModelsRepositoryClient` 配置为查询自定义模型存储库（可通过 http (s) 获取），并使用任何可用的 `ModelDependencyResolution` 来指定依赖项解析：

- 已禁用。 仅返回指定的接口，没有任何依赖项。
- 已启用。 返回依赖项链中的所有接口
- TryFromExpanded。 使用 `.expanded.json` 文件检索已预先计算的依赖项 

> [!Tip] 
> 自定义存储库可能不会公开 `.expanded.json` 文件，当不可用时，客户端将回退，从而在本地处理每个依赖项。

下一个示例代码演示如何使用自定义存储库基 URL 初始化 `ModelsRepositoryClient`，在这种情况下是指使用 GitHub API 中的 `raw` URL，而不是 `expanded` 窗体（因为它在 `raw` 终结点中不可用）。 `AzureEventSourceListener` 将执行初始化，以检查客户端执行的 HTTP 请求：

```cs
using AzureEventSourceListener listener = AzureEventSourceListener.CreateConsoleLogger();

var client = new ModelsRepositoryClient(
    new Uri("https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main"),
    new ModelsRepositoryClientOptions(dependencyResolution: ModelDependencyResolution.Enabled));

IDictionary<string, string> models = client.GetModels("dtmi:com:example:TemperatureController;1");

models.Keys.ToList().ForEach(k => Console.WriteLine(k));
```

有关更多示例，请参阅 Azure SDK GitHub 存储库中的源代码：[Azure.Iot.ModelsRepository/samples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/modelsrepository/Azure.IoT.ModelsRepository/samples)

## <a name="publish-a-model"></a>发布模型

> [!Important]
> 要向公共 DMR 中提交模型，必须具有 GitHub 帐户。

1. 创建公共 GitHub 存储库分支：[https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models)。
1. 克隆已创建分支的存储库。 （可选）创建一个新分支，使更改与 `main` 分支隔离。
1. 按照文件夹/文件名约定向 `dtmi` 文件夹中添加新接口。 有关详细信息，请参阅将[将模型导入到 `dtmi/` 文件夹](#import-a-model-to-the-dtmi-folder)。
1. 使用 `dmr-client` 工具对模型进行本地验证。 有关详细信息，请参阅[验证模型](#validate-models)。
1. 在本地提交更改并将其推送到你的分支。
1. 在分支中，创建一个目标为 `main` 分支的拉取请求。 请参阅[创建问题或拉取请求](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request)文档。
1. 查看[拉取请求要求](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md)。

拉取请求会触发一系列 GitHub 操作来验证已提交的接口，并确保拉取请求满足所有要求。

Microsoft 将在三个工作日内完成所有检查来响应拉取请求。

### <a name="dmr-client-tools"></a>`dmr-client` 工具

在 PR 检查期间用于验证模型的工具，也可用于在本地添加和验证 DTDL 接口。

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

如果已将模型存储在 json 文件中，则可以使用 `dmr-client import` 命令将它们添加到文件名正确的 `dtmi/` 文件夹中：

```bash
# from the local repo root folder
dmr-client import --model-file "MyThermostat.json"
```

> [!TIP]
> 可以使用 `--local-repo` 参数来指定本地存储库根文件夹。

### <a name="validate-models"></a>验证模型

可以通过 `dmr-client validate` 命令验证模型：

```bash
dmr-client validate --model-file ./my/model/file.json
```

> [!NOTE]
> 验证将使用最新的 DTDL 分析程序版本来确保所有接口都与 DTDL 语言规范兼容。

要验证外部依赖项，它们必须位于本地存储库中。 要验证模型，请使用 `--repo` 选项来指定 `local` 或 `remote` 文件夹以解析依赖项：

```bash
# from the repo root folder
dmr-client validate --model-file ./my/model/file.json --repo .
```

### <a name="strict-validation"></a>严格验证

DMR 包括其他[要求](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md)，请使用 `stict` 标记来验证模型的以下项目：

```bash
dmr-client validate --model-file ./my/model/file.json --repo . --strict true
```

检查控制台输出中有无任何错误消息。

### <a name="export-models"></a>导出模型

可以使用 JSON 数组将模型从指定存储库（本地或远程）导出到单个文件：

```bash
dmr-client export --dtmi "dtmi:com:example:TemperatureController;1" -o TemperatureController.expanded.json
```

## <a name="next-steps"></a>后续步骤

建议接下来查看 [IoT 即插即用体系结构](concepts-architecture.md)。
