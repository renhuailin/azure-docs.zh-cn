---
title: CLI (v2) 环境 YAML 架构
titleSuffix: Azure Machine Learning
description: CLI (v2) 环境 YAML 架构的参考文档。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: 66c14b25fb60f76b015d461b7375deb595c954fb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779814"
---
# <a name="cli-v2-environment-yaml-schema"></a>CLI (v2) 环境 YAML 架构

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="schema"></a>架构

源 JSON 架构可在 https://azuremlschemas.azureedge.net/latest/environment.schema.json 中找到。 为了便于使用，下面以 JSON 和 YAML 格式提供了架构。

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/azureml-examples-main/cli/.schemas/jsons/latest/environment.schema.json":::

# <a name="yaml"></a>[YAML](#tab/yaml)

:::code language="yaml" source="~/azureml-examples-main/cli/.schemas/yamls/latest/environment.schema.yml":::

---

## <a name="remarks"></a>备注

`az ml environment` 命令可用于管理 Azure 机器学习环境。

## <a name="next-steps"></a>后续步骤

- [安装并使用 CLI (v2)](how-to-configure-cli.md)
