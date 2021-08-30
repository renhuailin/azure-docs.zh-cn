---
title: CLI (v2) 托管在线部署 YAML 架构
titleSuffix: Azure Machine Learning
description: CLI (v2) 托管在线部署 YAML 架构的参考文档。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: 98bf786206fc0a14de0e77b48bbd2b11eb17d3ac
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778393"
---
# <a name="cli-v2-managed-online-deployment-yaml-schema"></a>CLI (v2) 托管在线部署 YAML 架构

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="schema"></a>架构

源 JSON 架构可在 https://azuremlschemas.azureedge.net/latest/managedOnlineDeployment.schema.json 中找到。 为了便于使用，下面以 JSON 和 YAML 格式提供了架构。

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/azureml-examples-main/cli/.schemas/jsons/latest/managedOnlineDeployment.schema.json":::

# <a name="yaml"></a>[YAML](#tab/yaml)

:::code language="yaml" source="~/azureml-examples-main/cli/.schemas/yamls/latest/managedOnlineDeployment.schema.yml":::

---

## <a name="remarks"></a>备注

`az ml deployment` 命令可用于管理 Azure 机器学习部署。

## <a name="next-steps"></a>后续步骤

- [安装并使用 CLI (v2)](how-to-configure-cli.md)
