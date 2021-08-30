---
title: CLI (v2) 计算 YAML 架构
titleSuffix: Azure Machine Learning
description: CLI (v2) 计算 YAML 架构的参考文档。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: 34eaff89fd715c112feb2f89b4e3b6c355e9ac86
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778529"
---
# <a name="cli-v2-compute-yaml-schema"></a>CLI (v2) 计算 YAML 架构

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="schema"></a>架构

可在 https://azuremlschemas.azureedge.net/latest/compute.schema.json 找到源 JSON 架构。 为方便起见，按以下 JSON 和 YAML 格式提供架构。

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/azureml-examples-main/cli/.schemas/jsons/latest/compute.schema.json":::

# <a name="yaml"></a>[YAML](#tab/yaml)

:::code language="yaml" source="~/azureml-examples-main/cli/.schemas/yamls/latest/compute.schema.yml":::

---

## <a name="remarks"></a>备注

`az ml compute` 命令可用于管理 Azure 机器学习计算。

## <a name="next-steps"></a>后续步骤

- [安装并使用 CLI (v2)](how-to-configure-cli.md)
