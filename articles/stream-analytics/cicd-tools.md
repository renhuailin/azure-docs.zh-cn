---
title: 使用 CI/CD 工具自动生成、测试和部署 Azure 流分析作业
description: 本文介绍如何使用 Azure 流分析 CI/CD 工具自动生成、测试和部署 Azure 流分析项目。
services: stream-analytics
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/29/2021
ms.openlocfilehash: 29105070ea3dfb0a41db194e03095b2611a08393
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113110630"
---
# <a name="automate-builds-tests-and-deployments-of-an-azure-stream-analytics-job-using-cicd-tools"></a>使用 CI/CD 工具自动生成、测试和部署 Azure 流分析作业

可使用 Azure 流分析 CI/CD npm 包自动生成、测试和部署 Azure 流分析 Visual Studio Code 或 Visual Studio 项目。 可使用开发工具创建项目，也可从现有的流分析作业中导出这些项目。 本文介绍如何在任何 CI/CD 系统中使用 npm 包。 有关使用 Azure Pipelines 进行的部署，请查看[使用 Azure DevOps 为流分析作业创建 CI/CD 管道](set-up-cicd-pipeline.md)。

## <a name="installation"></a>安装

可直接[下载包](https://www.npmjs.com/package/azure-streamanalytics-cicd)，也可使用 `npm install -g azure-streamanalytics-cicd` 命令[在全局](https://docs.npmjs.com/downloading-and-installing-packages-globally)安装它。 建议使用命令，它还可在 Azure Pipelines 中的生成管道的 PowerShell 或 Azure CLI 脚本任务中使用。

## <a name="build-the-project"></a>生成项目

NPM 包 asa-streamanalytics-cicd 提供了用于生成流分析 [Visual Studio Code 项目](./quick-create-visual-studio-code.md)或 [Visual Studio 项目](stream-analytics-quick-create-vs.md)的 Azure 资源管理器模板的工具。 你也可在 Windows、macOS 和 Linux 上使用 npm 包，无需安装 Visual Studio Code 或 Visual Studio。

安装包后，请使用以下命令生成流分析项目。

```powershell
azure-streamanalytics-cicd build -project <projectFullPath> [-outputPath <outputPath>]
```

build 命令会执行关键字语法检查并输出 Azure 资源管理器模板。

| 参数 | 说明 |
|---|---|
| `-project` | asaproj.json 文件的绝对路径（针对 Visual Studio Code 项目），或者 [你的项目名称].asaproj 的绝对路径（针对 Visual Studio 项目） 。 |
| `-outputPath` | Azure 资源管理器模板的输出文件夹的路径。 如果未指定，则将模板放在当前目录中。 |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/samplejob.asaproj"
```

--- 

成功生成流分析项目后，它会在输出文件夹下生成以下两个文件：

* Azure 资源管理器模板文件

   `[ProjectName].JobTemplate.json`

* Azure 资源管理器参数文件

   `[ProjectName].JobTemplate.parameters.json`

parameters.json 文件中的默认参数来自 Visual Studio Code 或 Visual Studio 项目中的设置。 如果要部署到其他环境，请相应地替换参数。

所有凭据的默认值均为 null。 部署到 Azure 之前，必须先设置这些值。

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

若要将 Azure Data Lake Store Gen1 的托管标识用作输出接收器，需要在部署到 Azure 之前使用 PowerShell 提供对服务主体的访问权限。 了解有关如何[使用资源管理器模板部署具有托管标识的 ADLS Gen1](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment) 的详细信息。

## <a name="local-run"></a>本地运行

如果项目已指定本地输入文件，则可使用 `localrun` 命令在本地运行流分析脚本。

```powershell
azure-streamanalytics-cicd localrun -project <projectFullPath> [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| 参数 | 说明 |
|---|---|
| `-project` | asaproj.json 文件的路径（针对 Visual Studio Code 项目），或者 [你的项目名称].asaproj 的路径（针对 Visual Studio 项目） 。 |
| `-outputPath` | 输出文件夹的路径。 如果未指定，则将输出结果文件放在当前目录中。 |
| `-customCodeZipFilePath` | UDF 或反序列化程序（若使用）等 C# 自定义代码的 zip 文件的路径。 将 DLL 打包到一个 zip 文件中，并指定此路径。 |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```

---

> [!Note] 
> JavaScript UDF 仅适用于 Windows。

## <a name="automated-test"></a>自动测试

可使用 CI/CD npm 包配置和运行流分析脚本的自动测试。

### <a name="add-a-test-case"></a>添加测试用例

可在测试配置文件中查看测试用例。 若要开始，请使用 `addtestcase` 命令将测试用例模板添加到测试配置文件。 如果测试配置文件不存在，会默认创建一个。

```powershell
azure-streamanalytics-cicd addtestcase -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>]
```

| 参数 | 说明 |
|---|---|
| `-project` | asaproj.json 文件的路径（针对 Visual Studio Code 项目），或者 [你的项目名称].asaproj 的路径（针对 Visual Studio 项目） 。 |
| `-testConfigPath` | 测试配置文件的路径。 如果未指定，则会在 asaproj.json 文件的当前目录下的 \test 中搜索文件，默认文件名为 testConfig.json  。 如果不存在，则将创建一个新文件。 |

> [!NOTE]
> 生成的 testConfig.json 文件中的 `Script` 值仅用于提供上下文，不用于测试逻辑。 

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```
---

如果测试配置文件为空，会写入以下内容。 否则，测试用例会被添加到 TestCases 数组中。 必要的输入配置会根据输入配置文件自动填充（若存在）。 否则，将配置默认值。 在运行测试之前，必须指定每个输入和预期输出的 FilePath。 可手动修改配置。

如果希望测试验证忽略某个输出，请将预期输出的“必填”字段设置为 false 。

```json
{
  "Script": "",
  "TestCases": [
    {
      "Name": "Case 1",
      "Inputs": [
        {
          "InputAlias": [Input alias string],
          "Type": "Data Stream",
          "Format": "JSON",
          "FilePath": [Required],
          "ScriptType": "InputMock"
        }
      ],
      "ExpectedOutputs": [
        {
          "OutputAlias": [Output alias string],
          "FilePath": [Required],
          "Required": true
        }
      ]
    }
  ]
}
```

> [!NOTE]
> 目前，`ScriptType` 元素唯一允许的值为 `InputMock`，这也是默认值。 如果将其设置为任何其他值，则会忽略该值并使用默认值 (`InputMock`)。 

### <a name="run-a-unit-test"></a>运行单元测试

可使用以下命令为项目运行多个测试用例。 输出文件夹中会生成测试结果摘要。 如果测试全部通过，则进程退出并显示代码 0；如果发生异常，则进程退出并显示代码 -1；如果测试失败，则进程退出并显示代码 -2  。

```powershell
azure-streamanalytics-cicd test -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>] [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| 参数 | 说明 |
|---|---|
| `-project` | asaproj.json 文件的路径（针对 Visual Studio Code 项目），或者 [你的项目名称].asaproj 的路径（针对 Visual Studio 项目） 。 |
| `-testConfigPath` | 测试配置文件的路径。 如果未指定，则会在 asaproj.json 文件的当前目录下的 \test 中搜索文件，默认文件名为 testConfig.json  。
| `-outputPath` | 测试结果输出文件夹的路径。 如果未指定，则将输出结果文件放在当前目录中。 |
| `-customCodeZipFilePath` | UDF 或反序列化程序（若使用）等自定义代码的 zip 文件的路径。 |

完成所有测试后，会在输出文件夹中生成 JSON 格式的测试结果摘要。 摘要文件名为 testResultSummary.json。

```json
{
  "Total": (integer) total_number_of_test_cases,
  "Passed": (integer) number_of_passed_test_cases,
  "Failed": (integer) number_of_failed_test_cases,
  "Script": (string) absolute_path_to_asaql_file,
  "Results": [ (array) detailed_results_of_test_cases
    {
      "Name": (string) name_of_test_case,
      "Status": (integer) 0(passed)_or_1(failed),
      "Time": (string) time_span_of_running_test_case,
      "OutputMatched": [ (array) records_of_actual_outputs_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputNotEqual": [ (array) records_of_actual_outputs_not_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputMissing": [ (array) records_of_actual_outputs_missing
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": ""
        }
      ],
      "OutputUnexpected": [ (array) records_of_actual_outputs_unexpected
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": "",
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputUnrequired": [ (array) records_of_actual_outputs_unrequired_to_be_checked
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ]
    }
  ],
  "Time": (string) time_span_of_running_all_test_cases,
}
```

## <a name="deploy-to-azure"></a>部署到 Azure

可使用从生成管道生成的 Azure 资源管理器模板文件和参数文件[将作业部署到 Azure](../azure-resource-manager/templates/template-tutorial-use-parameter-file.md?tabs=azure-powershell#deploy-template)。

## <a name="next-steps"></a>后续步骤

* [Azure 流分析的持续集成和持续部署](cicd-overview.md)
* [使用 Azure Pipelines 为流分析作业设置 CI/CD 管道](set-up-cicd-pipeline.md)
