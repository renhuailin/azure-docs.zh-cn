---
title: Azure CLI 脚本示例 - 创建逻辑应用
description: 通过 Azure CLI 中的逻辑应用扩展创建逻辑应用的示例脚本。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc, devx-track-azurecli
ms.date: 07/30/2020
ms.openlocfilehash: 8086ce0e5964d4e37a5ffc3082d5f2856058e4e5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875478"
---
# <a name="azure-cli-script-sample---create-a-logic-app"></a>Azure CLI 脚本示例 - 创建逻辑应用

此脚本通过 [Azure CLI 逻辑应用扩展](/cli/azure/logic) (`az logic`) 创建一个示例逻辑应用。 有关通过 Azure CLI 创建和管理逻辑应用的详细指南，请参阅 [Azure CLI 的逻辑应用快速入门](quickstart-logic-apps-azure-cli.md)。

> [!WARNING]
> Azure CLI 逻辑应用扩展当前是试验性的，未涵盖在客户支持中 。 请谨慎使用此 CLI 扩展，尤其是在生产环境中选择使用扩展时。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 如果没有 Azure 订阅，可以[创建一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 在本地计算机上安装的 [Azure CLI](/cli/azure/install-azure-cli)。
* 在计算机上安装的[逻辑应用 Azure CLI 扩展](/cli/azure/azure-cli-extensions-list)。 若要安装此扩展，请使用此命令：`az extension add --name logic`
* 逻辑应用的[工作流定义](quickstart-logic-apps-azure-cli.md#workflow-definition)。 此 JSON 文件必须遵循[工作流定义语言架构](logic-apps-workflow-definition-language.md)。
* 通过与逻辑应用相同的资源组中支持的[逻辑应用连接器](../connectors/apis-list.md)建立与电子邮件帐户的 API 连接。 此示例使用 [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md) 连接器，但你也可以使用其他连接器（如 [Outlook.com](../connectors/connectors-create-api-outlook.md)）。

### <a name="prerequisite-check"></a>先决条件检查

开始之前验证环境：

* 登录 Azure 门户，并通过运行 `az login` 来检查订阅是否处于活动状态。

* 通过运行 `az --version` 在终端或命令窗口中检查 Azure CLI 版本。 有关最新版本，请参阅[最新发行说明](/cli/azure/release-notes-azure-cli)。

  * 如果没有最新版本，请按照[适用于你操作系统或平台的安装指南](/cli/azure/install-azure-cli)来更新安装。

### <a name="sample-workflow-explanation"></a>示例工作流说明

此示例工作流定义文件创建了与 [Azure 门户的逻辑应用快速入门](quickstart-create-first-logic-app-workflow.md)相同的基本逻辑应用。 

此示例工作流： 

1. 为逻辑应用指定架构 `$schema`。

1. 在触发器列表中为逻辑应用定义一个触发器 `triggers`。 该触发器每 3 小时重复一次 (`recurrence`)。 在为指定的 RSS 源 (`feedUrl`) 发布新源项 (`When_a_feed_item_is_published`) 时，将触发该操作。

1. 在操作列表中为逻辑应用定义一个操作 `actions`。 该操作通过 Microsoft 365 发送一封电子邮件 (`Send_an_email_(V2)`)，其中包含该操作输入 (`inputs`) 的正文部分 (`body`) 中指定的来自 RSS 源项的详细信息。

## <a name="sample-workflow-definition"></a>示例工作流定义

在运行示例脚本之前，必须先创建一个示例[工作流定义](#prerequisites)。

1. 在计算机上创建 JSON 文件 `testDefinition.json`。 

1. 将以下内容复制到 JSON 文件中： 
    ```json
    
    {
        "definition": {
            "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
            "actions": {
                "Send_an_email_(V2)": {
                    "inputs": {
                        "body": {
                            "Body": "<p>@{triggerBody()?['publishDate']}<br>\n@{triggerBody()?['title']}<br>\n@{triggerBody()?['primaryLink']}</p>",
                            "Subject": "@triggerBody()?['title']",
                            "To": "test@example.com"
                        },
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['office365']['connectionId']"
                            }
                        },
                        "method": "post",
                        "path": "/v2/Mail"
                    },
                    "runAfter": {},
                    "type": "ApiConnection"
                }
            },
            "contentVersion": "1.0.0.0",
            "outputs": {},
            "parameters": {
                "$connections": {
                    "defaultValue": {},
                    "type": "Object"
                }
            },
            "triggers": {
                "When_a_feed_item_is_published": {
                    "inputs": {
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['rss']['connectionId']"
                            }
                        },
                        "method": "get",
                        "path": "/OnNewFeed",
                        "queries": {
                            "feedUrl": "https://www.pbs.org/now/rss.xml"
                        }
                    },
                    "recurrence": {
                        "frequency": "Hour",
                        "interval": 3
                    },
                    "splitOn": "@triggerBody()?['value']",
                    "type": "ApiConnection"
                }
            }
        },
        "parameters": {
            "$connections": {
                "value": {
                    "office365": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/office365",
                        "connectionName": "office365",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/office365"
                    },
                    "rss": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/rss",
                        "connectionName": "rss",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/rss"
                    }
                }
            }
        }
    }
    
    ```

1. 将占位符值更新为你自己的信息：

    1. 替换占位符电子邮件地址 (`"To": "test@example.com"`)。 需要使用与逻辑应用连接器兼容的电子邮件地址。 有关详细信息，请参阅[先决条件](#prerequisites)。

    1. 如果你使用 Office 365 Outlook 连接器外的其他电子邮件连接器，请替换其他连接器详细信息。

    1. 将连接参数 (`$connections`) 下连接标识符（`connectionId` 和 `id`）的占位符订阅值 (`00000000-0000-0000-0000-000000000000`) 替换为你自己的订阅值。

1. 保存所做更改。

## <a name="sample-script"></a>示例脚本

> [!NOTE]
> 此示例针对 `bash` shell 编写而成。 如果要在另一个 shell（例如 Windows PowerShell 或命令提示符）中运行此示例，可能需要对脚本进行修改。

运行此示例脚本之前，请运行此命令以连接到 Azure：

```azurecli-interactive

az login

```

接下来，导航到在其中创建了工作流定义的目录。 例如，如果在桌面上创建了工作流定义 JSON 文件：

```azurecli

cd ~/Desktop

```

然后，运行此脚本以创建逻辑应用。 

```azurecli-interactive

#!/bin/bash

# Create a resource group

az group create --name testResourceGroup --location westus

# Create your logic app

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

### <a name="clean-up-deployment"></a>清理部署

使用完示例脚本后，运行以下命令以删除资源组及其所有嵌套资源，包括逻辑应用。

```azurecli-interactive

az group delete --name testResourceGroup --yes

```

## <a name="script-explanation"></a>脚本说明

此示例脚本使用以下命令创建新的资源组和逻辑应用。

| 命令 | 说明 |
| ------- | ----- |
| [`az group create`](/cli/azure/group#az_group_create) | 创建存储逻辑应用资源的资源组。 |
| [`az logic workflow create`](/cli/azure/logic/workflow#az_logic_workflow_create) | 基于参数 `--definition` 中定义的工作流创建逻辑应用。 |
| [`az group delete`](/cli/azure/vm/extension) | 删除资源组及其所有嵌套资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure/)。

可以在 [Microsoft 代码示例浏览器](/samples/browse/?products=azure-logic-apps)中找到其他逻辑应用 CLI 脚本示例。
