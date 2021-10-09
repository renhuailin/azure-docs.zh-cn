---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 09/13/2021
ms.openlocfilehash: 58f0c7bab2308912979d1a8dfc442f7af4d27591
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128548179"
---
在 Visual Studio Code 中，逻辑应用项目具有以下类型之一：

* 基于扩展捆绑包 (Node.js)（默认类型）
* 基于 NuGet 包 (.NET)（可从默认类型转换）

根据这些类型，项目包含略有不同的文件夹和文件。 基于 NuGet 的项目包含一个 .bin 文件夹，其中包含包和其他库文件。 基于捆绑包的项目不包括 .bin 文件夹和其他文件。 某些场景需要基于 NuGet 的项目以运行应用，例如，在想要开发和运行自定义内置操作时。 有关将项目转换为使用 NuGet 的详细信息，请参阅[启用内置连接器创作](../articles/logic-apps/create-single-tenant-workflows-visual-studio-code.md#enable-built-in-connector-authoring)。

默认的基于捆绑包的项目具有类型于以下示例的文件夹和文件结构：

```text
MyBundleBasedLogicAppProjectName
| .vscode
| Artifacts
  || Maps 
     ||| MapName1
     ||| ...
  || Schemas
     ||| SchemaName1
     ||| ...
| WorkflowName1
  || workflow.json
  || ...
| WorkflowName2
  || workflow.json
  || ...
| workflow-designtime
| .funcignore
| connections.json
| host.json
| local.settings.json
```

你可以在项目的根级别找到包含其他项的以下文件和文件夹：

| 名称 | 文件夹或文件 | 说明 |
|------|----------------|-------------|
| .vscode | 文件夹 | 包含与 Visual Studio Code 相关的设置文件，如 extensions.json、launch.json、settings.json 和 tasks.json 文件   。 |
| **项目** | 文件夹 | 包含在支持企业对企业 (B2B) 场景的工作流中定义和使用的集成帐户项目。 例如，示例结构包含用于 XML 转换和验证操作的映射和架构。 |
| <WorkflowName> | 文件夹 | 对于每个工作流，<WorkflowName> 文件夹包含 workflow.json 文件，其中包含该工作流的基础 JSON 定义。 |
| workflow-designtime | 文件夹 | 包含与开发环境相关的设置文件。 |
| .funcignore | 文件 | 包含与已安装的 [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) 相关的信息。 |
| connections.json | 文件 | 包含工作流使用的用于任何托管连接和 Azure 函数的元数据、终结点和密钥。 <p><p>重要说明：若要在各种环境中使用不同的连接和函数，请确保实现此 connections.js 文件的参数化并更新终结点。 |
| **host.json** | 文件 | 包含特定于运行时的配置设置和值，例如，单租户 Azure 逻辑应用平台、逻辑应用、工作流、触发器和操作的默认限制。 在逻辑应用项目的根级别，host.json 元数据文件包含配置设置和默认值，相同逻辑应用中的所有工作流会在运行时（无论是在本地还是在 Azure 中）使用这些配置设置和默认值。 <p>注意：创建逻辑应用时，Visual Studio Code 会在存储容器中创建一个备份 host.snapshot.*.json 文件 。 如果你删除逻辑应用，此备份文件不会被删除。 如果你创建另一个同名的逻辑应用，系统会创建另一个快照文件。 对于同一逻辑应用，最多只能有 10 个快照。 如果超出限制，你将收到以下错误： <p>`Microsoft.Azure.WebJobs.Script.WebHost: Repository has more than 10 non-decryptable secrets backups (host))` <p>若要解决此错误，请从存储容器中删除额外的快照文件。 |
| **local.settings.json** | 文件 | 包含工作流在本地运行时使用的应用设置、连接字符串和其他设置。 换句话说，这些设置和值仅在本地开发环境中运行项目时适用。 在部署到 Azure 期间，文件和设置将被忽略，并且不包含在部署中。 <p><p>此文件会将设置和值存储为本地环境变量，本地开发工具会将其用作 `appSettings` 值。 可以通过使用“应用设置”和“参数”在运行时和部署时调用和引用这些环境变量 。 <p><p>**重要说明**：local.settings.json 包含机密，因此请确保从项目源代码理管理中排除此文件。 |
||||