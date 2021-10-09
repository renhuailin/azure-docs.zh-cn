---
title: 适用于 Azure Functions 的 Azure CLI 示例 | Microsoft Docs
description: 找到使用 Azure CLI 的 Azure Functions 的 bash 脚本的链接。 了解如何创建允许集成和部署的函数应用。
ms.assetid: 577d2f13-de4d-40d2-9dfc-86ecc79f3ab0
ms.topic: sample
ms.date: 09/17/2021
ms.custom: mvc, devx-track-azurecli, seo-azure-cli
keywords: functions, azure cli 示例, azure cli 示例, azure cli 代码示例
ms.openlocfilehash: 8f6f5e45be61746cf33bf79e144403631892da9d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128618697"
---
# <a name="azure-cli-samples"></a>Azure CLI 示例

下表包含指向 Azure Functions 的 bash 脚本的链接，这些脚本使用 Azure CLI。

<a id="create"></a>

| 创建应用 | 说明 |
|---|---|
| [创建可无服务器执行的 Function App](scripts/functions-cli-create-serverless.md) | 在消耗计划中创建函数应用。  |
| [创建无服务器 Python 函数应用](scripts/functions-cli-create-serverless-python.md) | 在消耗计划中创建 Python 函数应用。 |
| [在可缩放的高级计划中创建函数应用](scripts/functions-cli-create-premium-plan.md) | 在高级计划中创建函数应用。 |
| [在专用（应用服务）计划中创建函数应用](scripts/functions-cli-create-app-service-plan.md) | 在专用应用服务计划中创建 Function App。 |

| 集成 | 说明|
|---|---|
| [创建 Function App 并连接到存储帐户](scripts/functions-cli-create-function-app-connect-to-storage-account.md) | 创建 Function App 并将其连接到存储帐户。 |
| [创建 Function App 并连接到 Azure Cosmos DB](scripts/functions-cli-create-function-app-connect-to-cosmos-db.md) | 创建 Function App 并将其连接到 Azure Cosmos DB。 |
| [创建 Python 函数应用并装载 Azure 文件共享](scripts/functions-cli-mount-files-storage-linux.md) | 通过将共享装载到 Linux 函数应用，可以利用现有的机器学习模型或函数中的其他数据。 | 

| 连续部署 | 说明|
|---|---|
| [从 GitHub 进行部署](scripts/functions-cli-create-function-app-github-continuous.md) | 创建从 GitHub 存储库部署的 Function App。  |
| [从 Azure DevOps 部署](scripts/functions-cli-create-function-app-vsts-continuous.md) | 创建从 Azure DevOps 存储库部署的函数应用。  |
